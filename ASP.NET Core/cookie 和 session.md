設定cookie：
```C#
HttpContext.Response.Cookies.Append("CookieKey", "CookieValue", new CookieOptions
{
    HttpOnly = true
});
```

![[Pasted image 20250102201010.png]]

session：
Program.cs：
```C#
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession();

app.UseSession();
```
![[Pasted image 20250102201504.png]]

設定session：
```C#
HttpContext.Session.SetString("SessionKey", "SessionValue");
```

https://ithelp.ithome.com.tw/articles/10249151