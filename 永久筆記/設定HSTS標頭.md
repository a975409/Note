HSTS是另一種Web應用程式安全機制，有助於防止中間人攻擊

它允許Web伺服器發送一個特殊的HTTP回應標頭，通知支援的瀏覽器後續的通訊和資料傳輸，只能透過HTTPS完成，否則不允許連線

[ASP.NET](http://ASP.NET) Core 會使用 [UseHsts](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.builder.hstsbuilderextensions.usehsts) 擴充方法實作 HSTS。 下列程式碼會在應用程式不在`UseHsts`開發模式[時呼叫](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/environments?view=aspnetcore-8.0) ：

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddRazorPages();

//新增這段
builder.Services.AddHsts(options =>
{
    options.Preload = true;

    //將 HSTS 原則套用至主機子網域
    options.IncludeSubDomains = true;

    //max-age 參數明確設定為 60 天。 如果未設定，則預設為 30 天
    options.MaxAge = TimeSpan.FromDays(60);

    //將 example.com、localhost 新增至要排除的主機清單。
    options.ExcludedHosts.Add("example.com");
    options.ExcludedHosts.Add("www.example.com");
    options.ExcludedHosts.Add("localhost");
});

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");

    //新增這段
    // The default HSTS value is 30 days. You may want to change this for production scenarios, see <https://aka.ms/aspnetcore-hsts>.
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthorization();

app.MapRazorPages();

app.Run();
```

※`UseHsts` 不建議用於開發環境，因為 HSTS 設定可由瀏覽器高度快取。 根據預設，`UseHsts` 會排除本機回送位址。

設定後的Response Headers會多一個`Strict-Transport-Security` 標頭：

[HSTS Preload List Submission](https://hstspreload.org/)

參考資料：

[在 ASP.NET Core 中強制執行 HTTPS | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/security/enforcing-ssl?view=aspnetcore-8.0&tabs=visual-studio%2Clinux-ubuntu#require-https)