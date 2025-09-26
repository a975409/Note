```csharp
using Newtonsoft.Json;
using Newtonsoft.Json.Serialization;
public void ConfigureServices(IServiceCollection services)
{
	services.AddControllersWithViews(...).AddNewtonsoftJson(options => {
		//设置时间格式
		options.SerializerSettings.DateFormatString = "yyyy-MM-dd HH:mm:ss";
		//忽略循环引用
		options.SerializerSettings.ReferenceLoopHandling = ReferenceLoopHandling.Ignore;
		//数据格式首字母小写
		//options.SerializerSettings.ContractResolver = new CamelCasePropertyNamesContractResolver();
		//数据格式按原样输出
		options.SerializerSettings.ContractResolver = new DefaultContractResolver();
		//忽略空值
		options.SerializerSettings.NullValueHandling = NullValueHandling.Ignore;
	});
}

```

參考資料：
[asp.net core 3.0 MVC JSON 全局配置 - wzh0717 - 博客园 (cnblogs.com)](https://www.cnblogs.com/lenovo_tiger_love/p/12054253.html)
[.NET Core 处理 WebAPI JSON 返回烦人的null为空 - Crazy Struggle - 博客园 (cnblogs.com)](https://www.cnblogs.com/1312mn/p/14262985.html)
[從 Newtonsoft.Json 移轉至 System.Text.Json](https://learn.microsoft.com/zh-tw/dotnet/standard/serialization/system-text-json/migrate-from-newtonsoft?pivots=dotnet-9-0#use-ai-to-migrate)
