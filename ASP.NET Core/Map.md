#Middleware #ASP_NET_Core 
```C#
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

//localhost:1234/map1
app.Map("/map1", HandleMapTest1);

//localhost:1234/map2
app.Map("/map2", HandleMapTest2);

//localhost:1234
//localhost:1234/map3 =>不存在的分支名稱則會跑這裡
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello from non-Map delegate.");
});

//最後一定要有這段就對了
app.Run();

static void HandleMapTest1(IApplicationBuilder app)
{
    app.Run(async context =>
    {
        await context.Response.WriteAsync("Map Test 1");
    });
}

static void HandleMapTest2(IApplicationBuilder app)
{
    app.Run(async context =>
    {
        await context.Response.WriteAsync("Map Test 2");
    });
}
```

下表說明使用上述程式碼後，來自 `http://localhost:1234` 的要求及回應。

| Request             | 回應                           |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

使用 `Map` 時，會將相符的路徑線段從 `HttpRequest.Path` 移除，並附加至每個要求的 `HttpRequest.PathBase`

[[Map 支援巢狀項目]]
[[MapWhen 會依據指定述詞的結果將要求管線分支]]
[[Map 也可以一次比對多個線段]]
[ASP.NET Core 中介軟體 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/middleware/?view=aspnetcore-6.0#branch-the-middleware-pipeline-1)
