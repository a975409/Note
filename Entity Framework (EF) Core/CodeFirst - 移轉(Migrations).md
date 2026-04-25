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

## 透過 EF Core Power Tools 反向工程產生了完整的 EF Model
因為來源端資料庫已經有所有的資料表了，所以需建立空的初始遷移 (Empty Initial Migration) 來對應現有資料庫：
```
dotnet ef migrations add InitialCreate --context EVTContext --ignore-changes
```

## 假設從git取得新的版本，然後有新的遷移紀錄時
```markdown
git pull
    ↓
確認有新的 Migration 檔案（Migrations 資料夾）
    ↓
執行 dotnet ef database update
    ↓
✅ 本機資料庫自動套用新的變更
```

所以只要執行`更新資料庫`的指令，就會將新的遷移檔案更新到資料庫內：
```.NET CLI
dotnet ef database update
```

EF Core 會自動：
1. 讀取 `__EFMigrationsHistory`，知道本機目前套用到哪一個版本
2. 找出**尚未套用**的新 Migration
3. **依序執行**所有差異的 Migration

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