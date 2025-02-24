針對要進入的Controller或Action前需通過驗證，則需加上 Authorize 標籤

```csharp
[Authorize]
public class ApiBaseController : ControllerBase
{
.....
}
```

如果是針對所有Controller都需通過驗證，則需做以下設定：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews(options =>
    {
        //進入所有controller都要驗證
        options.Filters.Add(new AuthorizeFilter());
    });
		//....
}
```

因為上述全域設定而導致所有Controller都需通過驗證，但有部分Controller或Action需略過驗證：

```csharp
[AllowAnonymous]
public class ApiBaseController : ControllerBase
{
.....
}
```

如果是針對所有Controller都需略過驗證，則需做以下設定：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews(options =>
    {
        //進入所有controller都無須驗證
        options.Filters.Add(new AllowAnonymousFilter());
    });
		//....
}
```

參考資料：

[[faq]ASP .NET Core 如何在開發的情況下讓Controller不需要過Authorization (驗證) @ Alan Tsai 的學習筆記｜A Microsoft MVP, MCT, Blogger, Youtuber and ASP .NET Developer Blog](https://blog.alantsai.net/posts/2018/12/faq-asp-net-core-web-apiallow-anonymous-in-development-environment)