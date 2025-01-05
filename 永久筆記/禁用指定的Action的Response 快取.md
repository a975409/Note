```csharp
[ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
public IActionResult Index()
{
    return View();
}
```

Duration = 0⇒max-age=0

Location = ResponseCacheLocation.None⇒指定瀏覽器不要快取資源

NoStore = true⇒告知用戶端資源不會被快取

上面的程式碼會在回應中包含下列標頭：

```csharp
Cache-Control: no-store,no-cache
Pragma: no-cache
```

參考資料：

[ASP.NET Core 中的回應快取 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/performance/caching/response?view=aspnetcore-8.0#nostore-and-locationnone)