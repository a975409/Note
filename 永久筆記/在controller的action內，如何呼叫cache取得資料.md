
參考資料：
[ASP.NET Core 中的回應快取 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/performance/caching/response?view=aspnetcore-7.0)

Program.cs
```C#
builder.Services.AddResponseCaching();

app.UseResponseCaching();
```


![[Pasted image 20250104025346.png]]