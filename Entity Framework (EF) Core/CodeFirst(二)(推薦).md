
#### 初次建立資料庫：
1. 建立`Entity`： 

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
  public Guid Id { get; set; }

  [Required]
  [MaxLength(15)]
  public string UserName { get; set; }

  public ICollection<Article> Articles { get; set; }
}
```

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

  public DateTime PostTime { get; set; }

  public Guid UserId { get; set; }

  public User User { get; set; }

}
```
	
2. 建立`DBContext`
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
	        optionsBuilder.UseSqlServer("Data Source=.;Initial Catalog=tutorial_database;Database=Blog;Integrated Security=True;Trust Server Certificate=True");
	      }
	    }
	    protected override void OnModelCreating(ModelBuilder modelBuilder)
	    {
	      //呼叫新增測試資料的方法
	      modelBuilder.Seed();
	    }

	    public virtual DbSet<Article> Articles { get; set; }
	    public virtual DbSet<User> Users { get; set; }
	  }
	}
```

2. 建立測試資料
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

3. 將初次建立的Model更新到資料庫
```C#
//init代表產生的 migration 的檔名
//有更新Model檔案才需要執行此指令產生migrations
//如果只更新Seed的測試資料內容，則不用執行此指令，直接更新資料庫就好
dotnet ef migrations add init --context BlogContext

//更新資料庫
dotnet ef database update --context BlogContext
```

![[Pasted image 20250523203237.png]]

#### 修改資料庫模型並更新資料庫
1. 在 `Articles.cs`中加入：
```C#
public DateTime PostTime { get; set; }
```
2. 輸入以下指令：
```C#
//有更新Model檔案才需要執行此指令產生migrations
//如果只更新Seed的測試資料內容，則不用執行此指令，直接更新資料庫就好
//Add_PostTime代表產生的 migration 的檔名
dotnet ef migrations add Add_PostTime --context BlogContext

//將現有遷移版本更新至資料庫
dotnet ef database update --context BlogContext
```
