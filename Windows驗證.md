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

新增一個 WindowsOrCookieScheme.cs：
```C#
public static class WindowsOrCookieScheme
{
    public static string CookieAuthKey = CookieAuthenticationDefaults.CookiePrefix + CookieAuthenticationDefaults.AuthenticationScheme;

    /// <summary>
    /// 新增cookie 和 Windows驗證的混合驗證方案
    /// </summary>
    /// <param name="services"></param>
    /// <returns></returns>
    public static IServiceCollection AddWindowsOrCookieScheme(this IServiceCollection services)
    {
        services.AddScoped<UsetIdentityService>();
        services.AddScoped<CookieAndWindowsAuthService>();
        services.AddHttpContextAccessor();

        //驗證方案名稱
        string cookiesOrNegotiate = "WindowsOrCookie";

        //設定預設要採用的驗證方案，以及新增cookie驗證 和 Windows驗證
        services.AddAuthentication(options =>
        {
            options.DefaultScheme = cookiesOrNegotiate;
            options.DefaultChallengeScheme = cookiesOrNegotiate;
        }).AddCookie(options =>
        {
            options.LoginPath = "/Account/Login"; // Cookie 驗證登入頁面
            options.LogoutPath = "/Account/Login"; // Cookie 驗證登入頁面
        }).AddNegotiate();

        //新增驗證方案並自訂驗證規則
        services.AddAuthentication()
            .AddPolicyScheme(cookiesOrNegotiate, cookiesOrNegotiate, options =>
            {
                //該驗證方案根據 Http Request 決定採用 Windows 或 Cookie驗證
                options.ForwardDefaultSelector = context =>
                {
                    if (context.Request.Cookies.ContainsKey(CookieAuthKey))
                    {
                        return CookieAuthenticationDefaults.AuthenticationScheme;
                    }

                    return NegotiateDefaults.AuthenticationScheme;
                };
            });

        services.AddAuthorization(options =>
        {
            options.FallbackPolicy = options.DefaultPolicy;
        });

        return services;
    }
}
```