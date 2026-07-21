
SQL Server 重啟服務：
 - 停止SQL Server服務：
	net stop MSSQLSERVER

- 啟用SQL Server服務：
	net start MSSQLSERVER


確認資料庫版本：
```sql
SELECT @@VERSION;
```

[[SQL Server 如何進入單一使用者模式]]
[[SQL Server 1433 Port 衝突完整解決總結(Docker使用Hyper-V)]]
[[SQL Server 1433 Port 衝突完整解決總結(Docker使用WSL2)]]
[[何謂MARS]]