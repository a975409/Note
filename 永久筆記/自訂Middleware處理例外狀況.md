```C#
using static System.Net.Mime.MediaTypeNames;

 var builder = WebApplication.CreateBuilder(args);

 // Add services to the container.
 builder.Services.AddControllersWithViews();
 //builder.Services.AddDbContext<TodoContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("TodoDatabase")));
 var app = builder.Build();

 // Configure the HTTP request pipeline.
 if (!app.Environment.IsDevelopment())
 {
     app.UseExceptionHandler(exceptionHandlerApp =>
     {
         exceptionHandlerApp.Run(async context =>
         {
             context.Response.StatusCode = StatusCodes.Status500InternalServerError;

             context.Response.ContentType = Text.Plain;

             await context.Response.WriteAsync("拋出一個Exception");

             var exceptionHandlerPathFeature = context.Features.Get<IExceptionHandlerPathFeature>();

             //針對特定的例外錯誤回應不同的訊息
             if (exceptionHandlerPathFeature?.Error is FileNotFoundException)
             {
                 await context.Response.WriteAsync("找不到檔案");
             }

             if (exceptionHandlerPathFeature?.Path == "/")
             {
                 await context.Response.WriteAsync(" Page Home ");
             }
         });
     });
 }
```