參考文章：
[GUID Primary Key資料庫避雷守則-黑暗執行緒](https://blog.darkthread.net/blog/guid-as-pk-on-db/)

>在 Code First 建立 Entity Framework 模型時，**叢級索引（Clustered Index）** 和 **主鍵（Primary Key, PK）** 的設定通常是綁定在一起的，因為預設情況下，主鍵會自動建立為叢級索引。

不過，EF Core（Entity Framework Core）允許你分開設定：

- 你可以指定主鍵（PK）不使用叢級索引（Clustered = false）
- 同時你可以另外為其他欄位建立叢級索引
### 範例（EF Core）

```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<MyEntity>(entity =>
    {
        // 設定主鍵，但不使用叢級索引
        entity.HasKey(e => e.Id)
              .IsClustered(false);

        // 為另一個欄位建立叢級索引
        entity.HasIndex(e => e.SomeOtherColumn)
              .IsClustered();
    });
}
```

### 總結

- EF Core 可以分開設定 PK 和叢級索引
- 透過 `IsClustered(false)` 讓 PK 不使用叢級索引
- 另外建立一個叢級索引在其他欄位
- 這功能主要支援 SQL Server