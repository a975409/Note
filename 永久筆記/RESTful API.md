有以下幾種建議的設計方式：
1. 傳遞或回傳格式使用JSON
2. 以同一個URL，但以不同 HTTP 動詞(GET、POST、PUT、DELETE等等)區隔要執行的動作
3. 無狀態。(Stateless)
	>RESTful 的狀態，意即 HTTP 的請求狀態，一般 Web 服務中，Server 端和 Client 端交互的資訊，會存在 Server 端的 Session (例如：已登入狀態)，在 Client 端再次發送請求的時候，Server 端透過保存在 Server 端的 Session，去執行 request。無狀態的意思，即 **Client 端自行保存狀態**，在請求 Server 的時候，一併附上給 Server 端，Server 端無保存 Client 端的狀態資訊。
	
4. 