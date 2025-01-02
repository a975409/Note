Program.cs
```C#
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllersWithViews();
//builder.Services.AddDbContext<TodoContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("TodoDatabase")));
var app = builder.Build();

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
}
```

HomeController.cs
```C#
public class HomeController : Controller
{
    public HomeController(){}

    public IActionResult Error()
    {
        return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
    }
}
```
