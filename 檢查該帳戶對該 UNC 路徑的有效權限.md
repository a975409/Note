可以！使用 PowerShell 測試是非常聰明且快速的做法。

但因為我們要測試的是**「IIS 應用程式集區（AppPool）的特定身分」**是否有權限，而不是您目前登入的系統管理員身分，所以我們不能直接打開 PowerShell 執行。

我們必須利用 Windows 的 `psexec` 工具，或者使用 PowerShell 內建的 `Start-Process`，**模擬該特定 Windows 帳戶的身分**來執行測試指令。

以下提供兩種最實用的 PowerShell 測試方法：

---

### 方法一：在 PowerShell 中「模擬該特定帳戶」進行讀寫測試（最推薦）

這個方法不需要安裝任何工具。您可以用系統管理員身分開啟 PowerShell，然後執行以下腳本。它會彈出視窗要求輸入該特定帳戶的密碼，並在背景以該帳戶身分嘗試寫入 UNC 路徑。

請在 PowerShell 中執行以下指令：

```powershell
# 1. 設定您要測試的 UNC 網路路徑（請替換成實際的 UNC 路徑，不要用 Z:\）
$uncPath = "\\您的伺服器IP或名稱\共享資料夾名稱\test_powershell.txt"

# 2. 設定您 IIS 應用程式集區所使用的特定 Windows 帳戶（格式：網域\帳戶 或 電腦名\帳戶）
$username = "DOMAIN\YourAppPoolUser" 

# 3. 取得憑證（執行後會彈出視窗請您輸入該帳戶的密碼）
$credential = Get-Credential -UserName $username -Message "請輸入 IIS 應用程式集區帳戶的密碼以進行權限測試"

# 4. 模擬該帳戶身分執行寫入與讀取測試
Start-Process powershell -Credential $credential -ArgumentList "-NoProfile -Command `
    try {
        '[成功] 這是由 PowerShell 模擬 IIS 帳戶寫入的測試檔案' | Out-File -FilePath '$uncPath' -ErrorAction Stop;
        `$content = Get-Content -Path '$uncPath' -ErrorAction Stop;
        Write-Host '=== 測試結果 ===' -ForegroundColor Green;
        Write-Host '成功寫入並讀取檔案！內容：' `$content -ForegroundColor Green;
    } catch {
        Write-Host '=== 測試結果 ===' -ForegroundColor Red;
        Write-Host '存取失敗！錯誤原因：' `$_.Exception.Message -ForegroundColor Red;
    }
    Read-Host '按任意鍵結束...'"
```

**💡 執行結果：**
執行後會彈出一個新的 PowerShell 視窗。
* 如果顯示 **綠色的成功訊息**，代表該帳戶確實有權限透過網路存取該路徑。
* 如果顯示 **紅色的錯誤訊息**（例如 Access is denied），代表權限設定有問題。

---

### 方法二：直接檢查該帳戶對該 UNC 路徑的有效權限（Effective Permissions）

如果您不想輸入密碼，只想在 PowerShell 中直接查詢該特定帳戶對該網路資料夾有沒有讀寫權限，可以使用 `Get-Acl` 指令：

```powershell
# 請替換為您的 UNC 路徑與帳戶名稱
$uncPath = "\\您的伺服器IP或名稱\共享資料夾名稱"
$targetUser = "DOMAIN\YourAppPoolUser"

# 取得該路徑的安全性清單 (ACL)
$acl = Get-Acl -Path $uncPath

# 篩選出該特定使用者的權限設定
$acl.Access | Where-Object { $_.IdentityReference -like "*$targetUser*" } | Format-Table IdentityReference, AccessControlType, FileSystemRights
```

**💡 執行結果：**
這會列出該帳戶在該資料夾擁有的權限。您需要確認 `FileSystemRights` 欄位中是否有包含 **`Modify`**、**`Write`** 或 **`FullControl`**。

---

### ⚠️ 再次提醒
測試成功後，請務必確認您的 Web 應用程式程式碼中，檔案儲存路徑是寫 **`\\Server\Share\file.txt`**（UNC 路徑），而**絕對不能**寫 `Z:\file.txt`，因為 IIS 執行時是抓不到 `Z:` 磁碟機代號的！