
> 單一使用者模式（Single User Mode）通常用於**資料庫修復、系統資料庫還原、忘記 SA 密碼**等緊急情況。

---

## 方法一：透過啟動參數 `-m`（最常用）

### Step 1 — 停止 SQL Server 服務
```powershell
Stop-Service MSSQLSERVER
```

### Step 2 — 以單一使用者模式啟動
```powershell
# 方法 A：用 net start
net start MSSQLSERVER /m

# 方法 B：指定只允許 SQLCMD 連入（更安全）
net start MSSQLSERVER /m"SQLCMD"
```

### Step 3 — 連入執行操作
```powershell
# 用 SQLCMD 連入
sqlcmd -S localhost -E
```

### Step 4 — 完成後恢復正常模式
```powershell
Stop-Service MSSQLSERVER
Start-Service MSSQLSERVER
```

---

## 方法二：透過 SQL Server 設定管理員（GUI）

1. 開啟 **SQL Server Configuration Manager**
2. 左側選 `SQL Server 服務`
3. 右鍵點選 `SQL Server (MSSQLSERVER)` → **內容**
4. 切換到 **啟動參數** 頁籤
5. 在參數欄位輸入 `-m` → 點 **新增** → **套用**
6. 重新啟動服務
7. ⚠️ 操作完成後記得**移除 `-m` 參數**再重啟！

---

## 方法三：直接執行 sqlservr.exe

```powershell
# 切換到 SQL Server 安裝目錄
cd "C:\Program Files\Microsoft SQL Server\MSSQL16.MSSQLSERVER\MSSQL\Binn"

# 直接以單一使用者模式執行
sqlservr.exe -m
```

---

## ⚠️ 注意事項

| 項目 | 說明 |
|---|---|
| 連線數限制 | 只允許**一個連線**，第二個連線會被拒絕 |
| SSMS 風險 | SSMS 開啟時會佔用唯一連線，導致你自己無法連入，建議用 `SQLCMD` |
| Agent 服務 | SQL Server Agent 也會搶連線，建議先停止 |
| 操作完成 | 務必移除 `-m` 參數並重啟，否則永遠只有單一連線 |

---

## 💡 常見使用情境

```sql
-- 進入後常見操作範例

-- 1. 重設 SA 密碼
ALTER LOGIN sa WITH PASSWORD = 'NewPassword123!', UNLOCK;
ALTER LOGIN sa ENABLE;

-- 2. 將自己加入 sysadmin
EXEC sp_addsrvrolemember 'DOMAIN\YourAccount', 'sysadmin';

-- 3. 修復系統資料庫
-- 通常搭配 -T3608 旗標使用
```

---

> 💡 **建議使用方法一 + `/m"SQLCMD"`**，限制只有 SQLCMD 可以連入，避免其他程序搶走唯一連線！