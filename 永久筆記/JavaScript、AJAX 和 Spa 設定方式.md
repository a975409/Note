參考網址： [https://ithelp.ithome.com.tw/articles/10248847](https://ithelp.ithome.com.tw/articles/10248847)

> 前端呼叫後端API的套件axios，他有預設cookie='XSRF-TOKEN'、Request Header='X-XSRF-TOKEN'，所以不用在前端另外設定cookie和Header
> 參考網址：https://israynotarray.com/nodejs/20210527/2874571028/

Program.cs
```C#
//設定request header存放的token名稱
services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
```

RequestGetXSRFTokenMiddleware.cs 設定在那些路徑下，才能取得XSRF-TOKEN：
```C#
//防止跨網站偽造要求 (XSRF/CSRF) 攻擊，進到功能的Create或Detail才會取得token
//參考網址：https://ithelp.ithome.com.tw/articles/10248847
public class RequestGetXSRFTokenMiddleware
{
    private readonly RequestDelegate _next;
    private readonly IAntiforgery _antiforgery;

    public RequestGetXSRFTokenMiddleware(RequestDelegate next, IAntiforgery antiforgery)
    {
        _next = next;
        _antiforgery = antiforgery;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        if (context.Request.RouteValues.TryGetValue("controller", out object controller) && context.Request.RouteValues.TryGetValue("action", out object action))
        {
            string controllerName = controller.ToString();
            string actionName = action.ToString();

            if ((controllerName.Length == 6 && int.TryParse(controllerName.Substring(2, 4), out int temp) && (actionName == "Create" || actionName == "Detail")) ||
                (controllerName == "Auth" && actionName == "Login" && !AuthHepler.CheckLogin(context)) ||
                (controllerName == "AC0011" && actionName == "Index") ||
                (controllerName == "AC0054" && (actionName == "AccountRcvWindowOpenCreate" || actionName == "AccountPayWindowOpenCreate")))
            {
                var tokens = _antiforgery.GetAndStoreTokens(context);
                context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken,
                    new CookieOptions()
                    {
                        HttpOnly = false,
                        //MaxAge = TimeSpan.FromMinutes(UserAuthenticationOptions.ExpiresMinute)
                    });
            }
        }

        await _next(context);
    }
}

public static class RequestGetXSRFTokenMiddlewareExtensions
{
    /// <summary>
    /// 防止跨網站偽造要求 (XSRF/CSRF) 攻擊，進到功能的Create或Detail才會取得token
    /// </summary>
    /// <param name="builder"></param>
    /// <returns></returns>
    public static IApplicationBuilder RequestGetXSRFToken(
        this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<RequestGetXSRFTokenMiddleware>();
    }
}
```

在Program.cs新增以下程式碼：
```C#
 app.UseStaticFiles();
 app.UseRouting();
 app.UseSession();
 app.UseAuthentication();
 app.UseAuthorization();

 //新增這一段就好，上述只是說明要擺放的位置而已
 app.RequestGetXSRFToken();

```
