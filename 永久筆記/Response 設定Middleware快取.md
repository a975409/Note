
參考資料：
[ASP.NET Core 中的回應快取中介軟體 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/performance/caching/middleware?view=aspnetcore-7.0#options)

回應快取中介軟體：
>1. 根據 [HTTP 快取標頭](https://developer.mozilla.org/docs/Web/HTTP/Headers/Cache-Control)啟用快取伺服器回應。 實作標準 HTTP 快取語意。 根據 Proxy 之類的 HTTP 快取標頭快取。
>2. 通常對 Razor Pages 之類的 UI 應用程式並不適用，因為瀏覽器通常會設定造成無法快取的要求標頭。 [輸出快取](https://learn.microsoft.com/zh-tw/aspnet/core/performance/caching/output?view=aspnetcore-7.0)可在 ASP.NET Core 7.0 和更新版本中取得，對 UI 應用程式有益。 使用輸出快取，設定會決定應該獨立於 HTTP 標頭快取的內容。
>3. 對於來自[符合快取條件](https://learn.microsoft.com/zh-tw/aspnet/core/performance/caching/middleware?view=aspnetcore-7.0#cfc)用戶端的公用 GET 或 HEAD API 要求可能會有幫助。

Program.cs：
```C#
builder.Services.AddResponseCaching();

app.UseResponseCaching();
```

範例應用程式會新增標頭來控制後續要求的快取(如下程式碼)：
- [快取控制](https://www.rfc-editor.org/rfc/rfc9111#field.cache-control)：快取最多 10 秒的可快取回應。
- [不同](https://www.rfc-editor.org/rfc/rfc9110#field.vary)：只有當後續要求的 [Accept-Encoding](https://www.rfc-editor.org/rfc/rfc9110#field.accept-encoding) 標頭符合原始要求時，才設定中介軟體來提供快取的回應。
```C#
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddResponseCaching();

var app = builder.Build();

app.UseHttpsRedirection();

// UseCors must be called before UseResponseCaching
//app.UseCors();

app.UseResponseCaching();

app.Use(async (context, next) =>
{
    context.Response.GetTypedHeaders().CacheControl =
        new Microsoft.Net.Http.Headers.CacheControlHeaderValue()
        {
            Public = true,
            MaxAge = TimeSpan.FromSeconds(10)
        };
    context.Response.Headers[Microsoft.Net.Http.Headers.HeaderNames.Vary] =
        new string[] { "Accept-Encoding" };

    await next();
});

app.MapGet("/", () => DateTime.Now.Millisecond);

app.Run();
```