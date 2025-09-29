[System.ComponentModel.DataAnnotations 命名空間 | Microsoft Learn](https://learn.microsoft.com/zh-tw/dotnet/api/system.componentmodel.dataannotations?view=net-5.0)

[EF Core 筆記 2 - Model 設計](https://blog.darkthread.net/blog/ef-core-notes-2/)
[EF Core 筆記 3 - Model 關聯設計](https://blog.darkthread.net/blog/ef-core-notes-3/)

# EF Core 提供三種設定 Model 方式：
1. 慣例 (Convension)
2. Data Annotation(資料註解，以 Attribute 形式加註於 Property)
3. Fluent API：Fluent API 要寫在 DbContext.OnModelCreating()，例如：
	```C#
	class MyContext : DbContext
	{
	    public DbSet<Blog> Blogs { get; set; }
	
	    protected override void OnModelCreating(ModelBuilder modelBuilder)
	    {
	        modelBuilder.Entity<Blog>() //Fluent API
	            .Property(b => b.Url)
	            .IsRequired();
	    }
	}
	public class Blog //Entity 型別
	{
	    //慣例，屬性名稱為 Id 或 <type name>Id 會自動成為 Entity 的 Key
	    public int BlogId { get; set; } 
	    public string Url { get; set; }
	    [Required] //Data Annotation
	    public string Blogger { get; set; }
	}

	```

> Fluent API 的優先權最高，可覆寫慣例及資料註解設定。

# Entity 設計相關設定
#### 在`Model`中包括`Class`，有以下三種包含設定方式
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

#### 從Model排除Class，有以下兩種包含設定方式
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

##### 如果要將該`Class`排除移轉，但仍要保留至`Model`：
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	//加上ExcludeFromMigrations()
    modelBuilder.Entity<IdentityUser>()
        .ToTable("AspNetUsers", t => t.ExcludeFromMigrations());
}
```
> 移轉時不會建立 `AspNetUsers` 資料表，但`IdentityUser` 仍包含在`Model`中，而且可以正常使用。


