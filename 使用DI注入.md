## 1. 需安裝套件

```
Install-Package Microsoft.Extensions.DependencyInjection
```

## 2. 建立 DependencyInjectionConfig.cs

```C#
internal class DependencyInjectionConfig
{
    public static IServiceProvider ServiceProvider { get; set; }

    public static IServiceProvider Register()
    {
        var services = new ServiceCollection();
        ConfigureServices(services);
        ServiceProvider = services.BuildServiceProvider();
        return ServiceProvider;
    }

    /// <summary>
    /// 使用 MS DI 註冊服務
    /// </summary>
    private static IServiceCollection ConfigureServices(IServiceCollection services)
    {
        return services.AddSingleton<OpenWeatherDataAPIsRepository>();
    }
}
```

## 3. Program.cs 呼叫Register()，即可使用DI注入功能

使用方式：
```C#
internal class Program
{
    static void Main(string[] args)
    {
        var serviceProvider = DependencyInjectionConfig.Register() as ServiceProvider;

        using (serviceProvider)
        {
	        //取得服務
            var repository = serviceProvider.GetService(typeof(OpenWeatherDataAPIsRepository)) as OpenWeatherDataAPIsRepository;
        }
    }
}
```

參考文件：
https://www.dotblogs.com.tw/yc421206/2020/10/29/standard_di_container_for_microsoft_extensions_dependencyInjection