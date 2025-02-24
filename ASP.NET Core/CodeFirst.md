
[https://blog.darkthread.net/blog/ef-core-notes-2/](https://blog.darkthread.net/blog/ef-core-notes-2/)[https://blog.darkthread.net/blog/ef-core-notes-3/](https://blog.darkthread.net/blog/ef-core-notes-3/)
[程序代碼第一個數據批註 - EF6 | Microsoft Learn](https://learn.microsoft.com/zh-tw/ef/ef6/modeling/code-first/data-annotations)

以下為第一種方式的完整步驟：
步驟一(以下二選一)：
[[手動設定資料庫物件]]
[[使用 新增sacffold項目快速建立資料庫物件(推薦作法)]]

步驟二：
[[新增初始資料]]

以下為第二種方式的完整步驟：
1. 建立Model和DBContext： 
	Article.cs
	```C#
	using System.ComponentModel.DataAnnotations;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Threading.Tasks;
	
	namespace SampleCodeFirst.Models;
	
	public partial class Article
	{
	
	  [Key]
	  public int Id { get; set; }
	
	  public string Title { get; set; }
	
	  public string? Content { get; set; }
	
	  public int Read { get; set; }
	
	  public Guid UserId { get; set; }
	
	  public DateTime PostTime { get; set; }
	
	  public virtual User User { get; set; }
	
	}
	
	```

	User.cs
	```C#
	using System.ComponentModel.DataAnnotations;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Threading.Tasks;
	
	namespace SampleCodeFirst.Models;
	
	public partial class User
	{
	  [Key, DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	  public Guid Id { get; set; }
	
	  [Required]
	  [MaxLength(15)]
	  public string UserName { get; set; }
	
	  public virtual ICollection<Article> Articles { get; set; }
	}
	```

	BlogContext.cs
```C#
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Threading.Tasks;
	using Microsoft.EntityFrameworkCore;
	using SampleCodeFirst.Helpers;

	namespace SampleCodeFirst.Models
	{

	  public class BlogContext : DbContext

	  {

	    public BlogContext() {}



	    public BlogContext(DbContextOptions<BlogContext> options) : base(options){}



	    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
	    {
	      if (!optionsBuilder.IsConfigured)
	      {
	        //以下這段要刪除，連線字串放到appsettings.json
	        optionsBuilder.UseSqlServer("Data Source=.;Initial Catalog=tutorial_database;Database=Blog;Integrated Security=True");
	      }
	    }

	    protected override void OnModelCreating(ModelBuilder modelBuilder)
	    {
	      modelBuilder.Seed();
	    }

	    public virtual DbSet<Article> Articles { get; set; }

	    public virtual DbSet<User> Users { get; set; }

	  }
	}
```

2. 建立範例資料
```C#
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Threading.Tasks;
	using Microsoft.EntityFrameworkCore;
	using SampleCodeFirst.Models;
	
	namespace SampleCodeFirst.Helpers
	{
	  public static class SeedDataExtentions
	  {
	    public static void Seed(this ModelBuilder modelBuilder)
	    {
	      modelBuilder.Entity<User>().HasData
	            (
	              new User
	              {
	                Id = new Guid("7c9e6679-7425-40de-944b-e07fc1f90ae7"),
	                UserName = "ATai"
	              }
	            );
	
	      modelBuilder.Entity<Article>().HasData
	      (
	        new Article
	        {
	          Id = 1,
	          Title = "ASP.NET Core",
	          Content = "第一次學ASP.NET Core就上手",
	          PostTime = new DateTime(2021, 11, 24, 14, 30, 00),
	          UserId = new Guid("7c9e6679-7425-40de-944b-e07fc1f90ae7")
	        }
	      );
	    }
	  }
	}

```

```C#

//依據 Model 和 DBContext 的設定產生 migration 的檔案
dotnet ef migrations add Add_PostTime --context BlogContext

//更新資料庫
dotnet ef database update --context BlogContext

```

[[解決刪除資料時無法刪除底下子資料的問題]]