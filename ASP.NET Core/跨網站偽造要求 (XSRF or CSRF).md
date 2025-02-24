
1.ajax呼叫api的方式：
[[JavaScript、AJAX 和 Spa 設定方式]]

2.Html Form標籤傳送：
```Html
<form action="CSRFAttack" method="post">
	@Html.AntiForgeryToken()
</form>
```

controller.cs
```C#
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult CSRFAttack()
{
    return View();
}
```
