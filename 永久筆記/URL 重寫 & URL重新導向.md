```csharp
var options = new RewriteOptions()
    //**URL 重寫**
    .AddRewrite("Post.aspx", "WeatherForecast", skipRemainingRules: true)

    //**URL重新導向**
    .AddRedirect("Post.php", "WeatherForecast");

app.UseRewriter(options);
```

※第一個參數是指接收到的請求網址

※第二個參數是指要跳轉的網址

第一個參數網址可採用Regex方式設定，參考以下表格：

## **Regex 範例**

|目標|Regex 字串 &比對範例|取代字串 &輸出範例|
|---|---|---|
|將路徑重寫成查詢字串|`^path/(.*)/(.*)/path/abc/123`|`path?var1=$1&var2=$2/path?var1=abc&var2=123`|
|移除斜線|`^path2/(.*)/$/path2/xyz/`|`$1/path2/xyz`|
|強制使用斜線|`^path3/(.*[^/])$/path3/xyz`|`$1//path3/xyz/`|
|避免重寫特定的要求|`^(.*)(?<!\\.axd)$` 或`^(?!.*\\.axd$)(.*)$`是：`/path4/resource.htm`否：`/path4/resource.axd`|`rewritten/$1/rewritten/resource.htm/resource.axd`|
|重新排列 URL 區段|`path5/(.*)/(.*)/(.*)path5/1/2/3`|`path5/$3/$2/$1path5/3/2/1`|
|取代 URL 區段|`^path6/(.*)/segment2/(.*)^path6/segment1/segment2/segment3`|`path6/$1/replaced/$2/path6/segment1/replaced/segment3`|

[ASP.NET Core 的 URL 重寫中介軟體](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/url-rewriting?view=aspnetcore-7.0#regex-examples)