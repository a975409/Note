[[CodeFirst(一)]]
[[CodeFirst(二)(推薦)]]

### 建立Entity
[[CodeFirst - Entity 設計]]
[[CodeFirst - Entity 屬性]]
[[CodeFirst - 關聯性設定]]

[索引與條件約束](https://learn.microsoft.com/zh-tw/ef/core/modeling/indexes?tabs=data-annotations)
[[值轉換器]]
### 實際案例
[[解決刪除資料時無法刪除底下子資料的問題]]

### 遷移指令：
```C#
//有更新Model檔案才需要執行此指令產生migrations
//如果只更新Seed的測試資料內容，則不用執行此指令，直接更新資料庫就好
//Add_PostTime代表產生的 migration 的檔名
dotnet ef migrations add Add_PostTime --context BlogContext

//將現有遷移版本更新至資料庫
dotnet ef database update --context BlogContext

//指定xxx遷移版本更新至資料庫
dotnet ef database update xxxxx --context BlogContext

//移除最新一筆的遷移(Pending)
dotnet ef migrations remove --context BlogContext

//確認有多少遷移還未套用至資料庫
dotnet ef migrations list --context BlogContext
```
