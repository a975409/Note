
> 常見的要求是具有資料庫數據行，其中包含第一次插入數據列的日期/時間（新增時產生的值），或上次更新時（新增或更新時產生的值）。 

### 建立時間戳
```C#
modelBuilder.Entity<Blog>()
    .Property(b => b.Created)
    .HasDefaultValueSql("getdate()"); //執行指定的SQL日期函式
```


### 更新時間戳

雖然預存計算數據行似乎是管理上次更新時間戳的好解決方案，但資料庫通常不允許在計算數據行中指定這類 `GETDATE()` 函式。 或者，您可以設定資料庫觸發程式以達到相同的效果：

```SQL
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    UPDATE B
    SET LastUpdated = GETDATE()
    FROM dbo.Blogs AS B
    INNER JOIN INSERTED AS I
        ON B.BlogId = I.BlogId
END
```
