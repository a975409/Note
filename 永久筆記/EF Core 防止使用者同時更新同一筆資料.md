資料庫環境：Sql Server 11.0.2100.60 EF Core版本：5.0

1. 在資料表新增以下欄位：
    ![[Untitled.png]]

※ 比較舊的SQL Server版本 資料型態只能設定 timestamp，而SQL 2008則必須設為 rowversion

1. 執行dotnet ef 或 Scaffold-DbContext指令更新ef Model
2. 如果使用者同時更新同一筆資料，就會觸發 DbUpdateConcurrencyException 例外錯誤，就代表發生並行衝突，該筆資料在更新前已被異動

ef core參考資料： [https://learn.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/concurrency?view=aspnetcore-3.1&WT.mc_id=DOP-MVP-37580https://blog.darkthread.net/blog/efcore-upldate-conflict/](https://learn.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/concurrency?view=aspnetcore-3.1&WT.mc_id=DOP-MVP-37580https://blog.darkthread.net/blog/efcore-upldate-conflict/)

Sql Server參考資料： [http://dboffat.blogspot.com/2017/01/sql-servertimestamp.htmlhttps://www.cnblogs.com/ljhdo/p/4791838.html](http://dboffat.blogspot.com/2017/01/sql-servertimestamp.htmlhttps://www.cnblogs.com/ljhdo/p/4791838.html)