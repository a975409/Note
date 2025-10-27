Program.cs：
```C#
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Authentication.Negotiate;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();

//加上這段
builder.Services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
   .AddNegotiate();

builder.Services.AddAuthorization(options =>
{
    options.FallbackPolicy = options.DefaultPolicy;
});
builder.Services.AddRazorPages();

var app = builder.Build();

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

//加上這段
app.UseAuthentication();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();

```

如果要再加上多重驗證，例如cookie驗證：

Program.cs 調整如下：
```C#
var builder = WebApplication.CreateBuilder(args);

// 加入 Cookie 驗證
builder.Services.AddAuthentication(options =>
{
    // 預設使用 Policy Scheme 來決定驗證方案
    options.DefaultScheme = "WindowsOrCookie";
    options.DefaultChallengeScheme = "WindowsOrCookie";
})
.AddCookie("Cookies", options =>
{
    options.LoginPath = "/Account/Login"; // Cookie 驗證登入頁面
})
.AddNegotiate("Windows"); // Windows 驗證

// Policy Scheme 根據請求決定使用 Windows 或 Cookie
builder.Services.AddAuthentication()
    .AddPolicyScheme("WindowsOrCookie", "Windows or Cookie Authentication", options =>
    {
        options.ForwardDefaultSelector = context =>
        {
            // 這邊判斷條件，例如有 Authorization header 就用 Windows，否則用 Cookie
            var authHeader = context.Request.Headers["Authorization"].ToString();
            if (!string.IsNullOrEmpty(authHeader) && authHeader.StartsWith("Negotiate"))
            {
                return NegotiateDefaults.AuthenticationScheme;
            }
            return CookieAuthenticationDefaults.AuthenticationScheme;
        };
    });

builder.Services.AddAuthorization();

builder.Services.AddControllersWithViews();

var app = builder.Build();

app.UseStaticFiles();
app.UseRouting();

app.UseAuthentication();
app.UseAuthorization();

app.MapDefaultControllerRoute();

app.Run();

```