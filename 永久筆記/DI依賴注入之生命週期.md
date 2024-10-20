
生命週期：
```C#
public void ConfigureServices(IServiceCollection services) 
{ 
	//每個Request都是同一個新的實例 
	services.AddScoped<TodoListFunction>(); 
	//程式執行期間只會有一個實例 
	services.AddTransient<TodoListFunction>(); 
	//每次注入時，都是一個新的實例 
	services.AddSingleton<TodoListFunction>(); 
}
```

1. Singleton：當應用程式啟動時，會在第一次注入時或在註冊進DI容器時建立物件
2. Scoped：每個 HTTP Request 只會共用一個物件，並在第一次注入時建立物件
3. Transient：每次注入時都會建立物件

[[在Controller內使用DI有以下三種作法]]
[[在 Controller或View 以外使用 HttpContext]]

[ASP.NET Core 檢視中的相依性插入 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/views/dependency-injection?view=aspnetcore-8.0#service-injection)
[筆記 - 不可不知的 ASP.NET Core 依賴注入-黑暗執行緒 (darkthread.net)](https://blog.darkthread.net/blog/aspnet-core-di-notes/)
