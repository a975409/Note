
後端：
```C#
 public class t_HighwayAccidentController : Controller
 {
	 public async Task<IActionResult> GetReviewReportAsync(int AccidentId)
	 {
	     string contentType = "application/pdf";
	     string extension = ".pdf";
	
	     try
	     {
	         var result = await _service.GetReviewReportAsync(AccidentId);
	         var ms = _service.ReviewReportConvertToPdf(result);
			
			 //檔案內容以 File 回傳
	         return File(ms, contentType, "重大事件檢討報告" + extension);
	     }
	     catch (Exception ex)
	     {
		     //不要用 204 來表示錯誤**，改用 4xx 或 5xx 狀態碼並回傳錯誤訊息
	         return BadRequest(ex.Message);
	     }
	 }
 }
```

前端：
```javascript
function printPDF(event) {
    let id = event.dataset.id;
    var xhr = new XMLHttpRequest();
    var url = `/t_HighwayAccident/GetReviewReport?AccidentId=${id}`;
    xhr.responseType = "blob"; //需設定這段
    xhr.open("GET", url, true);
    
    xhr.onload = function () {
        if (this.status===200) {
            //新分頁
            var newWindow = window.open("");
            // 將PDF文件顯示在新分頁
            newWindow.document.write("<embed width='100%' height='100%' src='" + URL.createObjectURL(this.response) + "'></embed>");
        }else{
        
            //因為responseType = "blob"，所以要用 FileReader 將 Blob 轉成文字，
            //才能取得錯誤訊息並顯示
            
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
    };

    xhr.send();
};
```

