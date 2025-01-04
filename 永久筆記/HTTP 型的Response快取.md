> 設定HTTP 型的Response快取需設定Http Header[Cache-Control]

下表顯示常見的 `Cache-Control` 指示詞：
![[Pasted image 20250104092741.png]]

下表顯示其他在快取中發揮作用的快取標頭。
![[Pasted image 20250104092802.png]]

ResponseCache 屬性使用方式如下：
[設定VaryByHeader屬性](https://learn.microsoft.com/zh-tw/aspnet/core/performance/caching/response?view=aspnetcore-7.0#vary)
[設定`NoStore` 和 `Location.None`](https://learn.microsoft.com/zh-tw/aspnet/core/performance/caching/response?view=aspnetcore-7.0#nostore-and-locationnone)
[設定位置和持續時間](https://learn.microsoft.com/zh-tw/aspnet/core/performance/caching/response?view=aspnetcore-7.0#location-and-duration)

[ResponseCache](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.mvc.responsecacheattribute) 屬性可以套用至：
- Razor Pages：屬性無法套用至處理常式方法中。 與 UI 應用程式一起使用的瀏覽器會阻止快取回應。
- MVC 控制器。
- MVC 動作方法：方法層級屬性會覆寫類別層級屬性中所指定的設定。