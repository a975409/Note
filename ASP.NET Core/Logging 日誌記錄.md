[記錄提供者](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/logging/?view=aspnetcore-7.0#logging-providers)
![[Pasted image 20250102181415.png]]![[Pasted image 20250102181431.png]]

更換 Logging provider(Program.cs)：
```C#
var builder = WebApplication.CreateBuilder(args);
builder.Logging.ClearProviders();
builder.Logging.AddConsole();

//或

var builder = WebApplication.CreateBuilder();
builder.Host.ConfigureLogging(logging =>
{
    logging.ClearProviders();
    logging.AddConsole();
});

```

[設定記錄](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/logging/?view=aspnetcore-7.0#configure-logging)
[記錄等級](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/logging/?view=aspnetcore-7.0#log-level)
[建立記錄](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/logging/?view=aspnetcore-7.0#create-logs)
[第三方的 Logging provider](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/logging/?view=aspnetcore-7.0#third-party-logging-providers)
