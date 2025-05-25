
# 慣例路由
`App_Start\RouteConfig.cs`
```C#
public class RouteConfig
{
	public static void RegisterRoutes(RouteCollection routes)
	{
		routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

		//慣例路由
		routes.MapRoute(
			name: "Default",
			url: "{controller}/{action}/{id}",
			defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
		);
	}
}
```

> 可設定多個慣例路由


# 屬性路由

### 一、Route：可針對controller、action自定義路由原則，可設定多個路由原則
https://kevintsengtw.blogspot.com/2013/12/aspnet-mvc-attribute-routing.html

### 二、HTTP Method：可針對controller、action標記對應的HTTP Method
Http動作方法：
[HttpGetAttribute](https://learn.microsoft.com/zh-tw/dotnet/api/system.web.mvc.httpgetattribute?view=aspnet-mvc-5.2)：表示用於限制動作方法只處理 HTTP GET 要求的屬性。
[HttpPostAttribute](https://learn.microsoft.com/zh-tw/dotnet/api/system.web.mvc.httppostattribute?view=aspnet-mvc-5.2)：表示用於限制動作方法只處理 HTTP POST 要求的屬性。
[HttpPutAttribute](https://learn.microsoft.com/zh-tw/dotnet/api/system.web.mvc.httpputattribute?view=aspnet-mvc-5.2)：表示用於限制動作方法只處理 HTTP PUT 要求的屬性。
[HttpDeleteAttribute](https://learn.microsoft.com/zh-tw/dotnet/api/system.web.mvc.httpdeleteattribute?view=aspnet-mvc-5.2)：表示用於限制動作方法只處理 HTTP DELETE 要求的屬性。
[HttpHeadAttribute](https://learn.microsoft.com/zh-tw/dotnet/api/system.web.mvc.httpheadattribute?view=aspnet-mvc-5.2)：指定 HTTP 要求必須是 HTTP HEAD 方法。
[HttpPatchAttribute](https://learn.microsoft.com/zh-tw/dotnet/api/system.web.mvc.httppatchattribute?view=aspnet-mvc-5.2)：表示用於限制動作方法只處理 HTTP PATCH 要求的屬性。
[HttpOptionsAttribute](https://learn.microsoft.com/zh-tw/dotnet/api/system.web.mvc.httpoptionsattribute?view=aspnet-mvc-5.2)：表示用於限制動作方法只處理 HTTP OPTIONS 要求的屬性。

> 當有兩個相同的controller、action的路由時，可標註Http動作方法做區隔
> 並且只能用來標記該`action`要用哪個HTTP動作方法，無法額外設定路由，如下：
```C#
[HttpGet]
public ActionResult Details(int? id)
```

>ControllerBase有標記Controller標籤，ApiController標籤則繼承Controller標籤，Controller繼承ControllerBase，所以在MVC專案開發API時，無須在Controller上方另外標記ApiController，並繼承Controller就好