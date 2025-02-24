# **ViewData**

> ViewData只對一個Action有作用，生命週期只存在單一頁面中，若頁面重新導向ViewData的資料便會清除

```html
//Controllerpublic ActionResult Index(){
  ViewData["msg"]="從Index選擇器所傳來的ViewData訊息!"
}
//View
<p>@ViewData["msg"]</p>//這裡會顯示從Index選擇器所傳來的ViewData訊息!
```

# **ViewBag**

> ViewBag與ViewData有相同的功用，生命週期也一樣僅限在單一頁面中，只要重新導向後資料便會清除，與ViewData的差異是在ViewBag可產生動態屬性，不須再進行類別的轉換

```html
//Controllerpublic ActionResult Index(){
  ViewBag.msg="從Index選擇器所傳來的ViewBag訊息!"
}
//View
<p>@ViewBag.msg</p>//這裡會顯示從Index選擇器所傳來的ViewBag訊息!
```

# **TempData**

> TempData預設是將資料放在Session，所以生命週期是存在一整個Request範圍，即使網頁導向後還是能透過TempData取得後端所傳來的資料，Request執行結束後自動刪除

```html
//Controllerpublic ActionResult Index(){
  TempData["msg"]="從Index選擇器所傳來的TempData訊息!"
}
//View
<p>@TempData["msg"]</p>//這裡會顯示從Index選擇器所傳來的TempData訊息!
```

- 使用 `Peek`，你可以檢索儲存在 TempData 中的資料，而不會將其標記為刪除，因此將來的請求仍然可以使用資料
    
    ```csharp
    //first request, save value to TempData
    TempData["value"] = "someValueForNextRequest";
    
    //second request, PEEK value so it is not deleted at the end of the request
    object value = TempData.Peek("value");
    
    //third request, read value and mark it for deletion
    object value = TempData["value"];
    ```
    
- 使用 `Keep`，你可以指定實際上應保留標記為刪除的金鑰。在這種情況下，檢索資料並將其儲存為刪除需要 2 個方法呼叫：
    
    ```csharp
    //first request, save value to TempData
    TempData["value"] = "someValueForNextRequest";
    
    //second request, get value marking it from deletion
    object value = TempData["value"];
    //later on decide to keep it
    TempData.Keep("value");
    
    //third request, read value and mark it for deletion
    object value = TempData["value"];
    ```