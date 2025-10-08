
後端(下載xlsx檔案)：
```C#
public class t_HighwayAccidentController : Controller
{
	public async Task<IActionResult> GetReviewReportAsync(int AccidentId)
	{
	    string contentType = "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
	    string extension = ".xlsx";
	    string fileName = $"重大事件檢討報告_{DateTime.Now.ToAllDateTime()}{extension}";
	
	    try
	    {
	        var result = await _service.GetReviewReportAsync(AccidentId);
	        var ms = _service.ReviewReportConvertToExcel(result);
	
	        // 設定 Content-Disposition header 支援 RFC 5987 編碼
	        Response.Headers.Add("Content-Disposition",
	            $"attachment; filename=\"{Uri.EscapeDataString(fileName)}\"; filename*=UTF-8''{Uri.EscapeDataString(fileName)}");
	
	        return File(ms, contentType);
	    }
	    catch (Exception ex)
	    {
	        return BadRequest(ex.Message);
	    }
	}
}
```

前端：
```html
<td style="cursor:pointer;" onclick="printPDF(this)" data-id="@item.HA_ID">
    💾
</td>
```

以下原始碼有使用到[[Loading遮罩套件]]，當AJAX正在等待後端回應時會顯示載入中的畫面：
```javascript
function printPDF(event) {
    let id = event.dataset.id;
    var xhr = new XMLHttpRequest();
    var url = `/api/t_HighwayAccidentApi/GetReviewReport/${id}`;
    xhr.responseType = "blob";

	//開始執行時
    xhr.addEventListener('loadstart',function(){
        $.LoadingOverlay("show"); //呼叫loading遮罩
    },false);

	//執行完畢(不管成功或失敗)
    xhr.addEventListener('loadend',function(){
       $.LoadingOverlay("hide"); //隱藏loading遮罩
    },false);

    xhr.addEventListener('load',function(){
        if (this.status===200) {

            // 取得 Content-Disposition header
            var contentDisposition = xhr.getResponseHeader("Content-Disposition");
            var fileName = "重大事件檢討報告.xlsx"; // 預設檔名

            if (contentDisposition) {
                // 解析 filename
                var fileNameMatch = contentDisposition.match(/filename\*=UTF-8''(.+)|filename="(.+)"|filename=(.+)/);
                if (fileNameMatch) {
                    // filename*=UTF-8''xxx 或 filename="xxx" 或 filename=xxx
                    fileName = decodeURIComponent(fileNameMatch[1] || fileNameMatch[2] || fileNameMatch[3]);
                }
            }

            var blobUrl= URL.createObjectURL(this.response);

            // 建立隱藏的 <a> 標籤用來下載
            var a = document.createElement('a');
            a.href = blobUrl;
                        a.download = fileName;

            // 將 <a> 標籤加入文件並觸發點擊事件
            document.body.appendChild(a);
            a.click();

            // 移除 <a> 標籤並釋放 blob URL
            document.body.removeChild(a);
            URL.revokeObjectURL(blobUrl);
        }else{

            // 嘗試讀取錯誤訊息
            var reader = new FileReader();
            reader.onload = function() {
                let errorMessage = reader.result || "發生錯誤，無法取得錯誤訊息";
                Swal.fire({
                    icon: "error",
                    title: "錯誤",
                    text: errorMessage,
                });
            };
            reader.readAsText(this.response);
        }
    },false);

    xhr.open("GET", url, true);
    xhr.send();
};
```

