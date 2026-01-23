## 1. 新增 `appsettings.json` 檔案

在 Console 專案根目錄新增 `appsettings.json`，內容範例如下：
```json
{
  "OpenWeatherDataUrl": "https://api.openweathermap.org/data/2.5/"
}
```
---

## 2. 在 Console App 中設定 `IConfiguration`

你需要安裝 NuGet 套件：

- `Microsoft.Extensions.Configuration`
- `Microsoft.Extensions.Configuration.Json`

然後在 `Program.cs` 裡建立 `IConfiguration` 實例:
```C#
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using System;
using System.IO;

class Program
{
    static void Main(string[] args)
    {
        // 建立 ConfigurationBuilder，讀取 appsettings.json
        var configuration = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory()) // 設定基準路徑
            .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
            .Build();
    }
}
```


## 3. 透過DI注入就可以使用`IConfiguration`讀取參數了
```C#
public static IServiceCollection AddInfrastructureServices(this IServiceCollection services, IConfiguration configuration)
{
    services.AddHttpClient("OpenWeatherDataUrl", httpClient =>
    {
        httpClient.BaseAddress = new Uri(configuration["OpenWeatherDataUrl"] ?? string.Empty);
        httpClient.Timeout = TimeSpan.FromSeconds(30);
    });

    return services;
}
```