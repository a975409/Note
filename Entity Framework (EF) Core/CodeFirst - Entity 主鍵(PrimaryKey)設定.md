[鍵 - EF Core | Microsoft Learn](https://learn.microsoft.com/zh-tw/ef/core/modeling/keys?tabs=data-annotations)

#### 主鍵設定方式

1. 依照慣例，名為 `Id` 或 `<type name>Id` 的屬性將會設定為實體的主鍵：
```C#
internal class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}

internal class Truck
{
    public string TruckId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

2. 也可在屬性欄位加上`[Key]`，手動指定`PrimaryKey`：
 - Data Annotation(資料註解)：
```C#
internal class Car
{
    [Key]
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

 - Fluent API：
 ```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Car>()
        .HasKey(c => c.LicensePlate);
}
 ```

#### 複合主鍵設定方式

> 以下範例為將`State`和`LicensePlate`設為複合主鍵

1. Data Annotation(資料註解)：
```C#
[PrimaryKey(nameof(State), nameof(LicensePlate))]
internal class Car
{
    public string State { get; set; }
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Car>()
        .HasKey(c => new { c.State, c.LicensePlate });
}
```

#### 主鍵值自動產生規則

`DatabaseGeneratedOption.None`、`DatabaseGeneratedOption.Identity`設定說明請參考：
[[CodeFirst - 屬性值 DatabaseGenerated 設定]]

#### 替代主鍵

> 除了主鍵之外，替代鍵可作為每個實體實例的替代唯一標識符；它也可以用作關聯的目標(設定FK)。 
> 但如果只在數據行上強制執行唯一性，建議設定唯一索引（請參閱 [索引](https://learn.microsoft.com/zh-tw/ef/core/modeling/indexes)）。 

1. 主表`Primary Key` 之外的其他不重複 Key，可用於與子表 `Foreign Key` 關聯，需透過 Fluent API `HasForeignKey()` 指定：
```C#
internal class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        //`Blog`和`Post`關聯設定
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .HasForeignKey(p => p.BlogUrl) //子表的`ForeignKey`
            .HasPrincipalKey(b => b.Url); //主表的替代主鍵
    }
}

//主表
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

//子表
public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public string BlogUrl { get; set; }
    public Blog Blog { get; set; }
}
```

2. 如果只是要增加 Unique Index：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	//單一屬性設定替代主鍵
    modelBuilder.Entity<Car>()
        .HasAlternateKey(c => c.LicensePlate);
        
    //將多個屬性設定為複合替代主鍵
    modelBuilder.Entity<Car>()
        .HasAlternateKey(c => new { c.State, c.LicensePlate });
}
```

最後，依照慣例，針對替代索引鍵引進的索引和條件約束將會命名 `AK_<type name>_<property name>` （針對複合替代索引鍵 `<property name>` 會變成屬性名稱的底線分隔清單）。 

可以設定`替代主鍵的索引和唯一性約束`的名稱。
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Car>()
        .HasAlternateKey(c => c.LicensePlate)
        .HasName("AlternateKey_LicensePlate");// 設定約束條件的名稱
}
```

