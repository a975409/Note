#Middleware #ASP_NET_Core 
```C#
using System.Globalization;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.Use(async (context, next) =>
{
    var cultureQuery = context.Request.Query["culture"];
    if (!string.IsNullOrWhiteSpace(cultureQuery))
    {
        var culture = new CultureInfo(cultureQuery);

        CultureInfo.CurrentCulture = culture;
        CultureInfo.CurrentUICulture = culture;
    }

	//透過呼叫next()管道中的下一個Middleware。
    await next(context);
});

app.Run(async (context) =>
{
    await context.Response.WriteAsync(
        $"CurrentCulture.DisplayName: {CultureInfo.CurrentCulture.DisplayName}");
});

//最後一定要有這段就對了
app.Run();
```

`Use` 擴充功能提供兩種多載：

- 一是採用 [HttpContext](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.http.httpcontext) 和 `Func<Task>`。 在沒有任何參數的情況下叫用 `Func<Task>`，就是所謂的請求管線短路。
```C#
await next.Invoke();
```

- 另一項則是採用 `HttpContext` 與 [RequestDelegate](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.http.requestdelegate)。 透過傳遞 `HttpContext` 來叫用 `RequestDelegate`。
```C#
await next(context);
```

[[建立擴充方法]]
[[記錄http request、response 擴充方法範例]]
[[引用DI注入服務]]
[[UseWhen 也會依據指定述詞的結果將要求管線分支]]
[撰寫自訂的 ASP.NET Core 中介軟體 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/middleware/write?view=aspnetcore-6.0)
