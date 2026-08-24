要取得網路磁碟機的 **UNC 網路路徑**（格式通常為 `\\伺服器名稱\共享資料夾`），最快的方法是直接在 Windows 中查詢現有的網路磁碟機對應關係。

以下提供幾種最簡單的查詢方法：

---

### 方法一：使用 PowerShell 查詢（最推薦、最快）

請直接打開您的 PowerShell，輸入以下指令並按下 Enter：

```powershell
Get-SmbMapping | Select-Object LocalPath, RemotePath
```

**💡 輸出結果範例：**
```text
LocalPath RemotePath
--------- ----------
Z:        \\192.168.1.100\SharedFolder
```
在這個例子中，`\\192.168.1.100\SharedFolder` 就是您要找的 **UNC 網路路徑**！

---

### 方法二：使用命令提示字元 (CMD) 查詢

如果您習慣使用 CMD，可以打開 CMD 並輸入：

```cmd
net use
```

**💡 輸出結果範例：**
```text
狀態         本機      遠端                    網路
-------------------------------------------------------------------------------
OK           Z:        \\FileServer\Uploads      Microsoft Windows Network
```
在「遠端 (Remote)」欄位下顯示的 **`\\FileServer\Uploads`** 就是該網路磁碟機的 UNC 路徑。

---

### 方法三：從「本機 (檔案總管)」直接複製

1. 開啟 Windows 的 **檔案總管**，點擊左側的 **「本機」**。
2. 找到您的網路磁碟機（例如 `Z:`）。
3. 它的名稱通常會顯示成類似這樣：
   > `Uploads (\\FileServer) (Z:)` 或是 `SharedFolder (\\192.168.1.100) (Z:)`
4. 括號裡面的 **`\\FileServer`** 或 **`\\192.168.1.100`** 就是伺服器位置。
5. **最快的方法**：直接**點進去**該網路磁碟機，然後點擊視窗上方「路徑列（網址列）」的空白處，路徑列就會自動轉換成 UNC 路徑（例如 `\\192.168.1.100\SharedFolder\`），您就可以直接複製它了！