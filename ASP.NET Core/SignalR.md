
參考資料：
[開始使用 ASP.NET Core SignalR | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/tutorials/signalr?view=aspnetcore-7.0&tabs=visual-studio#create-a-signalr-hub)
[SignalRASP.NET Core .NET 用戶端 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/signalr/dotnet-client?view=aspnetcore-7.0&tabs=visual-studio)

[[伺服器端(ASP.NET Core)與用戶端(WinForm)互相通訊]]
[[群組概念與使用]]
[[SignalR 加入群組]]
## 注意事項

### ⚠️ Hub 的生命週期是 Transient

Hub 每次連線都會被**重新建立**，因此：

|Service 生命週期|是否可注入 Hub|說明|
|---|---|---|
|`Transient`|✅ 安全|每次都新建，沒問題|
|`Scoped`|✅ 安全|Hub 本身就是 Scoped 範圍|
|`Singleton`|✅ 可以，但需注意|Singleton 中若有狀態需注意執行緒安全|

## 基本使用方式

1. 後端建立 SignalR 中樞：
```C#
using Microsoft.AspNetCore.SignalR;

namespace SignalRChat.Hubs
{
    public class ChatHub : Hub
    {
        public async Task SendMessage(string user, string message)
        {
	        //回傳訊息至用戶端
            await Clients.All.SendAsync("ReceiveMessage", user, message);
        }
    }
}
```
>連線的用戶端可以呼叫 `SendMessage` 方法將訊息傳送至所有用戶端

2. 新增 SignalR 用戶端程式代碼：
```html
<script src="~/js/signalr/dist/browser/signalr.js"></script>
<script src="~/js/chat.js"></script>
```

chat.js：
```javascript
"use strict";

//建立連線
var connection = new signalR.HubConnectionBuilder().withUrl("/chatHub").build();

document.getElementById("sendButton").disabled = true;

//啟動連線
connection.start().then(function () {
    document.getElementById("sendButton").disabled = false;
}).catch(function (err) {
    return console.error(err.toString());
});

//新增處理常式至連線物件，可從中樞接收訊息
connection.on("ReceiveMessage", function (user, message) {
    var li = document.createElement("li");
    document.getElementById("messagesList").appendChild(li);
    li.textContent = `${user} says ${message}`;
});

//新增提交按鈕的點擊事件，觸發後會將訊息傳送至中樞。
document.getElementById("sendButton").addEventListener("click", function (event) {
    var user = document.getElementById("userInput").value;
    var message = document.getElementById("messageInput").value;
    
    //將訊息傳送至中樞。
    connection.invoke("SendMessage", user, message).catch(function (err) {
        return console.error(err.toString());
    });
    event.preventDefault();
});

```
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