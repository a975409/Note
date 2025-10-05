> 依慣例，當 Primary Key 為 short、int、long 或 Guid 時預設為新增時自動給值

[欄位自動跳號設定](https://learn.microsoft.com/zh-tw/ef/core/modeling/sequences)

[DatabaseGenerated(DatabaseGeneratedOption.Identity)] 可宣告屬性為新增時自動給值，
 - Data Annotation(資料註解)：
```C#
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    public DateTime Inserted { get; set; }
}
```
 
 - Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Inserted)
        .ValueGeneratedOnAdd();
}
```

> 適用[[CodeFirst - Entity 主鍵(PrimaryKey)設定]]，指定`GUID`屬性設為主鍵時也會自動產生
>指定 [ValueGeneratedOnAdd](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.entityframeworkcore.metadata.builders.propertybuilder.valuegeneratedonadd) 在 `DateTime` 屬性上將不會有任何作用

[DatabaseGenerated(DatabaseGeneratedOption.Computed)] 則是新增及更新時自動給值。
 - Data Annotation(資料註解)：
```C#
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime LastUpdated { get; set; }
}
```
 - Fluent API：
 ```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.LastUpdated)
        .ValueGeneratedOnAddOrUpdate();
} 
 ```

DatabaseGenerated 結論：
> 屬性可設為「新增自動給值」或是「新增及更新都自動給值」。 屬性值可從 EF 端也可在 DB 端產生
> 若是在 DB 端產生(例如自動跳號、Schema 指定 GETDATE() 作為日期欄位預設值)，EF 會先給一個暫時值，SaveChanges() 後再置換成 DB 產生的結果。 若加入 DbContext 時有指定值(string != null, int != 0, Guid != Guie.Empty)，EF Core 會試著使用指定的值寫入資料庫。


[DatabaseGenerated(DatabaseGeneratedOption.None)] 取消自動產生。 
 - Data Annotation(資料註解)：
 ```C#
 public class Blog
{
    [DatabaseGenerated(DatabaseGeneratedOption.None)]
    public int BlogId { get; set; }

    public string Url { get; set; }
}
 ```
 - Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.BlogId)
        .ValueGeneratedNever();
}
```