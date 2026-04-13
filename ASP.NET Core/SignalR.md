
參考資料：
[開始使用 ASP.NET Core SignalR | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/tutorials/signalr?view=aspnetcore-7.0&tabs=visual-studio#create-a-signalr-hub)
[SignalRASP.NET Core .NET 用戶端 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/signalr/dotnet-client?view=aspnetcore-7.0&tabs=visual-studio)

[[伺服器端(ASP.NET Core)與用戶端(WinForm)互相通訊]]
[[群組概念與使用]]

## SignalR 中的使用者

後端：
```C#
public Task SendPrivateMessage(string user, string message)
{
    return Clients.User(user).SendAsync("ReceiveMessage", message);
}
```

(前端)建立加入群組的事件到`ButtonClick`裡，然後傳回給`AddGroup`

```jsx
document.getElementById("addUserBtn").addEventListener("click", function (event) {
    var user = document.getElementById("name").value;
    var message = document.getElementById("message").value;
    connection.invoke("SendPrivateMessage", user,message ).catch(function (err) {
        return console.error(err.toString());
    });
    event.preventDefault();
});
```