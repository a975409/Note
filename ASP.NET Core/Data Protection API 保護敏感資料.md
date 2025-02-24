```csharp
public class IndexModel : PageModel
 {
     private readonly IDataProtector _dataProtector;

     public IndexModel(IDataProtectionProvider dataProtector)
     {
         _dataProtector = dataProtector.CreateProtector("OnlineBankingApp.Pages.Customers");
     }

     public async void OnGetAsync()
     {
         foreach (var cust in _context.Customer)
         {
             //資料加密
             cust.EncCustomerID = _dataProtector.Protect(cust.ID.ToString());

						 //資料解密
						 cust.ID = _dataProtector.Unprotect(cust.EncCustomerID.ToString());
         }
     }
 }
```

[在 ASP.NET Core 中開始使用資料保護 API | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/security/data-protection/using-data-protection?view=aspnetcore-7.0)