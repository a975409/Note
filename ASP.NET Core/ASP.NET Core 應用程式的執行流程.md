1. 由使用者發出HTTP Request
2. 透過反向代理(也可以不用)將HTTP Request發送到ASP.NET Core 應用程式
3. 透過 Kestrel Server 把 HTTP Request 發送至 Middleware 
4. 透過 Middleware 依序處理請求
5. 接著運行應用程式的程式碼(Application Code)
6. 再把程式運行結果透過 Middleware 依照倒序處理 Response
7. 最後再透過 Kestrel Server 回傳 HTTP Response

![[Pasted image 20240917190025.png]]

> ASP.NET Core 是依據 OWIN 規格所實作的網站架構
> [開發筆記-OWIN-黑暗執行緒 (darkthread.net)](https://blog.darkthread.net/blog/about-owin/)
