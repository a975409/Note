[回應後端變更](https://learn.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-7.0#react-to-back-end-changes)

一旦建立 cookie，cookie 就成為 identity 的單一來源。 如果後端系統中已停用使用者帳戶：

- 應用程式的 cookie 驗證系統會根據驗證 cookie 繼續處理要求。
- 只要驗證 cookie 有效，使用者仍會登入應用程式。

[ValidatePrincipal](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) 事件可用來攔截並覆寫 cookieidentity 的驗證。 在每個要求上驗證 cookie，可降低撤銷使用者存取應用程式的風險。

cookie 驗證的其中一種方法，是根據追蹤使用者資料庫的變更時機。 如果自使用者的 cookie 發出後資料庫沒有發生變更，且使用者的 cookie 仍然有效，則無需重新驗證使用者。

1.為了在資料庫根據 `LastChanged` 值變更時使 cookie 失效，請使用包含資料庫中目前 `LastChanged` 值的 `LastChanged` 宣告建立 cookie：
```C#
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims,
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

2.實作[ValidatePrincipal](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) 事件：
```C#
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

		//如果LastChanged值與資料庫比對發現有變更，就登出重新進行驗證
        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

3.註冊事件Program.cs：
```C#
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

builder.Services.AddScoped<CustomCookieAuthenticationEvents>();

```



