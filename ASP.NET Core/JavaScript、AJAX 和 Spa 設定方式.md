
# 第一種設定方式(推薦)

> 在View呼叫建立`TOKEN`的`function`，然後將產生的`TOKEN`帶入到底下AJAX的`header`

```HTML
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Antiforgery

@{
    ViewData["Title"] = "JavaScript";
    var requestToken = Antiforgery.GetAndStoreTokens(Context).RequestToken;
}
<input id="RequestVerificationToken" type="hidden" value="@requestToken" />

<button id="button" class="btn btn-primary">Submit with Token</button>
<div id="result" class="mt-2"></div>

@section Scripts {
<script>
    document.addEventListener("DOMContentLoaded", () => {
        const resultElement = document.getElementById("result");

        document.getElementById("button").addEventListener("click", async () => {

            const response = await fetch("@Url.Action("FetchEndpoint")", {
                method: "POST",
                headers: {
                    RequestVerificationToken:
                        document.getElementById("RequestVerificationToken").value
                }
            });

            if (response.ok) {
                resultElement.innerText = await response.text();
            } else {
                resultElement.innerText = `Request Failed: ${response.status}`
            }
        });
    });
</script>
}
```


# 第二種設定方式(與axios搭配)

> 在`Program.cs`建立`TOKEN`的`function`，並且自訂存放`TOKEN`的`reponse cookie`和`request header`的存取名稱

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
//防止跨網站偽造要求 (XSRF/CSRF) 攻擊，進到HTML頁面才會取得最新token
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
    context.Response.OnStarting(() =>
    {
        try
        {
            if (context.Response.Headers.TryGetValue("Content-Type", out var contentType))
            {
                if (contentType.ToString().Contains("text/html", StringComparison.OrdinalIgnoreCase))
                {
                    var tokens = _antiforgery.GetAndStoreTokens(context);
                    context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, new CookieOptions
                    {
                        HttpOnly = false,
                        //Secure = context.Request.IsHttps
                    });
                }
            }
        }
        catch
        {
            // 忽略錯誤，避免阻塞 Response
        }

        return Task.CompletedTask;
    });

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
