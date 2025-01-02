![[Pasted image 20250102182337.png]]
![[Pasted image 20250102182351.png]]

![[Pasted image 20250102182417.png]]
![[Pasted image 20250102182433.png]]

限制cookie只能允許Http傳輸使用，前端JavaScript無法存取(Program.cs)：
```C#
builder.Services.AddAuthentication().AddCookie(c => c.Cookie.HttpOnly = true);
```
