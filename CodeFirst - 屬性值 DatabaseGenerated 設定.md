依慣例，當 Primary Key 為 short、int、long 或 Guid 時預設為新增時自動給值，如下：

[DatabaseGenerated(DatabaseGeneratedOption.None)] 取消自動產生。 
 - 對應的Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.BlogId)
        .ValueGeneratedNever();
}
```

[DatabaseGenerated(DatabaseGeneratedOption.Identity)] 可宣告屬性為新增時自動給值，
 - 對應的Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Inserted)
        .ValueGeneratedOnAdd();
}
```

> 適用[[CodeFirst - Model 主鍵(PrimaryKey)設定]]，指定`GUID`屬性設為主鍵時也會自動產生
>指定 [ValueGeneratedOnAdd](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.entityframeworkcore.metadata.builders.propertybuilder.valuegeneratedonadd) 在 DateTime 屬性上將不會有任何作用

[DatabaseGenerated(DatabaseGeneratedOption.Computed)] 則是新增及更新時自動給值。
 - 對應的Fluent API：
 ```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.LastUpdated)
        .ValueGeneratedOnAddOrUpdate();
} 
 ```

> 適用於`byte[]`的屬性值，`byte[]`屬性會標記為並行控制符的屬性，會以 rowversion 數據類型設置，以便在資料庫中自動產生值

DatabaseGenerated 結論：
> 屬性可設為「新增自動給值」或是「新增及更新都自動給值」。 屬性值可從 EF 端也可在 DB 端產生
> 若是在 DB 端產生(例如自動跳號、Schema 指定 GETDATE() 作為日期欄位預設值)，EF 會先給一個暫時值，SaveChanges() 後再置換成 DB 產生的結果。 若加入 DbContext 時有指定值(string != null, int != 0, Guid != Guie.Empty)，EF Core 會試著使用指定的值寫入資料庫。
