
原理：
> 1. Html Form表單：後端產生`TOKEN`->response cookie存放`TOKEN`，Form表單帶入`TOKEN`(存放在隱藏的`input`標籤)->後端驗證`TOKEN`內容，以及比對存放`TOKEN`的Form表單欄位和 `cookie`兩者是否一致
> 
> 2. AJAX：後端產生`TOKEN`->response cookie存放`TOKEN`，API request header帶入`TOKEN`->後端驗證`TOKEN`內容，以及比對存放`TOKEN`的`request header` 和 `cookie`兩者是否一致

1. 產生`TOKEN`，以下方式任選其一：
	ajax呼叫api的方式：
	[[JavaScript、AJAX 和 Spa 設定方式]]

	Html Form表單：
```Html
<form action="CSRFAttack" method="post">
	@Html.AntiForgeryToken()
</form>
```

	SPA單頁應用：
```html
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Antiforgery
@{
    var token = Antiforgery.GetAndStoreTokens(Context).RequestToken;
}

<script>
    // 將令牌存儲在全局變數中，供 SPA 框架使用
    window.antiforgeryToken = '@token';
</script>

```

2. controller.cs：action新增防僞TOKEN
```C#
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult CSRFAttack()
{
    return View();
}
```
