
1. 指定IIS：
![[Pasted image 20250915160122.png]]

2. 設定如下：
![[Pasted image 20250915160215.png]]

> 應用程式URL，如果使用預設port號，就不用寫上port號，如https(80、8080)、https(443)，另外補充一點，各個開發專案的port不能重複；IIS的實際資料夾路徑都指向開發專案路徑
> 環境變數：ASPNETCORE_ENVIRONMENT=Development

3. 設定發布檔->新增設定轉換：
![[Pasted image 20250915160939.png]]

4. 會產生對應的config：
![[Pasted image 20250915162839.png]]

