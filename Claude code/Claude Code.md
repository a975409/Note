
### 環境架設
1. 安裝node.js、git
2. 全域安裝套件：
```shell
npm install -g @anthropic-ai/claude-code
```

https://www.npmjs.com/package/claude-code

2. 確認是否安裝成功(顯示版本)：
![[Pasted image 20260511225427.png|447]]


3. 安裝vs code套件：
![[Pasted image 20260511230911.png]]

4. 進入vs code的使用者設定檔：Preferences:open user setting
![[Pasted image 20260511231039.png]]

5. 新增 "claudeCode.useTerminal": true 設定：
![[Pasted image 20260511231131.png]]

### 常用指令(git bash)

1. 查看當前目錄：
```bash
pwd
```

2. 創建目錄：
```shell
mkdir [路徑或目錄名稱]
```

3. 切換目錄：
```shell
cd [路徑或目錄名稱]
```

4. 清除 Terminal 畫面：
```shell
clear
```

5. 進入 claude功能：
```shell
claude
```

![[Pasted image 20260511234552.png]]

每進行一次spec的開發，需下的Prompt如下：
1. 閱讀需求說明，有問題時提出，無問題時提出規劃，都寫在這個檔案裡做討論
2. 我已回答，請先確認，先不要寫程式，一樣都寫在這個檔案裡做討論
3. 我已回答，請先確認，有討論事項，寫在這個檔案裡做討論
4. 可以往下執行，你想要一口氣做完，還是分批

[讓 AI 助手透過 CLAUDE.md 理解開發流程](https://ithelp.ithome.com.tw/articles/10391730)