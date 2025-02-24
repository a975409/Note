
1. 在*.csproj專案檔裡面新增以下程式碼：
```XML
<PropertyGroup>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
  
  <!-- 關閉CS1591警告 -->
  <NoWarn>${NoWarn};1591</NoWarn>
</PropertyGroup>
```

2. Program.cs 新增以下程式碼：
```C#
using System.Reflection;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(options => {

    var xmlFilename = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
    options.IncludeXmlComments(Path.Combine(AppContext.BaseDirectory, xmlFilename));
});
```

3. 之後在controller之下的action 新增xml註解，就會出現在Swagger UI上
```C#
/// <summary>
/// 取得溫度
/// </summary>
/// <returns></returns>
[HttpGet]
[Route("GetWeatherForecast")]
public IEnumerable<WeatherForecast> Get()
{
    return Enumerable.Range(1, 5).Select(index => new WeatherForecast
    {
        Date = DateTime.Now.AddDays(index),
        TemperatureC = Random.Shared.Next(-20, 55),
        Summary = Summaries[Random.Shared.Next(Summaries.Length)]
    })
    .ToArray();
}
```

