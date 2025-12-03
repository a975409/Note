[如何實作沒有 ASP.NET Core Identity 的 Cookie-based 身分驗證機制 | The Will Will Web](https://blog.miniasp.com/post/2019/12/25/asp-net-core-3-cookie-based-authentication)


[1.新增cookie驗證](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#add-cookie-authentication)
可在 [AddCookie](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.extensions.dependencyinjection.cookieextensions.addcookie) 方法中設定 [CookieAuthenticationOptions](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)：
```C#
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme).AddCookie(options => { 
options.ExpireTimeSpan = TimeSpan.FromMinutes(20); 
options.SlidingExpiration = true; 
options.AccessDeniedPath = "/Forbidden/"; });
```

[2.建立cookie驗證(登入)](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#create-an-authentication-cookie)
[3.登出](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#sign-out)

[[(Cookie 驗證)ValidatePrincipal 事件]]
[持續性cookie(應用在登入時勾選「記住我」)](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#persistent-cookies)
[設定cookie 到期日](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#absolute-cookie-expiration)

AuthenticationProperties：
```C#
var authProperties = new AuthenticationProperties
{
    //取得或設定驗證票證的簽發時間。
    IssuedUtc = DateTime.Now.ToLocalTime(),

    // 是否允許 Cookie 被刷新（延長有效期）
    //AllowRefresh = <bool>,

    // 是否持久化 Cookie，設 true 讓使用者關閉瀏覽器後仍保持登入
    IsPersistent = true,

    //Cookie 的過期時間，IsPersistent = true的狀況下才需要設定
    ExpiresUtc = DateTime.Now.ToLocalTime().AddHours(8),

    // 登入後要導向的頁面，可視需求設定
    //RedirectUri = <string>
};
```

AuthenticationProperties 有關持久化cookie的屬性設定：

| IsPersistent | ExpiresUtc 設定 | 結果                                     |
| ------------ | ------------- | -------------------------------------- |
| true         | 有設定           | 持久化 Cookie，依設定過期時間失效                   |
| true         | 沒設定           | 持久化 Cookie，使用預設過期時間（通常14天）             |
| false        | 有設定           | 仍以 `IsPersistent` 為主，通常不會持久化（但不建議這樣設定） |
| false        | 沒設定           | Session Cookie，瀏覽器關閉即失效                |
