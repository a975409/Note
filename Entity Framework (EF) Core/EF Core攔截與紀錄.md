```C#
builder.Services.AddDbContext<BlogContext>(options=>{
	options.EnableSensitiveDataLogging(); //=>可替換為 options.LogTo(Console.WriteLine)

	//EF 發生錯誤時，可提供更詳細的錯誤訊息
  options.EnableDetailedErrors();
  
  //可定義紀錄的細節
  options.LogTo(Console.WriteLine,LogLevel.Information);
});
```