
```Markdown
## gstack 文件同步原則
**任何 gstack skill（/plan-eng-review、/plan-ceo-review、/context-save 等）產出或更新的規劃文件，都必須同步複製一份到本專案資料夾 `docs/gstack/` 下。**

原因：gstack 預設將產出物存在 `~/.gstack/projects/*/`，換機器或換工作目錄後無法取得。將文件歸檔在專案資料夾內，確保任何環境都能讀到最新版本。

規則：
- gstack 每次產出新文件 → 立即複製到 `docs/gstack/`（或對應子目錄）
- 現有文件被 gstack 更新 → 同步覆寫專案內的對應檔案
- 文件命名：與 gstack 產出的原始名稱保持一致，去除 session hash（例如 `phase1-test-plan.md`，而非 `a9754-master-eng-review-test-plan-20260619-193202.md`）
- 每次同步後，在本檔（CLAUDE.md）的文件清單中新增或更新對應條目

## 程式碼庫探索規則(必須遵守)
1. 日常理解程式碼、定位實作、追蹤呼叫鏈時，優先使用 CodeGraph。
2. 修改程式碼前，先用 CodeGraph 取得相關原始碼和影響範圍。
3. 如果需要架構統計、圖查詢、ADR、跨模組關係分析，再使用 codebase-memory-mcp。
4. 若兩個工具結果不一致，以目前代碼檔案內容為準，並重新同步索引。
5. 不要在 CodeGraph 已經回傳足夠上下文時重複全倉庫 grep。
```
