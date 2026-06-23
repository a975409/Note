## 參考連結
[CLAUDE.md 實戰技巧整理：10 個讓 Claude Code 更聰明的進階設定](https://haosquare.com/claude-code-claude-md-advanced-tips/)
[Claude Code Skills：讓 AI 變身專業工匠](https://kaochenlong.com/claude-code-skills)
[Claude Code 利用 Event-Driven Hooks 打造自動化開發大腦](https://ganhua.wang/claude-code-event-driven-hooks)
[Claude Code 教學｜從安裝、設定到第一個任務的完整實作筆記（2026 版）](https://israynotarray.com/ai/20260128/374686091/#%E5%89%8D%E8%A8%80)
[你的 Claude Code Context 是不是都被吃光了？AGENTS.md 與 Context 管控全攻略](https://israynotarray.com/ai/20260409/1057131696/)
[讓 AI 助手透過 CLAUDE.md 理解開發流程](https://ithelp.ithome.com.tw/articles/10391730)
[[三種 CLAUDE.md 記憶類型]]
[[gstack 結合 superpowers 開發專案]]

## 套件下載(skills、Hook、Agent)
[AI Templates](https://www.aitmpl.com/)
 [【Claude Code】應加入 Agent 的 20 個 Skills｜skills.sh 活用指南](https://codelove.tw/@tony/post/am270q)
 [[dotNet 官方团队发布的 .NET Agent Skills，告别 AI 编程幻觉！]]
[dotnet-skills](https://github.com/managedcode/dotnet-skills)

## skill下載清單
[gstack](https://github.com/garrytan/gstack)
[[gsd-core]]
[superpowers](https://github.com/obra/superpowers)
[Frontend Design](https://www.aitmpl.com/component/skill/creative-design/frontend-design)
[官方dotnet-skills](https://github.com/dotnet/skills)

## Skills 跟其他機制有什麼不同？

如果你用過 Claude Code 一段時間，應該會發現有好幾種方法可以「客製化」Claude 的行為。Skills 跟其他機制有什麼差別？什麼時候該用哪一種？我整理一個比較表：

| 機制              | 觸發方式                               | 持久性       | 內容類型          | 載入時機                         | 可包含程式碼         | 適用場景                        |
| --------------- | ---------------------------------- | --------- | ------------- | ---------------------------- | -------------- | --------------------------- |
| Skills          | Claude 自動判斷何時啟用（根據 description）    | 跨對話、跨專案可用 | 程序性知識 + 可執行腳本 | 動態載入（Progressive Disclosure） | 是              | 重複性專業工作流程、需要 Claude 主動判斷的情境 |
| Custom Commands | 使用者輸入 `/command`（也可引導 Claude 代為呼叫） | 專案或個人層級   | Prompt 範本     | 使用者觸發時載入                     | 否（單一 prompt 檔） | 固定格式的重複操作、使用者明確知道要做什麼的情境    |
| MCP             | 工具呼叫                               | 啟動時載入     | 外部服務連接        | 按需呼叫                         | 是（server 端）    | 連接外部 API、資料庫、檔案系統           |
| Subagents       | Claude 自動委派或手動啟動                   | 任務期間      | 獨立 AI 實例      | 需要時建立                        | 是              | 需要獨立 context、平行處理、專門化任務     |

### 環境架設
1. 安裝node.js、git
2. 全域安裝[套件](https://www.npmjs.com/package/claude-code)：
```shell
npm install -g @anthropic-ai/claude-code
```

2. 確認是否安裝成功(顯示版本)：
![[Pasted image 20260511225427.png|447]]

3. 安裝vs code套件：
![[Pasted image 20260511230911.png]]

4. 進入vs code的使用者設定檔：Preferences:open user setting
![[Pasted image 20260511231039.png]]

5. 新增 "claudeCode.useTerminal": true 設定：
![[Pasted image 20260511231131.png]]

### 常用指令(git bash)

1. 啟用claude code
```shell
# GSD 設計為自動化運行，建議加上此參數
claude --dangerously-skip-permissions
```

2. 查看當前目錄：
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

6. 安裝外部套件：
```bash
plugin
```

執行後會出現以下畫面：
![[Pasted image 20260521214455.png]]


![[Pasted image 20260520223428.png]]

