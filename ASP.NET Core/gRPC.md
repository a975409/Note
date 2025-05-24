在解決方案下建立以下3種專案：
![[Pasted image 20250524102056.png]]

Client：主控台程式，用來模擬呼叫gRPC服務的用戶端
Server：[ASP.NET](http://asp.net/) Core應用程式，負責實作gRPC服務，承載gRPC服務的應用
Proto：類別庫，用來存放Proto Buffers訊息和gRPC服務的定義

[[Proto]]
[[Server]]
[[Client]]