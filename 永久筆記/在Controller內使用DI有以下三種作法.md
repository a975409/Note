[ASP.NET Core 控制器的相依性插入 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/dependency-injection?view=aspnetcore-8.0)

```C#
//在Controller內建構給所有action使用private readonly IDataService _dataService;
public HomeController(IDataService dataService)
{
    _dataService = dataService;
}

//在action的arg使用[FromServices]
public IActionResult Index([FromServices] IDataService dataService2)

//從RequestServices拿
IDataService dataService3 = HttpContext.RequestServices.GetService(typeof(IDataService)) as IDataService;
```
