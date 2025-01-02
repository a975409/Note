
HttpResponseException.cs
```C#
public class HttpResponseException : Exception
{
    public int StatusCode { get; }
    public object? Value { get; }

    public HttpResponseException(int statusCode, object? value = null) 
    {
        StatusCode = statusCode;
        Value = value;
    }
}
```

HttpResponseExceptionFilter.cs
```C#
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;

public class HttpResponseExceptionFilter : IActionFilter, IOrderedFilter
{
    public int Order => int.MaxValue - 10;

    public void OnActionExecuted(ActionExecutedContext context)
    {
        if (context.Exception is HttpResponseException httpResponseException)
        {
            context.Result = new ObjectResult(httpResponseException.Value);
            context.ExceptionHandled = true;
        }
    }

    public void OnActionExecuting(ActionExecutingContext context) { }
}
```

![[Pasted image 20250102180203.png]]

Program.cs=>全域套用此Filter
```C#
builder.Services.AddControllersWithViews(options => {
    options.Filters.Add<HttpResponseExceptionFilter>();
});
```
