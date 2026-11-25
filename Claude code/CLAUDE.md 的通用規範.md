# Project Instructions

## 0. 通用原則

- 一律使用**繁體中文**回覆（程式碼、指令、識別字除外）。
- **子代理規範**：使用 `Task` 工具派發任何 Subagent 時，必須在提示詞中明確要求其遵守「繁體中文回覆」與「優先使用 CodeGraph 檢索」的規則。

---

## 1. 程式碼檢索

| 情境                                  | 使用工具                |
| ------------------------------------- | ----------------------- |
| 日常導航、定位實作、追蹤呼叫鏈        | **CodeGraph**（優先）   |
| 修改前的影響評估                      | **CodeGraph**           |
| 架構統計、圖查詢、ADR、跨模組關係分析 | **codebase-memory-mcp** |

- **優先序**：codebase-memory 的 hook（SessionStart／SubagentStart）會自動注入「結構性探索請用 `search_graph`、`trace_path`、`get_code_snippet`」的路由提示；與上表衝突時**以上表為準**——日常導航、定位實作、追蹤呼叫鏈、修改前影響評估一律先用 CodeGraph，codebase-memory 只用於上表所列的架構統計、圖查詢、ADR、跨模組分析。該 hook 的覆蓋率檢查（`check_index_coverage`）只在實際使用 codebase-memory 時才需遵守。
- 工具結果與實際檔案內容不一致時，以**目前檔案內容**為準。
- CodeGraph 不索引的內容（設定檔、文件、i18n）可直接 Read / Grep。
- 只使用當前工作目錄的獨立索引，不要混用其他專案的索引。
- 修改前先說明**影響範圍**（會動到的檔案、受影響的呼叫端）。

---

## 2. 程式實作規範（Superpowers 工作流）

### 2-1 新需求開發全生命週期流程

1. **規劃階段（Plan Mode / Opus 全程操刀）**：
   `superpowers:brainstorming`（多輪釐清需求與技術邊界，將架構規格 Spec 寫入原生計畫檔）
2. **實作階段（核准退出 Plan Mode / 切回 Sonnet 接手）**：
   `using-git-worktrees`（跨模組或高風險功能必用；小型單檔可跳過） → **落地 Spec 規格檔** → `superpowers:writing-plans`（由 Sonnet 拆解 2-5 分鐘 TDD 微任務） → `subagent-driven-development` → `verification-before-completion`（**涉及 UI 必依 2-7 實際跑畫面驗證**） → `finishing-a-development-branch`

### 2-2 問題修正流程

`systematic-debugging` → `test-driven-development`（先寫重現測試再修復） → `verification-before-completion`（**涉及 UI 必依 2-7 實際跑畫面驗證**）

### 2-3 模型分配與子代理路由（Max 方案額度最佳化）

派發 Subagent 時，**必須**明確傳入 `model` 參數，禁止無條件繼承主對話模型：

- **實作型 Subagent（Implementer）**：一律指定 `model: "sonnet"`，嚴格依計畫與 TDD 實作。
- **任務審查（Task Reviewer，規格合規＋程式碼品質合併為單一子代理，一次交回兩個判決）**：一般改動指定 `model: "sonnet"`；僅在涉及架構變更、並發控制、安全性或核心演算法時，才指定 `model: "opus"`。複審（範圍限定於修正 diff）與整支分支審查同樣套用此條件。
- **卡關排錯升級（Escalation）**：當某個實作 Subagent 在修復迴圈中連續失敗超過 3 次時，改派 `model: "opus"` 接手診斷。
- **本節為準**：模型選擇一律以本節為準；`superpowers:subagent-driven-development` 等技能內的模型建議（例如「最終審查用最強模型」「複審用中階模型」）只當參考，與本節衝突時以本節為準，**不得**把技能的建議當成選模型的理由；不因「最終審查」「複審」等名稱就自動升 `opus`。
- **回報依據**：派發每個 Subagent 前先對照本節判斷模型，回報時說明依據是哪一條（例如「並發控制」）。

### 2-4 日常操作與模式切換規範（OpusPlan 節奏）

1. **規劃期（Opus 全程操刀）**：開發新功能前，按 `Shift + Tab` 開啟 **Plan Mode**，依 Hook 指示執行 `superpowers:brainstorming` 釐清需求與現有架構；定案後將包含「背景目標、架構決策、影響檔案、驗收標準」的完整 **設計規格書（Spec）** 寫入原生計畫檔（`~/.claude/plans/`），隨後等待使用者核准並退出 Plan Mode（**嚴禁在 Plan Mode 內呼叫 writing-plans**）。
2. **實作期交接（Sonnet 接手，主進程轉為調度指揮官）**：計畫核准並退出 Plan Mode 後，**主進程嚴禁直接手動調用 Edit/Write 工具修改業務程式碼**，必須依序執行：
   - **隔離環境**：若需使用 Worktree，優先呼叫 `superpowers:using-git-worktrees` 建立並切入乾淨的工作區。
   - **落地 Spec 規格**：在目標工作區下，將剛核准的原生計畫檔備份至 `docs/superpowers/specs/YYYY-MM-DD-<功能簡稱>-spec.md`。
   - **Spec 審閱簡報**：Spec 落地後，呼叫 `spec-plan-html-review` 產出視覺化審閱簡報（見 2-5）。
   - **⛔ Spec 確認關卡**：Spec（md＋HTML）產出後**必須停下**，回報 md 與 HTML 的完整路徑，等使用者明確回覆同意後，才可進入 `writing-plans`；未獲同意前不得往下做任何事（見 2-6）。
   - **微任務拆解（Writing Plans）**：呼叫 `superpowers:writing-plans`，依據該 Spec 拆解出嚴格遵守 TDD 的 2–5 分鐘微任務實作計畫，存為 `docs/superpowers/plans/YYYY-MM-DD-<功能簡稱>-plan.md`。\r\n   - **Plan 審閱簡報**：Plan 存檔後，再呼叫 `spec-plan-html-review` 產出該 Plan 的簡報（見 2-5），並向使用者回報兩份簡報的完整路徑。
   - **⛔ Plan 確認關卡**：Plan（md＋HTML）產出後**必須停下**，等使用者明確回覆同意後，才可呼叫 `subagent-driven-development` 開工；未獲同意前不得開始實作（見 2-6）。
   - **正式開工**：呼叫 `superpowers:subagent-driven-development` 派發 Sonnet Subagent 嚴格依該微任務計畫檔執行編碼、TDD 與雙重審查。
3. **升級時機**：僅在架構級審查或子代理連續排錯失敗 3 次以上時，才臨時派發或切換至 `opus` 模型。

### 2-5 Spec／Plan 審閱簡報（`spec-plan-html-review`）

- **觸發時機**：`docs/superpowers/specs/*.md` 或 `plans/*.md` 新寫成或有實質修改後；使用者要求某份文件的視覺化／HTML 版時。
- **做法**：由 skill 指示讀取文件、整理成 `brief.json`，再以腳本渲染成 HTML，輸出於 `docs/superpowers/html/briefs/`。Spec 以「需求×端對照、角色×功能矩陣、前後流程」為主；Plan 以「影響範圍圖、階段切分、風險」為主。
- **原則**：`.md` 仍是唯一來源，subagent 與 `writing-plans` 只讀 `.md`；簡報只給人審閱，不手改 HTML，修改後回到 `.md` 與 `brief.json` 重新渲染。
- **版控**：`docs/superpowers/html/` 是產物，不主動 commit；是否忽略由使用者決定。

### 2-6 Spec／Plan 確認關卡（強制停下）

- **Spec 產出後必須停下**：Spec（md 與 HTML 簡報）完成後，回報完整路徑並請使用者確認，**等使用者明確同意**才可進入 Plan 階段。
- **Plan 產出後必須停下**：Plan（md 與 HTML 簡報）完成後同樣回報路徑並請使用者確認，**等使用者明確同意**才可進入實作（派發 Subagent、寫任何程式碼）。
- **不得自行視為同意**：沉默、先前的整體授權（如「核准計畫」、「跑到完成」）都不等於對這份 Spec／Plan 文件的同意；使用者要求修改時，改完 md 並重新渲染 HTML，再次停下等待確認。
- **本節優先**：與 `brainstorming`、`writing-plans` 等技能中「自動銜接下一步」或 auto-memory 的「核准後一路執行到完成」衝突時，以本節為準；同意之後的實作階段才適用一路執行的偏好。

### 2-7 UI / 畫面調整驗證規範（嚴禁腦補完工）

- **適用對象**：凡涉及 Web 網頁（HTML/CSS/JS/Vue/React 元件）、App（Flutter/React Native/原生畫面）、或任何使用者介面（UI/UX）的排版、樣式與互動流程修改。
- **實際畫面走訪**：
  - 程式碼修改完成**不等於完工**，嚴禁僅憑「建置成功」或「單元測試通過」即交付。
  - 必須實際啟動專案／Dev Server／模擬器，透過工具（如 Playwright 截圖、瀏覽器 MCP、或真實執行畫面）**親眼確認渲染結果與視覺呈現**。
  - 核心驗收點：版面是否跑版、元件樣式是否破圖、響應式斷點（RWD）表現、動態互動狀態（Hover/Active/Loading/Disabled）與錯誤訊息呈現。
- **即時修正閉環（Fix-in-place）**：
  - 畫面驗收時只要發現視覺瑕疵、跑版或互動異常，**必須當下直接修正**並再次執行畫面驗證。
  - 直到實際畫面與互動行為完全符合預期，該任務才視為完成，方可向使用者回報或進入後續流程。

---

## 3. SQL 資料表設計規範

- 新增或修改資料表、欄位、索引、migration 前，先呼叫 `sql-table-design` skill。
