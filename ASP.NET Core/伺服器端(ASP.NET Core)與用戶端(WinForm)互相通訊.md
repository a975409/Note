以下為 伺服器端([ASP.NET](http://ASP.NET) Core)

1. 環境安裝：[開始使用 ASP.NET Core SignalR | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/tutorials/signalr?view=aspnetcore-7.0&tabs=visual-studio#add-the-signalr-client-library)
2. **建立 SignalR**
    ```csharp
    using Microsoft.AspNetCore.SignalR;
    
    namespace SignalRChat.Hubs
    {
        public class ChatHub : Hub
        {
            public async Task SendMessage(string user, string message)
            {
                await Clients.All.SendAsync("ReceiveMessage", user, message);
            }
        }
    }
    ```
    
3. **設定 SignalR**
    ```csharp
    using SignalRChat.Hubs;
    
    var builder = WebApplication.CreateBuilder(args);
    
    // Add services to the container.
    builder.Services.AddRazorPages();
    builder.Services.AddSignalR();//加入這段
    
    var app = builder.Build();
    
    // Configure the HTTP request pipeline.
    if (!app.Environment.IsDevelopment())
    {
        app.UseExceptionHandler("/Error");
        // The default HSTS value is 30 days. You may want to change this for production scenarios, see <https://aka.ms/aspnetcore-hsts>.
        app.UseHsts();
    }
    
    app.UseHttpsRedirection();
    app.UseStaticFiles();
    
    app.UseRouting();
    
    app.UseAuthorization();
    
    app.MapRazorPages();
    app.MapHub<ChatHub>("/chatHub");//加入這段
    
    app.Run();
    ```
    

---

以下為 用戶端(WinForm)

1. 套件安裝：[SignalRASP.NET Core .NET 用戶端 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/signalr/dotnet-client?view=aspnetcore-7.0&tabs=visual-studio#install-the-signalr-net-client-package)
    ```csharp
    Install-Package Microsoft.AspNetCore.SignalR.Client
    ```
    
2. **建立 SignalR** 
    ```csharp
    HubConnection _connection = new HubConnectionBuilder()
    		                       //需輸入完整網址
                               .WithUrl(Properties.Settings.Default.WebApiUrl + "/BankAccountHistoryByAccountRcv")
                               .Build();
    
    //接收訊息
    _connection.On<string,string>("ReceiveMessage", (string user,string msg) =>
    {
        Console.WriteLine(user);
        Console.WriteLine(msg);
    });
    
    _connection.StartAsync()
    
    string user="";
    string message="";
    
    //呼叫SendMessage
    _connection.InvokeAsync<string,string>("SendMessage", user,message);
    ```