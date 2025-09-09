### 輸入以下指令(二擇一)

第一種：
```C#
//第一次執行需安裝 dotnetef 全域工具
dotnet tool install --global dotnet-ef

//輸入以下指令產生efModel
dotnet-ef dbcontext scaffold "Data Source=.;Initial Catalog=Northwind;Integrated Security=True" "Microsoft.EntityFrameworkCore.SqlServer" -o ./Models -c BlogContext -f

```

![[Untitled 1.png]]
![[Untitled 2.png]]

[目標目錄和命名空間](https://learn.microsoft.com/zh-tw/ef/core/managing-schemas/scaffolding/?tabs=dotnet-core-cli#target-directories-and-namespaces)

第二種：
```C#
Scaffold-DbContext "Server=伺服器位置;Database=資料庫;Trusted_Connection=True;User ID=帳號;Password=密碼" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Force -context 資料庫名稱

```

將產生的繼承DbContext的類別裡面以下的程式碼做修改
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) {
	//將optionsBuilder.UseSqlServer括號裡面的連線字串放到appsettings.json
	//將if整段全部刪除
	if (!optionsBuilder.IsConfigured)
	{
	#warning To protect potentially sensitive information in your connection string, you should move it out of source code. You can avoid scaffolding the connection string by using the Name= syntax to read it from configuration - see <https://go.microsoft.com/fwlink/?linkid=2131148>. For more guidance on storing connection strings, see <http://go.microsoft.com/fwlink/?LinkId=723263>.

	optionsBuilder.UseSqlServer("Server=伺服器位置;Database=資料庫;Trusted_Connection=True;User ID=帳號;Password=密碼");
	}
}
```

在appsettings.json裡面設定連線字串
```JSON
{
	"Logging": {
		 "LogLevel": {
			"Default": "Information",
			"Microsoft": "Warning",
			"Microsoft.Hosting.Lifetime": "Information"
		 }
	},
	"AllowedHosts": "*",

	 //連線字串
	 "ConnectionStrings": {
		 "TodoDatabase": "Server=伺服器位置;Database=資料庫;Trusted_Connection=True;User ID=帳號;Password=密碼"
	 }
 }

```

資料庫物件服務註冊`DbContext`：
Startup.cs 新增下列程式碼(ASP.NET Core 5.x)：
```C#
public void ConfigureServices(IServiceCollection services)
{
	services.AddControllersWithViews();

	//新增這段程式碼，MvcMovieContext就是指在 appsettings.json 設定的連線字串
	services.AddDbContext<MvcMovieContext>(options =>
			options.UseSqlServer(Configuration.GetConnectionString("MvcMovieContext")));
}
```

如果是(ASP.NET Core 6.x以上)，則在(Program.cs)設定如下：
```C#
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<TodoContext>(options =>
options.UseSqlServer(builder.Configuration.GetConnectionString("TodoDatabase")));
```