[實體類型 - EF Core | Microsoft Learn](https://learn.microsoft.com/zh-tw/ef/core/modeling/entity-types?WT.mc_id=DOP-MVP-37580&tabs=fluent-api#view-mapping)
[System.ComponentModel.DataAnnotations 命名空間 | Microsoft Learn](https://learn.microsoft.com/zh-tw/dotnet/api/system.componentmodel.dataannotations?view=net-5.0)
[無主鍵實體類型](https://learn.microsoft.com/zh-tw/ef/core/modeling/keyless-entity-types?tabs=data-annotations)
[空間數據](https://learn.microsoft.com/zh-tw/ef/core/modeling/spatial)
# EF Core 提供三種設定 Model 方式：

> Fluent API 的優先權最高，可覆寫慣例及資料註解設定。

1. 慣例 (Convension)
2. Data Annotation(資料註解，以 Attribute 形式加註於 Property)
3. Fluent API：Fluent API 要寫在 DbContext.OnModelCreating()，例如：
	```C#
	class MyContext : DbContext
	{
	    public DbSet<Blog> Blogs { get; set; }
	
	    protected override void OnModelCreating(ModelBuilder modelBuilder)
	    {
	        modelBuilder.Entity<Blog>() //Fluent API設定方式
	            .Property(b => b.Url)
	            .IsRequired();
	    }
	}
	public class Blog //Entity 型別
	{
	    //慣例，屬性名稱為 Id 或 <type name>Id 會自動成為 Entity 的 Key
	    public int BlogId { get; set; } 
	    public string Url { get; set; }
	    [Required] //Data Annotation設定方式
	    public string Blogger { get; set; }
	}

	```

# Entity 設計相關設定
#### 在`Entity`中包括`Class`，有以下三種包含設定方式
- `Blog` 包含，因為它會在內容上的 DbSet 屬性中公開。
- `Post` 因為透過導覽屬性探索到 `Blog.Posts` 它，所以會包含它。
- `AuditEntry`包含，因為它在 中 `OnModelCreating` 指定。
```C#
internal class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<AuditEntry>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}

public class AuditEntry
{
    public int AuditEntryId { get; set; }
    public string Username { get; set; }
    public string Action { get; set; }
}
```

#### 排除`Entity`的設定方式
1. Data Annotation(資料註解)：
```C#
[NotMapped]//=>加上這段即可
public class BlogMetadata
{
    public DateTime LoadedFromDatabase { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<BlogMetadata>();
}
```

#### 指定`Entity`排除移轉：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	//加上ExcludeFromMigrations()
    modelBuilder.Entity<IdentityUser>()
        .ToTable("AspNetUsers", t => t.ExcludeFromMigrations());
}
```
> 移轉時不會建立 `AspNetUsers` 資料表，但`IdentityUser` 仍包含在`DBContext`中，而且可以正常使用。

#### 設定資料表名稱
1. Data Annotation(資料註解)：
```C#
[Table("blogs")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ToTable("blogs");
}
```

#### 設定資料表批註
1. Data Annotation(資料註解)：
```C#
[Comment("Blogs managed on the website")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().ToTable(
        tableBuilder => tableBuilder.HasComment("Blogs managed on the website"));
}
```

#### 設定資料表架構(Schema)
1. Data Annotation(資料註解)：
```C#
[Table("blogs", Schema = "blogging")]
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

2. Fluent API：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ToTable("blogs", schema: "blogging");
}
```

> 也可以使用 Fluent API 定義預設架構，而不是為每個資料表指定架構
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.HasDefaultSchema("blogging");
}
```

#### 設定檢視表

> EF 會假設參考的檢視已存在於資料庫中，因此不會在移轉中自動建立檢視。

```C#
modelBuilder.Entity<Blog>()
    .ToView("blogsView", schema: "blogging");
```

#### 設定資料表值函式對應
1. 建立一個`Entity`，無`PK值`：
```C#
public class BlogWithMultiplePosts
{
    public string Url { get; set; }
    public int PostCount { get; set; }
}
```

2. 接下來，在資料庫中建立下列資料表值函式，只傳回具有多個文章的部落格，以及與每個部落格相關聯的文章數目：
```sql
CREATE FUNCTION dbo.BlogsWithMultiplePosts()
RETURNS TABLE
AS
RETURN
(
    SELECT b.Url, COUNT(p.BlogId) AS PostCount
    FROM Blogs AS b
    JOIN Posts AS p ON b.BlogId = p.BlogId
    GROUP BY b.BlogId, b.Url
    HAVING COUNT(p.BlogId) > 1
)
```

3. 現在，實體 `BlogWithMultiplePosts` 可以透過下列方式對應至此函式：
```C#
modelBuilder.Entity<BlogWithMultiplePosts>().HasNoKey().ToFunction("BlogsWithMultiplePosts");
```

4. 呼叫方式如下：
```C#
var query = from b in context.Set<BlogWithMultiplePosts>()
            where b.PostCount > 3
            select new { b.Url, b.PostCount };
```

轉譯的SQL如下：
```SQL
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

#### 採用繼承方式設定`Entity`：
```C#
internal class MyContext : DbContext
{
    public DbSet<RssBlog> RssBlogs { get; set; }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	//RssBlog指定繼承的Blog
	modelBuilder.Entity<RssBlog>().HasBaseType<Blog>()
}
```