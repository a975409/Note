#Middleware #ASP_NET_Core 
> Middleware 簡單來說，就是一段處理 Http Request 到 Http Response 的程式

> 而 ASP.NET Core 透過許多 Middleware 組成的 Pipeline 取代原本 Http Modules 以及 HTTP Handlers 來處理所有Http 的 Request 及 Response

每個 Middleware 可以選擇：
 - 是否將Request傳送到下一個 Middleware
 - 下一個 Middleware 的前後執行工作(直接 Response 回用戶端)

而在 Request 中經過的`Middleware`，會在 Response 的時候會以相反順序執行`Middleware`，然後每個`Middleware`會從執行到的`next()`開始往下繼續執行，如圖 3-1 所示：
![[Pasted image 20240917194718.png]]

> 當`http request`跑到最後一段`Run()`，才會繼續往下跑`Filter`

以下是 Middleware 的擴充方法：
[[Use]]
[[Run]]
[[Map]]
[[Middleware 3種定義格式]]
[ASP.NET Core 中介軟體 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/middleware/?view=aspnetcore-6.0#built-in-middleware-1)
[[自訂Middleware處理例外狀況]]