```shell
❯ gsd:config

● Skill(/gsd:config)
Successfully loaded skill · 4 tools allowed

沒有附加旗標，執行 settings 工作流程。先讀取工作流程檔案。

  Searched for 2 patterns, read 1 file, ran 1 shell command

先確認 gsd-tools 位置並載入目前的設定。

  Searched for 1 pattern, read 1 file, ran 2 shell commands

目前設定已讀取。開始互動式設定流程，目前 model_profile 為 adaptive。

第一步：模型 Profile

● User answered Claude's questions:
· 各 Agent 使用哪個模型 Profile？ → Adaptive (Recommended)

model_profile = adaptive，跳過 Q2。

Planning 區段

● User declined to answer questions
· 要啟用 Plan Researcher？（規劃前先研究領域） (Yes (Recommended) / No)
· 要啟用 Plan Checker？（執行前驗證計劃） (Yes (Recommended) / No)
· 要啟用 Plan Drift Guard？（驗證計劃中引用的符號是否存在於原始碼） (Yes (Recommended) / No)
· 要啟用 Pattern Mapper？（在研究與規劃中間對映新檔案至現有程式碼淣源） (Yes (Recommended) / No)

❯ gsd-config
⧉ Selected 1 lines from .planning\config.json in Visual Studio Code

● Skill(/gsd:config)
Successfully loaded skill · 4 tools allowed

目前設定已在 session 中，直接進入互動設定。

模型 Profile

● User answered Claude's questions:
· 各 Agent 使用哪個模型 Profile？（目前：adaptive） → Adaptive (Recommended)

Planning 區段

● User answered Claude's questions:
· Spawn Plan Researcher？（規劃前先研究領域，目前：On） → Yes (Recommended)
· Spawn Plan Checker？（執行前驗證計劃，目前：On） → Yes (Recommended)
· Enable Plan Drift Guard？（核實計劃中符號是否存在於原始碼，目前：On） → Yes (Recommended)
· Enable Pattern Mapper？（研究與規劃之間對映新檔案至現有淣源，目前：On） → Yes (Recommended)

● User answered Claude's questions:
· Enable Nyquist Validation？（規劃階段研究測試覆蓋，目前：On） → Yes (Recommended)
· Enable UI Phase？（為前端階段產生 UI-SPEC.md 設計合約，目前：On） → Yes (Recommended)
· Enable UI Safety Gate？（規劃前端階段前提示執行 /gsd-ui-phase，目前：On） → Yes (Recommended)
· Enable AI Phase？（AI 階段的框架選擇 + 評估策略，目前：Off） → No (Recommended)

Execution 區段（code_review 目前為 On，故包含深度問題）

● User answered Claude's questions:
· Spawn Execution Verifier？（驗證階段完成，目前：On） → Yes (Recommended)
· Enable TDD Mode？（RED/GREEN/REFACTOR 門第順序，目前：Off） → No (Recommended)
· Enable Code Review？（/gsd-code-review 指令，目前：On） → Yes (Recommended)
· Code Review 深度？（/gsd-code-review 預設深度，目前：standard） → Standard (Recommended)

Docs & Output 區段

● User answered Claude's questions:
· Enable UI Review？（自動模式的視覺品質稽核，目前：On） → Yes (Recommended)
· Auto-advance pipeline？（discuss → plan → execute 自動串接，目前：Off） → No (Recommended)
· Commit .planning/ 至 git？（目前：On） → Yes (Recommended)
· Skip discuss-phase？（自動模式下跨過討論階段，目前：Off） → No (Recommended)

Features 區段

● User answered Claude's questions:
· Enable Intel？（/gsd-map-codebase --query 可查詢程式碼智慧，目前：Off） → No (Recommended)
· Enable Graphify？（/gsd-graphify 專案知識圖譜，目前：Off） → No (Recommended)
· Use git worktrees 進行並行 agent 隔離？（目前：On） → Yes (Recommended)

Model & Pipeline + Misc 區段

● User answered Claude's questions:
· Git branching strategy？（目前：none） → None (Recommended)
· milestone 完成時建立 git tag？（目前：On） → Yes (Recommended)
· Enable context window warnings？（context 快滿時提示，目前：On） → Yes (Recommended)
· 提問前先搜尋最佳實務？（新專案與 discuss-phase 內網搜尋，目前：Off） → No (Recommended)

所有設定均已收集（全部保持現有值）。更新 config 並詢問全域預設值。

● User answered Claude's questions:
· 將這些設定儲存為所有新專案的預設定義？ → Yes

config.json 所有值均與現有設定一致，不需要修改。接著寫入全域預設值到 ~/.gsd/defaults.json。

  Ran 1 shell command

● Write(C:/Users/kentsai/.gsd/defaults.json)
Wrote 34 lines to ..\..\..\.gsd\defaults.json
{
  "mode": "interactive",
  "granularity": "standard",
  "model_profile": "adaptive",
  "commit_docs": true,
  "branching_strategy": "none",
  "quick_branch_template": null,
  "workflow": {
    "research": true,
    "plan_check": true,
… +24 lines

設定完成。以下是最終摘要：

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

| 設定                  | 值                |
|-----------------------|-------------------|
| Model Profile         | adaptive          |
| Plan Researcher       | On                |
| Plan Checker          | On                |
| Pattern Mapper        | On                |
| Plan Drift Guard      | On                |
| Nyquist Validation    | On                |
| UI Phase              | On                |
| UI Safety Gate        | On                |
| AI Integration Phase  | Off               |
| Execution Verifier    | On                |
| TDD Mode              | Off               |
| Code Review           | On                |
| Code Review Depth     | standard          |
| UI Review             | On                |
| Auto-Advance          | Off               |
| Commit Docs           | On                |
| Skip Discuss          | Off               |
| Worktrees             | On                |
| Intel                 | Off               |
| Graphify              | Off               |
| Git Branching         | None              |
| Git Tagging           | On                |
| Context Warnings      | On                |
| Research Qs           | Off               |
| Saved as Defaults     | Yes               |

這些設定將套用於未來的 /gsd-plan-phase 和 /gsd-execute-phase 執行。

快速指令：
- /gsd-config --advanced     → 進階調整（plan bounce、timeout、model policy 等）
- /gsd-config --integrations → API 金鑰（Brave/Firecrawl/Exa）、code review CLI 路由
- /gsd-config --profile <name> → 快速切換 model profile
```

