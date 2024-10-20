```C#
public class DataService : IDataService
{
    private readonly HttpContext _httpContext;

    public DataService(IHttpContextAccessor contextAccessor)
    {
        _httpContext = contextAccessor.HttpContext;
    }
    //...
}
```