
1. 在controller資料夾右鍵→加入→新增sacffold項目，選擇下方圖片選擇的項目：
	![[Untitled 3.png]]
2. 在上一個步驟按下加入後，會出現以下畫面：
	![[Untitled 5.png]]
	這時請指定模型類別、資料內容類別(DBContext)，如果此時尚未新增資料內容類別(DBContext)，就點擊右邊加號按鈕，他就會自己建立了 設定完畢後，按下新增即可
	
3. 將資料庫異動更新至資料庫：
```C#
Add-Migration InitialCreate
Update-Database
```