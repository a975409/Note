在 `Program.cs` 新增以下程式碼：

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddRazorPages();

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();//新增這段
app.UseStaticFiles();

app.UseRouting();

app.UseAuthorization();

app.MapRazorPages();

app.Run();
```

在 `appsettings.json` 中新增如下：

```json
{
  "https_port": 443,//新增這段
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

- 重新導向用戶端所在的安全連接埠 (通常在生產環境中為 443，在開發環境中為 5001)。
- 不安全的連接埠 (通常為在生產環境中為 80，在開發環境中為 5000)。

```csharp
using System.Net;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddRazorPages();

builder.Services.AddHsts(options =>
{
    options.Preload = true;
    options.IncludeSubDomains = true;
    options.MaxAge = TimeSpan.FromDays(60);
    options.ExcludedHosts.Add("example.com");
    options.ExcludedHosts.Add("www.example.com");
});

//新增以下這段
if (!builder.Environment.IsDevelopment())
{
    builder.Services.AddHttpsRedirection(options =>
    {
        options.RedirectStatusCode = (int)HttpStatusCode.PermanentRedirect;//永久重新導向
        options.HttpsPort = 443;
    });
}
else
{
		builder.Services.AddHttpsRedirection(options =>
		{
		    options.RedirectStatusCode = (int)HttpStatusCode.TemporaryRedirect;//臨時重新導向
		    options.HttpsPort = 5001;
		});
}

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthorization();

app.MapRazorPages();

app.Run();
```

[[修復過時的TLS版本]]
[[設定HSTS標頭]]

參考資料：
[在 ASP.NET Core 中強制執行 HTTPS | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/security/enforcing-ssl?view=aspnetcore-8.0&tabs=visual-studio%2Clinux-ubuntu#require-https)
