在 `Program.cs` 新增以下程式碼：
```csharp
var builder = WebApplication.CreateBuilder(args);

Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(host =>
    {
        host.UseKestrel(options =>
        {
            options.ConfigureHttpsDefaults(https =>
            {
                https.SslProtocols = System.Security.Authentication.SslProtocols.Tls12 | System.Security.Authentication.SslProtocols.Tls13;
            });
        });
    });
```