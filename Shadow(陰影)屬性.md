[Shadow 和 Indexer 屬性](https://learn.microsoft.com/zh-tw/ef/core/modeling/shadow-properties)

> 陰影屬性：指未定義在 `Entity` 類別需透過 Change Tracker 維護及存取的隱藏屬性

1. 一個例子是用於建立 `Entity` 關聯的 Foreign Key 屬性。  
>如下例，Post 類別會產生 Shadow 屬性 - BlogId：
```C#
internal class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
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

    // 在Model內未設定ForeignKey(外鍵屬性)，但會在資料庫產生一個ForeignKey(外鍵屬性)
    public Blog Blog { get; set; }
}
```

2. 自訂 Shadow 屬性只能使用 Fluent API 宣告：
```C#
internal class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property<DateTime>("LastUpdated");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}

//存取 Shadow 屬性
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;

//也可以透過 `EF.Property` 靜態方法，在 LINQ 查詢中參考陰影屬性：
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));

```