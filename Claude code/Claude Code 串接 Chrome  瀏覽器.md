
安裝 Claude in Chrome 官方擴充功能的完整步驟如下，整個流程大約需要 3 到 5 分鐘： [1]

## 🛠️ 安裝前置確認

- 帳戶方案：此功能僅支援 Claude 的付費方案用戶（Pro、Max、Team 或 Enterprise），免費版帳戶暫不支援。
- 支援瀏覽器：目前僅支援最新版的 Google Chrome 瀏覽器（不支援手機版）。 [2, 3]

---

## 📥 擴充功能安裝步驟

1. 前往線上商店
    
    - 打開 Google Chrome 瀏覽器，前往 [Chrome 線上應用程式商店的 Claude 頁面](https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn)。
    - _(請認明發行者必須為 Anthropic，以防下載到冒牌的惡意程式)_。 [4, 5]
    
2. 點擊安裝
    
    - 點擊右上角的 「加到 Chrome」 按鈕。
    - 畫面上彈出權限提示視窗時，點擊 「新增擴充程序」 確認。 [1, 6]
    
3. 釘選擴充功能
    
    - 安裝完成後，點擊 Chrome 工具列右上角的 「拼圖圖示（擴充功能）」。
    - 找到 Claude，點擊旁邊的 「圖頂符號（釘選）」，讓 Claude 圖示常駐在網址列右側。 [1, 6]
    
4. 登入您的帳戶
    
    - 點擊工具列上的 Claude 圖示開啟側邊欄。
    - 使用您擁有付費訂閱的 Claude 帳號登入。 [1, 6]
    

---

## 💻 額外步驟：如果你想與 Claude Code（終端機）連線

如果你要讓終端機裡的 Claude Code 操控這個瀏覽器，請完成以下步驟： [7]

1. 確認 Claude Code 版本：在終端機執行 `claude --version`，建議更新至最新版（`claude --update`）。 [8, 9]
2. 啟動 Chrome 整合：在終端機輸入 `claude --chrome` 啟動 Session。
    
    - _注意：首次執行此指令時，系統會自動在後台寫入原生通訊設定檔（Native Messaging Host）_。 [4, 8]
    
3. 重啟 Chrome 瀏覽器：請務必將 Chrome 完全關閉並重新打開，這樣瀏覽器才能成功載入剛才寫入的通訊設定。 [4, 10]
	
	_如果不重啟，Chrome 無法讀取剛才寫入的通訊檔案，連線時會出現 `Chrome extension not detected` 的錯誤。_ [4]
	
4. 驗證連線：在 Claude Code 終端機中輸入 `/chrome`，若顯示 `Chrome integration: Enabled` 即代表連線成功！ [7]

	此時您就可以在終端機直接對 Claude 下達指令，例如 `“幫我打開 localhost:3000 並測試登入流程”` 或 `“檢查目前分頁的 Console 有沒有報錯並直接修復”`。 [6, 7]

這取決於您有沒有將它設定為「預設啟用」。 [1, 2]

基本原則如下：

- 預設情況下：每次啟動新對話（Session）都要輸入。  
    如果您每次都直接輸入 `claude` 啟動，系統就不會載入 Chrome 瀏覽器工具。要使用時就必須改用 `claude --chrome` 啟動，或是進入對話後手動輸入指令 `/chrome` 來開啟連線。 [1, 2]

## 💡 想要一勞永逸？設定「預設啟用（Enabled by default）」

如果您不想每次都手動輸入 `--chrome` 標誌，Anthropic 官方提供了一個能讓您一勞永逸的設定： [1, 3]

1. 透過 `claude --chrome` 進入對話。
2. 在終端機中輸入指令：
    
    ```bash
    /chrome
    ```
    
3. 畫面上會跳出選單，請選擇 「Enabled by default」（預設啟用）。 [1, 4, 5]

設定完成後，未來您只需要像平常一樣輸入 `claude` 啟動終端機，Claude 就會自動在背景連接您的 [Claude in Chrome 擴充功能](https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn?hl=zh-TW)，不需要再補上參數了！ [1, 3]

> ⚠️ 溫馨小提示：  
> 官方文件有提到，將 Chrome 整合設為預設啟用會讓 Claude 每次對話時都必須預先載入瀏覽器相關的工具 Token，因此會微幅增加上下文（Context Window）的 Token 消耗量。如果您非常在意 Token 的使用量，維持每次要用時才輸入 `claude --chrome` 會是更節省的做法。 [1, 4]
