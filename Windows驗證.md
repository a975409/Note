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
//驗證方案名稱
string cookiesOrNegotiate = "WindowsOrCookie";

//設定預設要採用的驗證方案，以及新增cookie驗證 和 Windows驗證
builder.Services.AddAuthentication(options =>
{
    options.DefaultScheme = cookiesOrNegotiate;
    options.DefaultChallengeScheme = cookiesOrNegotiate;
}).AddCookie(options =>
{
    options.LoginPath = "/Account/Login"; // Cookie 驗證登入頁面

}).AddNegotiate();

//新增驗證方案並自訂驗證規則
builder.Services.AddAuthentication()
    .AddPolicyScheme(cookiesOrNegotiate, cookiesOrNegotiate, options =>
    {
	    //該驗證方案根據 Http Request 決定採用 Windows 或 Cookie驗證
        options.ForwardDefaultSelector = context =>
        {
		    //可在此區塊內自訂要採用哪種驗證方案的規則
        
            if (context.Request.Cookies.ContainsKey(CookieAuthenticationDefaults.CookiePrefix + CookieAuthenticationDefaults.AuthenticationScheme))
            {
                return CookieAuthenticationDefaults.AuthenticationScheme;
            }

            // 這些靜態檔案/資源/Hub → 不要用 Negotiate
            var path = context.Request.Path;
            if (path.StartsWithSegments("/lib") ||
                path.StartsWithSegments("/js") ||
                path.StartsWithSegments("/.well-known") ||
                (path.Value?.EndsWith(".map", StringComparison.OrdinalIgnoreCase) ?? false))
            {
                return CookieAuthenticationDefaults.AuthenticationScheme;
            }
            
            return NegotiateDefaults.AuthenticationScheme;
        };
    });

```