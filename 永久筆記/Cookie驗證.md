[1.新增cookie驗證](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#add-cookie-authentication)
可在 [AddCookie](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.extensions.dependencyinjection.cookieextensions.addcookie) 方法中設定 [CookieAuthenticationOptions](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)：
```C#
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme) .AddCookie(options => { options.ExpireTimeSpan = TimeSpan.FromMinutes(20); options.SlidingExpiration = true; options.AccessDeniedPath = "/Forbidden/"; });
```

[2.建立cookie驗證(登入)](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#create-an-authentication-cookie)
[3.登出](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#sign-out)

[[(Cookie 驗證)ValidatePrincipal 事件]]
[持續性cookie(應用在登入時勾選「記住我」)](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#persistent-cookies)
[設定cookie 到期日](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#absolute-cookie-expiration)