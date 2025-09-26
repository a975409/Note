
Program.cs：
```C#
public static void Main(string[] args)
{
    var builder = WebApplication.CreateBuilder(args);

    // Add services to the container.
	
	//Web API 專案設定
    builder.Services.AddControllers();
    // Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
    builder.Services.AddEndpointsApiExplorer();
    builder.Services.AddSwaggerGen(options => {

        var xmlFilename = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        options.IncludeXmlComments(Path.Combine(AppContext.BaseDirectory, xmlFilename));
    });

    var app = builder.Build();

    // Configure the HTTP request pipeline.
    if (app.Environment.IsDevelopment())
    {
        app.UseSwagger();
        app.UseSwaggerUI();
    }

    app.UseHttpsRedirection();

    app.UseAuthorization();

	//Web API 專案設定，啟用屬性路由
    app.MapControllers();

    app.Run();
}
```

> MapControllers 並沒有預設路由，所以需在controller、action上方設定屬性路由

# 屬性路由

[REST API 的屬性路由](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/routing?view=aspnetcore-8.0#attribute-routing-for-rest-apis)

1. 屬性路由有兩種設定方式，分別是 Route 和 HTTP Method
2. Controller建議標記為[ApiController]，這樣才能繫結[[FromBody]]來源參數
3. 承上點，如果Controller已標記[ApiController]，那Controller上方就一定要標記為[ApiController, Route("[api/controller]")]，Action也要設定屬性路由(`Route`或`HttpMethod`)

### 一、Route：可針對controller、action自定義路由原則，可設定多個路由原則
```C#
[ApiController,Route("api/[controller]")]
public class HomeController : ControllerBase
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
[ApiController,Route("api/[controller]")]
public class HomeController : ControllerBase
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
[ApiController,Route("api/[controller]")]
public class Test2Controller : ControllerBase
{
    [HttpGet]   // GET /api/test2
    public IActionResult ListProducts()
    {
        return ControllerContext.MyDisplayRouteInfo();
    }

    [HttpGet("{id}")]   // GET /api/test2/xyz
    public IActionResult GetProduct(string id)
    {
       return ControllerContext.MyDisplayRouteInfo(id);
    }

    [HttpGet("int/{id:int}")] // GET /api/test2/int/3
    public IActionResult GetIntProduct(int id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }

    [HttpGet("int2/{id}")]  // GET /api/test2/int2/3
    public IActionResult GetInt2Product(int id)
    {
        return ControllerContext.MyDisplayRouteInfo(id);
    }
}
```

