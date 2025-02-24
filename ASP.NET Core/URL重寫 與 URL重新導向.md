[URL 重新導向和 URL 重寫](https://learn.microsoft.com/zh-tw/aspnet/core/fundamentals/url-rewriting?view=aspnetcore-8.0#url-redirect-and-url-rewrite)

URL重新導向：
![[Pasted image 20241103160944.png]]

URL重寫：
![[Pasted image 20241103161009.png]]

Program.cs 設定方式如下：
```C#
var options = new RewriteOptions()
        .AddRedirect("redirect-rule/(.*)", "redirected/$1") // URL重新導向
        .AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2",
            skipRemainingRules: true); //URL重寫

    app.UseRewriter(options);
```

## **Regex 範例**

| 目標          | Regex 字串 &比對範例                                                                           | 取代字串 &輸出範例                                              |
| ----------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| 將路徑重寫成查詢字串  | `^path/(.*)/(.*)/path/abc/123`                                                           | `path?var1=$1&var2=$2/path?var1=abc&var2=123`           |
| 移除斜線        | `^path2/(.*)/$/path2/xyz/`                                                               | `$1/path2/xyz`                                          |
| 強制使用斜線      | `^path3/(.*[^/])$/path3/xyz`                                                             | `$1//path3/xyz/`                                        |
| 避免重寫特定的要求   | `^(.*)(?<!\\.axd)$` 或`^(?!.*\\.axd$)(.*)$`是：`/path4/resource.htm`否：`/path4/resource.axd` | `rewritten/$1/rewritten/resource.htm/resource.axd`      |
| 重新排列 URL 區段 | `path5/(.*)/(.*)/(.*)path5/1/2/3`                                                        | `path5/$3/$2/$1path5/3/2/1`                             |
| 取代 URL 區段   | `^path6/(.*)/segment2/(.*)^path6/segment1/segment2/segment3`                             | `path6/$1/replaced/$2/path6/segment1/replaced/segment3` |
