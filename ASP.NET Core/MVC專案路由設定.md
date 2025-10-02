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

# 慣例路由

![[Pasted image 20241020231218.png]]
![[Pasted image 20241020231250.png]]

[可設定多個慣例路由](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/routing?view=aspnetcore-8.0#multiple-conventional-routes)
[當有兩個相同的controller、action的路由時，可標註Http動作方法做區隔](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/routing?view=aspnetcore-8.0#resolving-ambiguous-actions)

Http動作方法：
![[Pasted image 20241103154701.png]]

# 屬性路由(避免跟API搞混，建議不要用)

> `Route`只是用來自訂路由路徑，`Http Method`只是用來標記該`Action`只能採用哪種方法呼叫
> 如果要開發API，請參考[[Web API 專案路由設定]]，並且要另外新增一個繼承`ControllerBase`的controller
> `Program.cs`不用加上`app.MapControllers();`

### 一、Route：可針對controller、action自定義路由原則，可設定多個路由原則
```C#
public class HomeController : Controller
{
    [Route("")]
    [Route("Home")]
    [Route("Home/Index")]
    [Route("Home/Index/{id?}")]
    [Route("~/Index")]
    public IActionResult Index(int? id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }

    [Route("Home/About")]
    [Route("Home/About/{id?}")]
    public IActionResult About(int? id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }
}
```
`HomeController.Index` 動作會針對任何 URL 路徑 `/`、`/Home`、`/Home/Index` 、 `/Home/Index/3` 或 `~/Index`執行。

使用屬性路由時，除非使用[語彙基元取代](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/routing?view=aspnetcore-8.0#routing-token-replacement-templates-ref-label)，否則控制器和動作名稱不會發揮比對動作的作用。 下列範例會比對與上一個範例相同的 URL，並以 `action` 和 `controller` 的語彙基元取代：
```C#
public class HomeController : Controller
{
    [Route("")]
    [Route("Home")]
    [Route("[controller]/[action]")]
    public IActionResult Index()
    {
        return ControllerContext.MyDisplayRouteInfo();
    }

    [Route("[controller]/[action]")]
    public IActionResult About()
    {
        return ControllerContext.MyDisplayRouteInfo();
    }
}
```

### 二、HTTP Method：可針對controller、action標記對應的HTTP Method
[Http 指令動詞範本](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/routing?view=aspnetcore-8.0#http-verb-templates)

使用範例如下：
```C#
public class Test2Controller : Controller
{
    [HttpGet]   // GET /test2
    public IActionResult ListProducts()
    {
        return ControllerContext.MyDisplayRouteInfo();
    }

    [HttpGet("{id}")]   // GET /test2/xyz
    public IActionResult GetProduct(string id)
    {
       return ControllerContext.MyDisplayRouteInfo(id);
    }

    [HttpGet("int/{id:int}")] // GET /test2/int/3
    public IActionResult GetIntProduct(int id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }

    [HttpGet("int2/{id}")]  // GET /test2/int2/3
    public IActionResult GetInt2Product(int id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }
}
```

# 設定`Area`路徑：
Program.cs：
```C#
app.MapControllerRoute(
	name: "RoutineWork",
	pattern: "{area:xxx}/{controller=e_xxx}/{action=Index}/{id?}");
```

controller指定`Area`：
```C#
[Area("RoutineWork")]
public class e_ShouldOpenController : Controller
```

