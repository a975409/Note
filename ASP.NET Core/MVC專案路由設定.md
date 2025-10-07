Program.cs：
```C#
public static void Main(string[] args)
{
    var builder = WebApplication.CreateBuilder(args);

    // MVC專案
    builder.Services.AddControllersWithViews();
    var app = builder.Build();

    // Configure the HTTP request pipeline.
    if (!app.Environment.IsDevelopment())
    {
        app.UseExceptionHandler("/Home/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthorization();

	//設定方式一：慣例路由
    app.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");

	//設定方式二：慣例路由
	app.MapDefaultControllerRoute();
    app.Run();
}
```

- `app.UseRouting()` 啟用了路由中介軟體，負責解析所有路由請求。
- `app.MapControllerRoute(...)` 註冊了傳統的「約定式路由」（conventional routing），用於匹配沒有屬性路由的 Controller Action。
- **同時，ASP.NET Core 會自動支援屬性路由（Attribute Routing）**，只要 Controller 或 Action 有標註 `[Route]`、`[HttpGet]`、`[HttpPost]` 等屬性路由標記，系統就會根據這些標記來匹配路由。

# 慣例路由(傳統的「約定式路由」)

![[Pasted image 20241020231218.png]]
![[Pasted image 20241020231250.png]]

[可設定多個慣例路由](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/routing?view=aspnetcore-8.0#multiple-conventional-routes)
[當有兩個相同的controller、action的路由時，可標註Http動作方法做區隔](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/routing?view=aspnetcore-8.0#resolving-ambiguous-actions)

可另外標記以下Http動作方法，來標示該Action只能接受哪種Http動作方法的請求：
![[Pasted image 20241103154701.png]]

>不能在`Http動作方法`內加上網址路徑，因為已交由`慣例路由(傳統的「約定式路由」)`設定路由了，所以就算在這裡面設定路徑也是無效的

# 屬性路由

用於API的開發，需要另外開一隻繼承`ControllerBase`的controller開發API，請參考[[Web API 專案路由設定]]

> `Program.cs`無須調整，沿用MVC專案的路由設定即可
> Controller上方需標記為[ApiController, Route("[api/controller]")]，且所有Action都要設定屬性路由(`Route`或`HttpMethod`)