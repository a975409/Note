
[在 EF Core 中對應關聯性](https://learn.microsoft.com/zh-tw/ef/core/modeling/relationships#mapping-relationships-in-ef-core)
[一對多關聯](https://learn.microsoft.com/zh-tw/ef/core/modeling/relationships/one-to-many)
[一對一關聯性](https://learn.microsoft.com/zh-tw/ef/core/modeling/relationships/one-to-one)
[ 多對多關聯性](https://learn.microsoft.com/zh-tw/ef/core/modeling/relationships/many-to-many)

[[解決刪除資料時無法刪除底下子資料的問題]]
[[CodeFirst - Entity 主鍵(PrimaryKey)設定]]

> 依照慣例，EF Core會為`ForeignKey`建立索引

### 基本一對多：
1. 慣例設定方式：
```C#
//主表
public class Blog
{
    public int Id { get; set; } //PrimaryKey
    public string Name { get; set; }
    public virtual Uri SiteUri { get; set; }

    public ICollection<Post> Posts { get; }
}

//子表
public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PublishedOn { get; set; }
    public bool Archived { get; set; }

    public int BlogId { get; set; } //ForeignKey
    public Blog Blog { get; set; } //主表導覽屬性
}
```

2. Fluent API 設定方式：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    //設定方式一：以主表為主設定
    modelBuilder.Entity<Blog>()
        .HasMany(e => e.Posts)
        .WithOne(e => e.Blog)
        .HasForeignKey(e => e.BlogId)
        .HasPrincipalKey(e => e.Id);
        
    //設定方式二：以子表為主設定
    modelBuilder.Entity<Post>()
        .HasOne(e => e.Blog)
        .WithMany(e => e.Posts)
        .HasForeignKey(e => e.BlogId);
}
```

### 基本多對多
1. 慣例設定方式：
```C#
public class Post
{
    public int Id { get; set; }
    public List<Tag> Tags { get; } = [];
}

public class Tag
{
    public int Id { get; set; }
    public List<Post> Posts { get; } = [];
}
```

2. Fluent API 設定方式：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>()
        .HasMany(e => e.Tags)
        .WithMany(e => e.Posts);
}
```