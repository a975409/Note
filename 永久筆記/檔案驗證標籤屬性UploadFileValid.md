檔案驗證[UploadFileValid]： ※主要驗證邏輯處理為IsValid，是由繼承ValidationAttribute後override 而來的 ※fileSignature 設定檔案格式簽章驗證格式，直接照抄就好 ※IsValidFileExtension依據副檔名 & 檔案內容參考fileSignature，驗證該檔案是否符合規範，直接照抄就好

```csharp
using System.ComponentModel.DataAnnotations;
using System.Configuration;

namespace arTWander.Helpers
{
    /// <summary>
    /// 檔案大小驗證
    /// 檔案附檔名 & 內容驗證
    /// <https://docs.microsoft.com/zh-tw/aspnet/core/mvc/models/file-uploads?view=aspnetcore-6.0#validation>
    /// </summary>
    public class UploadFileValidAttribute : ValidationAttribute
    {
        //檔案格式簽章驗證範例程式：<https://docs.microsoft.com/zh-tw/azure/security/develop/threat-modeling-tool-input-validation#controls-users>
        private Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]>
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        {
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        private bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return false;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }

        //檔案大小最大上限
        private readonly long _fileSizeLimit = 2097152;

        protected override ValidationResult? IsValid(object? value, ValidationContext validationContext)
        {
            if (value == null)//代表使用者沒有要上傳檔案，不代表驗證失敗
                return ValidationResult.Success;

            if (value is IFormFile)
            {
                IFormFile? file = value as IFormFile;

                if (file == null)//代表使用者沒有要上傳檔案，不代表驗證失敗
                    return ValidationResult.Success;

                if (file.Length == 0)
                    return new ValidationResult("該檔案內容是空的");

                if (file.Length > _fileSizeLimit)
                    return new ValidationResult("檔案大小超過系統設定的大小限制");

                //讀取IFormFile，並輸出Bytes
                using (var ms = new MemoryStream())
                {
                    file.CopyTo(ms);
                    var fileName = file.FileName;
                    var fileBytes = ms.ToArray();

                    if (IsValidFileExtension(fileName, fileBytes, new byte[] { }))
                        return ValidationResult.Success;
                    else
                        return new ValidationResult("檔案內容與副檔名不符");
                }
            }
            else
            {
                return new ValidationResult("該屬性不是IFormFile");
            }
        }
    }
}

```