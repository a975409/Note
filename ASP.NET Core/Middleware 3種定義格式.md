#Middleware #ASP_NET_Core 
```C#
 Func<RequestDelegate,RequestDelegate>
 Func<HttpContext,RequestDelegate,Task>
 Func<HttpContext,Func<Task>,Task>
```


```C#
//Func<RequestDelegate,RequestDelegate>
public class Program
{
    public static void Main(string[] args)
    {
        var app = WebApplication.Create(args);
        IApplicationBuilder appBuilder = app;

				//註冊 middleware
        appBuilder.Use(middleware: HelloMiddleware);
        appBuilder.Use(middleware: WorldMiddleware);
        app.Run();
        
		    static RequestDelegate HelloMiddleware(RequestDelegate next) => async httpContext =>
		    {
		        await httpContext.Response.WriteAsync("Hello, ");
		        await next(httpContext);//往下一個 middleware 執行
		    };
		
		    static RequestDelegate WorldMiddleware(RequestDelegate next) => async httpContext =>
		    {
		        await httpContext.Response.WriteAsync("World!");
		    };
    }
}

/*Response
Hello, World!
*/
```


```C#
//Func<HttpContext,RequestDelegate,Task>
public class Program
{
    public static void Main(string[] args)
    {
        var app = WebApplication.Create(args);
        
				//註冊 middleware
        app.Use(middleware: HelloMiddleware);
        app.Use(middleware: WorldMiddleware);
        app.Run();
	    
		    static async Task HelloMiddleware(HttpContext httpContext, RequestDelegate next)
        {
            await httpContext.Response.WriteAsync("Hello, ");
            await next(httpContext);
        };

        static async Task WorldMiddleware(HttpContext httpContext, RequestDelegate next)
        {
            await httpContext.Response.WriteAsync("World!");
        };
    }
}

/*Response
Hello, World!
*/
```


```C#
//Func<HttpContext,Func<Task>,Task>
public class Program
{
    public static void Main(string[] args)
    {
        var app = WebApplication.Create(args);
        
				//註冊 middleware
        app.Use(middleware: HelloMiddleware);
        app.Use(middleware: WorldMiddleware);
        app.Run();
	    
		    static async Task HelloMiddleware(HttpContext httpContext, Func<Task> next)
        {
            await httpContext.Response.WriteAsync("Hello, ");
            await next();
        };

        static async Task WorldMiddleware(HttpContext httpContext, Func<Task> next)
        {
            await httpContext.Response.WriteAsync("World!");
        };
    }
}

/*Response
Hello, World!
*/
```


