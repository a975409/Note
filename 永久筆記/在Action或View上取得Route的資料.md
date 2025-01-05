## **Action**
取得完整Route的資料
```csharp
var myRoute = RouteData.Values;
```

單獨取一欄的Route資料
```csharp
var controller = RouteData.Values["controller"].ToString();
var action = RouteData.Values["action"].ToString();
var id = RouteData.Values["id"].ToString();
```

把Route資料轉成包含斜線的網址(string)
```csharp
var myRouteUrl = Url.RouteUrl(RouteData.Values);
```

## **在filter取得Route資料**
```csharp
public override void OnActionExecuted(ActionExecutedContext filterContext)
{
	var actionName = filterContext.RouteData;
}
```

### **取得網址、querystring 和 post的資料**
承上，可以用 filterContext.HttpContext.Request 取相關資料

- Request.Url; (已包含 QueryString)
- Request.QueryString; (僅 QueryString)
- Request.Form; (僅 post form 的資料)

## **View(razor)**
View不能夠直接呼叫RouteData，需要用ViewContext.RouteData來取
```html
@ViewContext.RouteData.Values["id"]
```