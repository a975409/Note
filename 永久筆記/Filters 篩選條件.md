[Filters 如何運作](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#how-filters-work)

一般實作方式：
如果採用非同步IFilter，則：
※ 因為只有一個非同步方法，所以會多一個await next();
※ await next(); 代表會繼續往下一個Filter走，所以如果要[取消或縮短](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#cancellation-and-short-circuiting)直接Response的話，就不要呼叫await next();，並設定Result
```C#
public class SampleActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        // Do something before the action executes.
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // Do something after the action executes.
    }
}

public class SampleAsyncActionFilter : IAsyncActionFilter
{
    public async Task OnActionExecutionAsync(
        ActionExecutingContext context, ActionExecutionDelegate next)
    {
        // Do something before the action executes.
        //往下一個Filter走
        await next();
        // Do something after the action executes.

		//取消或縮短
		context.Result = new ContentResult { Content =                                          nameof(ShortCircuitingResourceFilterAttribute) 
		};
    }
}
```

[Filters 類型](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#filter-types)
[設定全域的FIlters](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#filter-scopes-and-order-of-execution)

Middlewave 和 各Filters 的執行順序：
![[Pasted image 20250102162215.png]]

各種Filter的說明和設定方式，依上圖執行順序排列：
[授權篩選條件](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#authorization-filters)
[資源篩選條件](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#authorization-filters)
[動作篩選條件](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#action-filters)
[結果篩選條件](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#result-filters)
[例外狀況篩選條件(只要有發生例外錯誤就會觸發，並不會按照固定順序執行)](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/controllers/filters?view=aspnetcore-7.0#exception-filters)
