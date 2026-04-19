針對 IIS 的應用程式集區（Application Pool）`GovDocTestWebSite` 設定資料夾權限，主要是給該應用程式集區所使用的身份帳號（通常是 ApplicationPoolIdentity）對指定資料夾的「讀取/寫入」或「修改」權限。

---

## 步驟說明（Windows Server / Windows 環境）

### 1. 確認應用程式集區使用的身份帳號

IIS 預設應用程式集區身份是 `ApplicationPoolIdentity`，對應的 Windows 帳號格式是：

```
IIS AppPool\<應用程式集區名稱>
```

![[Pasted image 20260414105903.png]]

例如你的應用程式集區名稱是 `GovDocTestWebSite`，則身份帳號是：

```
IIS AppPool\GovDocTestWebSite
```

---

### 2. 設定資料夾權限給該身份帳號

假設你要給網站根目錄下的 `ApiLogs` 資料夾設定權限：

1. 在檔案總管中，找到該資料夾（例如 `C:\inetpub\wwwroot\GovDocTestWebSite\ApiLogs`）。
2. 右鍵點擊該資料夾，選擇「內容」。
3. 切換到「安全性」標籤頁。
4. 點擊「編輯」按鈕，然後點「新增」。
5. 在「輸入要選擇的物件名稱」欄位輸入：

   ```
   IIS AppPool\GovDocTestWebSite
   ```

6. 按「檢查名稱」，確認能解析成正確帳號，然後按「確定」。
7. 選擇剛加入的帳號，勾選「修改」（Modify）權限（或依需求勾選「讀取」、「寫入」等權限）。
8. 按「確定」套用設定。

![[Pasted image 20260414105941.png]]

---

### 3. 確認權限生效

- 重新啟動 IIS 應用程式池或網站。
- 測試應用程式是否能成功寫入該資料夾。

---

## 備註

- 如果你用的是自訂身份（非 ApplicationPoolIdentity），請改用該身份的 Windows 帳號設定權限。
- 若找不到「IIS AppPool\GovDocTestWebSite」帳號，請先確認應用程式集區名稱是否正確，且該應用程式集區確實使用預設身份。

---

