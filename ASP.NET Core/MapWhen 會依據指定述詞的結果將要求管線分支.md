#Middleware #ASP_NET_Core 
```C#
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

//localhost:1234/?branch=main
app.MapWhen(context => context.Request.Query.ContainsKey("branch"), HandleBranch);

//localhost:1234
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello from non-Map delegate.");
});

app.Run();

static void HandleBranch(IApplicationBuilder app)
{
    app.Run(async context =>
    {
        var branchVer = context.Request.Query["branch"];
        await context.Response.WriteAsync($"Branch used = {branchVer}");
    });
}
```

下表顯示使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應：

|Request|回應|
|---|---|
|`localhost:1234`|`Hello from non-Map delegate.`|
|`localhost:1234/?branch=main`|`Branch used = main`|

