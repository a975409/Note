參考網址： [(ASP.NET Core) 使用User Secret安全存放開發應用程式密鑰(VS 2017) | Drift Mind - 點部落 (dotblogs.com.tw)](https://dotblogs.com.tw/tom5707/2018/05/07/004419)[Safe storage of app secrets in development in ASP.NET Core | Microsoft Learn](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-7.0&tabs=windows)

## Enable secret storage

```
//.NET CLI
dotnet user-secrets init

```

## Set a secret

```
//.NET CLI

//設定key:value值
dotnet user-secrets set "Movies:ServiceApiKey" "12345"

```

在visual studio 對專案點擊右鍵->管理使用者密碼，就可以看到新增的key:value值

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2cc91087-6848-4dd0-a7f4-c0dc185fdacd/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b55c6234-9cb7-4c19-a3a9-c66b03991e35/Untitled.png)

## key:value值讀取方式

```
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public void OnGet()
    {
        var moviesApiKey = _config["Movies:ServiceApiKey"];

        // call Movies service with the API key
    }
}

```