[[EF Core攔截與紀錄]]
[[EF Core 防止使用者同時更新同一筆資料]]
[[ASP.NET Core/MSSQL]]
[[資料庫存取環境建置]]
[GUID Primary Key資料庫避雷守則](https://blog.darkthread.net/blog/guid-as-pk-on-db/)

**自行擴充DbContext之ExecSQL用法功能(僅限EF Core，用原始SQL語法)：**
[https://youtu.be/uelo5mbdckc](https://youtu.be/uelo5mbdckc)

**關於IQueryable特性的小實驗**
[https://blog.darkthread.net/blog/iqueryable-experiment/](https://blog.darkthread.net/blog/iqueryable-experiment/)
[[LINQ AsEnumerable() 說明]]
[使用 EF Core Attach() 與 Entry().State 進行更新](https://blog.darkthread.net/blog/ef-core-attach/)
[積極式載入](https://learn.microsoft.com/zh-tw/ef/core/querying/related-data/eager#eager-loading)
[追蹤與 No-Tracking 查詢](https://learn.microsoft.com/zh-tw/ef/core/querying/tracking)

> 抓取的資料如果有關聯資料，原則上是不會自動載入的，要透過`Include`設定要載入的關聯資料

要取得關聯的資料，則需呼叫`Include`：
```C#
//以一筆Ord底下有多筆OrdItem的關聯為例

//以單筆OrdItem抓取Ord資料
_context.OrdItems.Include(x => x.Ord).AsNoTracking().FirstOrDefaultAsync(m => m.Id == id);

//以單筆Ord抓取多筆OrdItem資料
_context.Ords.Include(x=>x.OrdItems).AsNoTracking().FirstOrDefaultAsync(m => m.OrdNo == ordNo);

```
