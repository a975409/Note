[實體屬性 - EF Core | Microsoft Learn](https://learn.microsoft.com/zh-tw/ef/core/modeling/entity-properties?tabs=data-annotations%2Cwith-nrt)
[System.ComponentModel.DataAnnotations 命名空間 | Microsoft Learn](https://learn.microsoft.com/zh-tw/dotnet/api/system.componentmodel.dataannotations?view=net-5.0)
[產生的值 - EF Core | Microsoft Learn](https://learn.microsoft.com/zh-tw/ef/core/modeling/generated-properties?tabs=data-annotations)
[設定索引器屬性](https://learn.microsoft.com/zh-tw/ef/core/modeling/shadow-properties#configuring-indexer-properties)
[屬性包實體類型](https://learn.microsoft.com/zh-tw/ef/core/modeling/shadow-properties#property-bag-entity-types)
[欄位自動跳號設定](https://learn.microsoft.com/zh-tw/ef/core/modeling/sequences)

[[CodeFirst - Entity 主鍵(PrimaryKey)設定]]
[[CodeFirst - 屬性值 DatabaseGenerated 設定]]
[[CodeFirst - 自動產生日期時間值]]
[[Shadow(陰影)屬性]]
[[支援欄位]]

#### 排除屬性的設定方式
1. Data Annotation(資料註解)：
```C#
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    [NotMapped]
    public DateTime LoadedFromDatabase { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Ignore(b => b.LoadedFromDatabase);
}
```

#### 設定`RowVersion`用於避免資料更新衝突
1. Data Annotation(資料註解)：
```C#
public class YourEntity { 
	public int Id { get; set; } 
	
	[Timestamp] // 標示為 rowversion 欄位，用於併發控制 
	public byte[] RowVersion { get; set; } 
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    // Fluent API 方式設定 rowversion
    modelBuilder.Entity<YourEntity>()
	    .Property(e => e.RowVersion)
	    .IsRowVersion();
}
```

> 如果使用者同時更新同一筆資料，就會觸發`DbUpdateConcurrencyException`例外錯誤，就代表發生並行衝突，該筆資料在更新前已被異動

#### 設定欄位名稱
1. Data Annotation(資料註解)：
```C#
public class Blog
{
    [Column("blog_id")]
    public int BlogId { get; set; }

    public string Url { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.BlogId)
        .HasColumnName("blog_id");
}
```

#### 指定產生的資料表欄位數據類型
1. Data Annotation(資料註解)：
```C#
public class Blog
{
    public int BlogId { get; set; }

    [Column(TypeName = "varchar(200)")]
    public string Url { get; set; }

    [Column(TypeName = "decimal(5, 2)")]
    public decimal Rating { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        eb =>
        {
            eb.Property(b => b.Url).HasColumnType("varchar(200)");
            eb.Property(b => b.Rating).HasColumnType("decimal(5, 2)");
        });
}
```

#### 設定欄位最大長度

> 長度上限僅適用於陣列資料類型，例如 `string` 和 `byte[]`，另外還可以設定`Entity`驗證的錯誤訊息

1. Data Annotation(資料註解)：
```C#
public class Blog
{
    public int BlogId { get; set; }

    [MaxLength(500)]
    public string Url { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .HasMaxLength(500);
}
```

#### 設定欄位精度與範圍

> 僅適用於 `decimal` 和 `DateTime`
> Entity Framework 不會在將數據傳遞至提供者之前，對精度或小數刻度進行任何驗證。 是否驗證取決於提供者或數據存放區的決定。 例如，當以 SQL Server 為目標時，資料類型為 `datetime` 的列不允許設定精確度，而 `datetime2` 的列可以將精確度設定在 0 到 7 之間。

1. Data Annotation(資料註解)：
```C#
public class Blog
{
    public int BlogId { get; set; }
    [Precision(14, 2)] //會在資料表建立decimal(14,2)` 的數據行
    public decimal Score { get; set; }
    
    [Precision(3)] //會在資料表建立`datetime2(3)`的數據行
    public DateTime LastUpdated { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Score)
        .HasPrecision(14, 2);

    modelBuilder.Entity<Blog>()
        .Property(b => b.LastUpdated)
        .HasPrecision(3);
}
```

#### 文字欄位設定為非Unicode

> 欄位文字屬性預設會設定為 Unicode。 您可以將資料行設定為非 Unicode，如下所示

1. Data Annotation(資料註解)：
```C#
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }

    [Unicode(false)]
    [MaxLength(22)]
    public string Isbn { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Book>()
        .Property(b => b.Isbn)
        .IsUnicode(false);
}
```

#### 必要和選擇性屬性

##### 慣例規則
1. 當屬性能夠包含 `null`時，它會被視為選擇性。 如果 `null` 不是指派給屬性的有效值，則會將其視為必要屬性。
2. 映射到關係資料庫結構時，必要屬性會被建立為不可為 Null 的欄位，而選擇性屬性會被建立為可為 Null 的欄位。
3. 所有具有 .NET 實值型別的屬性（`int`、`decimal`、`bool`等）都設定為必要
4. 所有具有可為 Null .NET 實值型別的屬性（`int?`、`decimal?`、`bool?`等）都會設定為選擇性。


> 未啟用C# [可為 Null 的參考型別 (nullable reference types, NRT)](https://learn.microsoft.com/zh-tw/dotnet/csharp/tutorials/nullable-reference-types) 功能時，則會依照慣例規則判斷該欄位是否可為 Null

停用`NRT`=>需針對屬性標記為`[Required]`，代表該屬性不可設為`null`：
```C#
public class CustomerWithoutNullableReferenceTypes
{
    public int Id { get; set; }

    [Required] // Data annotations needed to configure as required
    public string FirstName { get; set; }

    [Required] // Data annotations needed to configure as required
    public string LastName { get; set; }

    public string MiddleName { get; set; } // Optional by convention
}
```

> 當專案有啟用[可為 Null 的參考型別 (nullable reference types, NRT)](https://learn.microsoft.com/zh-tw/dotnet/csharp/tutorials/nullable-reference-types) 功能時，則必須特別針對屬性值設定是否可為Null

啟用`NRT`=>屬性預設為不可設為`null`，需針對屬性的資料型態加上`?`符號，才會代表該屬性可設為`null`：
```C#
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; } // Required by convention
    public string LastName { get; set; } // Required by convention
    public string? MiddleName { get; set; } // Optional by convention

    // Note the following use of constructor binding, which avoids compiled warnings
    // for uninitialized non-nullable properties.
    public Customer(string firstName, string lastName, string? middleName = null)
    {
        FirstName = firstName;
        LastName = lastName;
        MiddleName = middleName;
    }
}
```

#### 設定該屬性為必填
1. Data Annotation(資料註解)：
```C#
public class Blog
{
    public int BlogId { get; set; }

    [Required]
    public string Url { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

#### 設定欄位註解
1. Data Annotation(資料註解)：
```C#
public class Blog
{
    public int BlogId { get; set; }

    [Comment("The URL of the blog")]
    public string Url { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .HasComment("The URL of the blog");
}
```

#### 設定欄位擺放順序
1. Data Annotation(資料註解)：
```C#
public class EntityBase
{
    [Column(Order = 0)]
    public int Id { get; set; }
}

public class PersonBase : EntityBase
{
    [Column(Order = 1)]
    public string FirstName { get; set; }

    [Column(Order = 2)]
    public string LastName { get; set; }
}

public class Employee : PersonBase
{
    public string Department { get; set; }
    public decimal AnnualSalary { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Employee>(x =>
    {
        x.Property(b => b.Id)
            .HasColumnOrder(0);

        x.Property(b => b.FirstName)
            .HasColumnOrder(1);

        x.Property(b => b.LastName)
            .HasColumnOrder(2);
    });
}
```
