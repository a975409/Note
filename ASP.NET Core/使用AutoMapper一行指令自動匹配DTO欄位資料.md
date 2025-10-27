1. 下載NuGet套件 
	AutoMapper
	AutoMapper.Extensions.Microsoft.DependencyInjection (該套件已停止維護，直接用AutoMapper)
    
2. 在Startup.cs裡面新增以下指令(.net 5.0)：
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    	services.AddAutoMapper(typeof(Startup));
    }
    ```

.net 6.0以上要這樣設定(僅安裝最新版的AutoMapper套件)：
```C#
//找到所有profile
builder.Services.AddAutoMapper(cfg => { }, AppDomain.CurrentDomain.GetAssemblies());
```
    
3. 建立Profiles資料夾
4. 在Profiles資料夾內建立類別，並繼承Profile類別：
    ```csharp
    public class TodoListProfile : Profile
    {
    	public TodoListProfile()
    	{
    		//<>左邊是來源，右邊是要轉換的類別
    		CreateMap<TodoList, TodoListResponseDto>();
    	}
    }
    
    ```
    
5. 在Controller一樣用DI注入的方式：
    ```csharp
    private readonly TodoContext _todoContext;
    
    //AutoMapper
    private readonly IMapper _iMapper;
    
    public TodoController(TodoContext todoContext, IMapper iMapper)
    {
    	_todoContext = todoContext;
    	_iMapper = iMapper;
    }
    
    ```
    
6. 使用方式如下：
    ```csharp
    //<要將來源資料轉換成的資料類型>，(來源資料)
    _iMapper.Map<List<TodoListResponseDto>>(source);
    
    ```
    

> 注意：盡量來源資料與Dto類別對應的屬性名稱要一致，這樣才能轉換成功，否則就只能用以下方自自訂轉換規則：
```csharp
public class TodoListProfile : Profile
{
	public TodoListProfile()
	{
		CreateMap<TodoList, TodoListResponseDto>()
			.ForMember(m => m.UploadFiles,
			a => a.MapFrom(s => s.UploadFiles.Select(
				  k => new UploadFileResponseDto
				  {
					  Name = k.Name,
					  Src = k.Src,
					  UploadFileId = k.UploadFileId
				  }).ToList()));
	}
}

```

[影片教學](https://www.youtube.com/watch?v=Zrziyw-LN7I)
[教學文章](https://blog.talllkai.com/ASPNETCore/2021/04/30/DtoUseAutoMapper)
