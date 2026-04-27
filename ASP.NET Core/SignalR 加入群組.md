後端中樞：
```C#
public class AmbulanceHub : Hub
{
    // 加入群組（病患頁面載入時呼叫）
    public async Task JoinReferralGroup(int pid)
    {
        await Groups.AddToGroupAsync(Context.ConnectionId, $"referral_{pid}");
    }

    // 離開群組
    public async Task LeaveReferralGroup(int pid)
    {
        await Groups.RemoveFromGroupAsync(Context.ConnectionId, $"referral_{pid}");
    }

    public async Task SendAmbulanceGPS(int ReferralUserId, decimal Lat, decimal Lng)
    {
        // ★ 只廣播給這筆轉診的群組，不影響其他人
        await Clients.Group($"referral_{lastReferralPatient.pid}")
                     .SendAsync("ReceiveAmbulanceGPS", true, $"救護車GPS接收成功：{Lat} / {Lng}");
    }
}

```

前端：
```javascript
//建立連線
var connection = new signalR.HubConnectionBuilder().withUrl("/chatHub").build();

//接收訊息
connection.on("ReceivAmbulanceGPS", function (result, message) {
    console.log("從中樞接收訊息: " + result + " / " + message);
});

//開始連線
connection.start();

//加入群組（病患頁面載入時呼叫）
connection.invoke("JoinReferralGroup", parseInt(pid));

```