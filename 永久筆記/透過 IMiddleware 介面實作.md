#Middleware #ASP_NET_Core 

>此為強型別的 Middleware，在對請求進行處理時，由依賴注入容器即時提供，所以需要透過依賴注入的方式註冊服務，並可設定不同的生命週期

GreetingMiddleware
```C#
public class GreetingMiddleware : IMiddleware
{
    private readonly IGreeter _greeter;

    public GreetingMiddleware(IGreeter greeter)
    {
        _greeter = greeter;
    }

    public Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        return context.Response.WriteAsync(_greeter.Greet(DateTimeOffset.Now));
    }
}
```

IGreeter
```C#
		public interface IGreeter
    {
        string Greet(DateTimeOffset time);
    }

    public class Greeter : IGreeter
    {
        public string Greet(DateTimeOffset time)
        {
            if (time.Hour >= 5 && time.Hour < 12)
                return "Good morning";
            else if (time.Hour >= 12 && time.Hour < 17)
                return "Good afternoon";
            else
                return "Good evening";
        }
    }
```

Program
```C#
public static void Main(string[] args)
{
    var builder = WebApplication.CreateBuilder(args);

    //註冊服務
    builder.Services.AddSingleton<IGreeter, Greeter>()
	//此為強型別的 Middleware，需要透過依賴注入的方式註冊服務
                    .AddSingleton<GreetingMiddleware>();
    
    var app= builder.Build();

    //註冊 middleware
    app.UseMiddleware<GreetingMiddleware>();
    app.Run();
}
```
