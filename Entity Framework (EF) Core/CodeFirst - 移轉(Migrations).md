https://learn.microsoft.com/zh-tw/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli

### 新增資料庫

1. 建立第一個移轉
```.NET CLI
dotnet ef migrations add InitialCreate
```

> add 後面的名稱隨便取

2. 建立資料庫：
```.NET CLI
dotnet ef database update
```

### 當有新增`Entity`或修改時，需更新資料庫
1. 建立移轉
```.NET CLI
dotnet ef migrations add AddBlogCreatedTimestamp
```

> add 後面的名稱隨便取

2. 更新資料庫：
```.NET CLI
dotnet ef database update
```

### 指定`Entity`排除移轉
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<IdentityUser>()
        .ToTable("AspNetUsers", t => t.ExcludeFromMigrations());
}
```

### 移轉相關指令
```C#
//有更新Model檔案才需要執行此指令產生migrations
//如果只更新Seed的測試資料內容，則不用執行此指令，直接更新資料庫就好
//Add_PostTime代表產生的 migration 的檔名
dotnet ef migrations add Add_PostTime --context BlogContext

//將現有遷移版本更新至資料庫
dotnet ef database update --context BlogContext

//指定xxx遷移版本更新至資料庫
dotnet ef database update xxxxx --context BlogContext

//確認有多少遷移，標記Pending的代表還未套用至資料庫
dotnet ef migrations list --context BlogContext

//移除最新一筆的遷移(Pending)
dotnet ef migrations remove --context BlogContext
```

## 重設所有遷移
1. 備份資料庫(建議)。
2. 在您的資料庫中，從移轉歷程記錄數據表中刪除所有數據列（例如 SQL Server 上的 `DELETE FROM [__EFMigrationsHistory]`）。
3. 刪除你的 **遷移** 資料夾。
4. 建立第一個移轉
```.NET CLI
dotnet ef migrations add InitialCreate
```

> add 後面的名稱隨便取

5. 更新資料庫：
```.NET CLI
dotnet ef database update
```