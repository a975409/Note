[建立 HttpClient 物件](https://learn.microsoft.com/zh-tw/dotnet/fundamentals/networking/http/httpclient#create-an-httpclient-object)
[發出HTTP請求](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/http-requests?view=aspnetcore-9.0&preserve-view=true#basic-usage)

Program.cs
```C#
builder.Services.AddHttpClient("APIUrl", httpClient =>
{
	httpClient.BaseAddress = new Uri(Configuration["APIUrl"]);
	httpClient.Timeout = TimeSpan.FromSeconds(30);
});
```

需引用：
```C#
using System.Net.Http;

private readonly IHttpClientFactory _httpClientFactory;

public WebAPIService(IHttpClientFactory httpClientFactory)
{
	_httpClientFactory = httpClientFactory;
}
```

傳遞資料的方式：
1. HttpPost，資料格式：FormData(multipart/form-data)
```C#
private async Task<HttpResponseMessage> WebAPIAsync(string url, Dictionary<string, string> formValues, IEnumerable<IFormFile> files = null)
{
	var httpClient = _httpClientFactory.CreateClient("APIUrl");

	//MultipartFormDataContent 預設的 Content-Type 為 multipart/form-data 
	using var formData = new MultipartFormDataContent();

	// 添加表單欄位
	if (formValues != null)
	{
		foreach (var kvp in formValues)
			formData.Add(new StringContent(kvp.Value, Encoding.UTF8), kvp.Key);
	}

	// 添加檔案
	if (files != null)
	{
		foreach (var file in files)
		{
			if (file.Length > 0)
			{
				var fileContent = new StreamContent(file.OpenReadStream());
				fileContent.Headers.ContentType = new MediaTypeHeaderValue(file.ContentType);

				// 使用檔案名稱作為表單欄位名稱，也可以自訂
				formData.Add(fileContent, "files", file.FileName);
			}
		}
	}

	//POST
	var httpResponseMessage = await httpClient.PostAsync(url, formData);
	return httpResponseMessage.EnsureSuccessStatusCode();
}

//主程式
public async Task<FileAPIData> CallWebAPIReturnFileAPIDataAsync(Dictionary<string, string> formValues, string url = "", IEnumerable<IFormFile> files = null)
{
	var response = await WebAPIAsync(url, formValues, files);

	if (response.IsSuccessStatusCode)
	{
		//將接收到JSON格式的資料，轉成指定的物件
		return await response.Content.ReadFromJsonAsync<FileAPIData>();
	}

	return new FileAPIData
	{
		resultcode = 0,

		//將接收到的資料轉成字串
		resultdesc = await response.Content.ReadAsStringAsync(),
		fileList = new List<FileClass>()
	};
}
```

2. HttpGet
```C#
public async Task<IActionResult> ExportPDF(string url)
{
    var httpClient = _httpClientFactory.CreateClient("Report");

    try
    {
        var httpResponseMessage = await httpClient.GetAsync(url);
        string fileDownloadName = httpResponseMessage.Content.Headers.ContentDisposition?.FileName;

        if (httpResponseMessage.IsSuccessStatusCode)
        {
	        //將接收到的資料轉成二進位檔
            var stream = await httpResponseMessage.Content.ReadAsStreamAsync();
            return File(stream, httpResponseMessage.Content.Headers.ContentType.MediaType, fileDownloadName);
        }
        else
        {
	        //將接收到的資料轉成字串
            string strJson = await httpResponseMessage.Content.ReadAsStringAsync();
            return BadRequest(strJson);
        }
    }
    catch (Exception ex)
    {
        return StatusCode(500);
    }
}
```

3. 當回傳的資料為二進位檔(`application/octet-stream`)，則需設定檔名(包含正確的附檔名)，瀏覽器才能正確解析下載的檔案
> 通常伺服器會回傳`response header` 的 `Content-Disposition`，裡面會記錄完整檔案名稱

```C#
[Route("Api/OrdFile/{ordId}/{fileId}/{fileCode}")]
public async Task<IActionResult> DownloadOrdFileAsync(int ordId, int fileId, string fileCode)
{
	string dwFileUrl = $"?task=getRealOrdsFile&ordId={ordId}&fileId={fileId}&fileCode={fileCode}&emp_id={_authHepler.GetCurrentUserDto().Id}&emp_no={_authHepler.GetCurrentUserDto().empNo}";

	try
	{
		var httpClient = _httpClientFactory.CreateClient("APIUrl");
		var httpResponseMessage = await httpClient.GetAsync(dwFileUrl);

		if (httpResponseMessage.IsSuccessStatusCode)
		{
			var stream = await httpResponseMessage.Content.ReadAsStreamAsync();

			// 取得檔案名稱 (如果原始回應中有提供)
			string fileName = ExtractFileName(httpResponseMessage);
			
			if (string.IsNullOrEmpty(fileName))
				fileName = $"file_{fileCode}";

			return File(stream, httpResponseMessage.Content.Headers.ContentType.MediaType, fileName);
		}
		else
		{
			string strJson = await httpResponseMessage.Content.ReadAsStringAsync();
			return BadRequest(strJson);
		}
	}
	catch (Exception ex)
	{
		return StatusCode(500);
	}
}

/// <summary>
/// 透過response header的Content-Disposition 取得檔案名稱 (如果原始回應中有提供)
/// </summary>
/// <param name="response"></param>
/// <returns></returns>
public string ExtractFileName(HttpResponseMessage response)
{
	if (!response.Content.Headers.TryGetValues("Content-Disposition", out var values))
	{
		return string.Empty;
	}

	var contentDisposition = values.FirstOrDefault();
	if (string.IsNullOrEmpty(contentDisposition))
	{
		return string.Empty;
	}

	string fileName = string.Empty;

	// 1. 嘗試 RFC 5987 編碼格式 (filename*=UTF-8''example%20file.txt)
	var rfc5987Match = System.Text.RegularExpressions.Regex.Match(
		contentDisposition,
		@"filename\*=(?<charset>[^']*)'(?<language>[^']*)'(?<value>[^;]*)"
	);

	if (rfc5987Match.Success)
	{
		string charset = rfc5987Match.Groups["charset"].Value;
		string value = rfc5987Match.Groups["value"].Value;

		// URL 解碼
		fileName = System.Net.WebUtility.UrlDecode(value);

		// 如果有指定字符集，確保使用正確的編碼
		if (!string.IsNullOrEmpty(charset))
		{
			try
			{
				byte[] bytes = System.Text.Encoding.GetEncoding("ISO-8859-1").GetBytes(fileName);
				fileName = System.Text.Encoding.GetEncoding(charset).GetString(bytes);
			}
			catch
			{
				// 忽略編碼錯誤，保留 URL 解碼後的值
			}
		}

		return fileName;
	}

	// 2. 嘗試標準 "filename" 參數
	var standardMatch = System.Text.RegularExpressions.Regex.Match(
		contentDisposition,
		@"filename=""?(?<value>[^""]+)""?"
	);

	if (standardMatch.Success)
	{
		fileName = standardMatch.Groups["value"].Value;

		// 嘗試處理可能的編碼問題
		try
		{
			// 嘗試 UTF-8
			byte[] bytes = System.Text.Encoding.GetEncoding("ISO-8859-1").GetBytes(fileName);
			string utf8FileName = System.Text.Encoding.UTF8.GetString(bytes);

			// 檢查轉換後的字符串是否包含亂碼
			// 一個簡單的啟發式方法：檢查是否包含常見的中文字符範圍
			bool containsChineseChars = utf8FileName.Any(c => c >= 0x4E00 && c <= 0x9FFF);

			if (containsChineseChars)
			{
				fileName = utf8FileName;
			}
		}
		catch
		{
			// 忽略編碼錯誤，保留原始值
		}

		return fileName;
	}

	// 3. 如果都失敗，返回 null
	return string.Empty;
}
```
## 補充說明

在 HTTP 請求中，標頭有不同的類型和用途：
1. **請求標頭 (Request Headers)**：與整個請求相關，例如 `Accept`、`Authorization`、`User-Agent` 等。這些應該設定在 `DefaultRequestHeaders` 中。
    
2. **內容標頭 (Content Headers)**：與請求/回應的內容相關，例如 `Content-Type`、`Content-Length` 等。這些應該設定在 `HttpContent` 的 `Headers` 屬性中。
    
3. **回應標頭 (Response Headers)**：與回應相關，例如 `Server`、`Set-Cookie` 等。

所以`Content-Type`不能設定在以下程式碼內，因為`Content-Type`是內容標頭，而不是請求標頭：
```C#
builder.Services.AddHttpClient("APIUrl", httpClient =>
{
    // 這段不能設定ContentType
    httpClient.DefaultRequestHeaders.Add(HeaderNames.ContentType, "application/x-www-form-urlencoded");
});
```

[Http內容標頭 ContentType列表](https://www.runoob.com/http/http-content-type.html)
