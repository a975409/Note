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
[認識 Entity Framework Core 載入關聯資料的三種方法 | The Will Will Web](https://blog.miniasp.com/post/2022/04/21/Loading-Related-Data-in-EF-Core)

> AddDbContext() 會註冊成 Scoped 生命週期(每個 Request 過程共用同一個 Instance)

```C#
public void ConfigureServices(IServiceCollection services) { services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db")); }
```

> 抓取的資料如果有關聯資料，原則上是不會自動載入的，要透過`Include`設定要載入的關聯資料
```C#
//以一筆Ord底下有多筆OrdItem的關聯為例

//以單筆OrdItem抓取Ord資料
_context.OrdItems.Include(x => x.Ord).AsNoTracking().FirstOrDefaultAsync(m => m.Id == id);

//以單筆Ord抓取多筆OrdItem資料
_context.Ords.Include(x=>x.OrdItems).AsNoTracking().FirstOrDefaultAsync(m => m.OrdNo == ordNo);

```


### 黑大EF Core筆記
[EF Core 筆記 1 - 概論-黑暗執行緒](https://blog.darkthread.net/blog/efcore-notes-1/)
[EF Core 筆記 2 - Model 設計](https://blog.darkthread.net/blog/ef-core-notes-2/)
[EF Core 筆記 3 - Model 關聯設計](https://blog.darkthread.net/blog/ef-core-notes-3/)
[EF Core 筆記 4 - 跨資料庫能力展示-黑暗執行緒](https://blog.darkthread.net/blog/ef-core-notes-4/)
[EF Core 筆記 5 - 淺談正式環境資料庫建立與換版-黑暗執行緒](https://blog.darkthread.net/blog/ef-core-notes-5/)