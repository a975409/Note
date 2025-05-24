### 安裝 Entity Framework

```powershell
Install-Package EntityFramework
```

### 建立模型
```C#
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

### 在`web.config`建立連線字串
```XML
<connectionStrings>
  <add name="CodeFirstDB"
        connectionString="Data Source=.;Initial Catalog=DabaseName;Integrated Security=True;
        Connect Timeout=30;Encrypt=False;TrustServerCertificate=True;
        ApplicationIntent=ReadWrite;MultiSubnetFailover=False"
        providerName="System.Data.SqlClient" />
</connectionStrings>
```

### 建立 DBContext
```C#
using System.Data.Entity;
public class BloggingContext : DbContext
{
	public BloggingContext() : base("name=CodeFirstDB")
    {
    }

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

### 以下更新資料庫方式二擇一
#### Migrations(推薦)

>  指令是要在Nuget 套件管理員中的「套件管理主控台」，而非使用「開發人員 PowerShell」

#### 初次建立`Migrations`：
```Package Manager Console
Enable-Migrations
```

執行後會自動加入 Migrations 資料夾以及 `COnfiguration.cs`：
Migrations/Configuration.cs：
```C#
internal sealed class Configuration : 
  DbMigrationsConfiguration<EF_CodeFirst.DAL.SqlDbContext>
{
    public Configuration()
    {
        AutomaticMigrationsEnabled = true;
        ContextKey = "EF_CodeFirst.DAL.SqlDbContext";
        AutomaticMigrationDataLossAllowed = true;
    }

    protected override void Seed(EF_CodeFirst.DAL.BloggingContext context)
    {
	    //測試資料
        ...
    }
}
```

#### 資料庫初次建立或有Model內容有更新時，需依序執行以下指令：
> name 為自定義名稱

```Package Manager Console
Add-Migration [name]
update-database
```

Add-Migration：
> 會基於您這次對 Model 做的變更產生出新的資料遷移檔 (Migration)

```Package Manager Console
Add-Migration [name]
```

更新資料庫：
> 就會把關於 Migration 和 Configuration 檔的內容對資料庫產生實際的變動。
```Package Manager Console
update-database
```

#### 資料庫初始化策略(Initialization Strategies)
資料庫初始化的策略：
1. CreateDatabaseIfNotExists：
	預設的行為，當資料庫不存在時建立，EF 1.0 加入。
	
2. DropCreateDatabaseAlways：
	每次重新啟動 IISExpress 時，自動重新建立資料庫，適合使用在模擬與測試環境，EF 1.0 加入。
	
3. DropCreateDatabaseIfModelChanges：
	當模型 (POCO 類別) 發生改變時，自動重新建立資料庫，適合在設計類別的開發情境，EF 4.1 加入

可在`DBContext`內設定初始化策略：
```C#
public class BloggingContext : DbContext
{
    public BloggingContext() : base("name=CodeFirstDB")
    {
        Database.SetInitializer<BloggingContext>(new DropCreateDatabaseAlways<BloggingContext>());
        Database.SetInitializer<BloggingContext>(new CreateDatabaseIfNotExists<BloggingContext>());
        Database.SetInitializer<BloggingContext>(new DropCreateDatabaseIfModelChanges<BloggingContext>());
    }
    
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

也能基於上述的資料庫初始化策略，客製化資料庫初始化，結合自動加入測試資料的應用方式：
```C#
public class Initializer : DropCreateDatabaseIfModelChanges<BloggingContext>
{
    protected override void Seed(BloggingContext context)
    {
        base.Seed(context);

        var blog = new Blog { Name = "KEN" }; 
        context.Blogs.Add(blog);
        context.SaveChanges();

        Console.WriteLine("Database Initialized Done.");
    }
}

public class BloggingContext : DbContext
{
    public BloggingContext() : base("name=CodeFirstDB")
    {
         Database.SetInitializer<BloggingContext>(new Initializer());
    }
    
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

