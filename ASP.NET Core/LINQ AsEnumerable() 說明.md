IEnumerable 與 IQueryable 只有在 AsEnumerable 或是 ToCollections 等類 Execute 相關操作(例如ToList()、ToArray()、foreach等)時才會觸發查詢，因此無論你 conditions 怎麼串，如果沒有最後的 AsEnumerable 等操作都不會造成『重新查詢』的結果。

也就是說 AsEnumerable() = ToList()、ToArray()，都是呼叫資料庫取得資料抓到記憶體內

參考資料：

[IEnumerable vs IQueryable - 差異 | 人生記事本 @kevin的日記 - 點部落](https://dotblogs.azurewebsites.net/kevin_date/2017/09/20/104713)