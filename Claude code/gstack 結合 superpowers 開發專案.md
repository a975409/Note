[gstack 的 Build 是空的，Superpowers 怎麼補上這個缺口](https://heymaibao.com/gstack-superpowers-build-gap/)
[Obra/superpowers 使用指南：用 Skill 做 Coding + Planning](https://university.tenten.co/t/obra-superpowers-skill-coding-planning/2275)
[gstack 完整解析：YC 總裁如何每天利用 AI 撰寫 10,000 行程式碼](https://youmind.com/zh-TW/blog/gstack-garry-tan-claude-code-workflow-guide)
[gstack：全流程 AI 代理團隊的自動化實踐 (完整技能解析)](https://vocus.cc/user/@aibasil)

把 gstack 的規劃和審查接上 Superpowers 的構建，完整流程是這樣：
1. **gstack /office-hours** → 定義問題，產出設計文件。你說要做日曆 app，它可能會說你其實在做 AI 行政助理
2. **gstack /plan-ceo-review** → 質疑方向，確認範圍。四種模式：擴展、選擇性擴展、維持範圍、縮減
3. **gstack /plan-eng-review** → 鎖定技術架構，畫資料流圖，列邊界情況
4. gstack /design-consultation → 從零開始建立完整的設計系統，最終會產生`DESIGN.md`
5. gstack /design-html → 參照`DESIGN.md`所規範的樣式和業務需求，產生已定案的html靜態畫面(尚未串接API)，此畫面是要提供給人工確認的
6. gstack /plan-design-review → 對每個設計維度進行 0-10 分的評分，解釋 10 分的標準，然後調整方案以達到該標準。
7. **Superpowers/writing-plans** → 格式橋接，把 gstack 設計文件轉成`Superpowers`實作計畫，這樣`subagent-driven-development**`才能依據計畫執行
8. **Superpowers/subagent-driven-development** → 子代理逐任務實作 + 雙重審查
9. **gstack /review** → 結構性程式碼審查
10. **gstack /qa** → 開真實瀏覽器做 QA 測試
11. **gstack /ship** → 跑測試、審計覆蓋率、建 PR

>這些不是一堆零散的工具。這些角色按照**思考 → 規劃 → 建立 → 審查 → 測試 → 發布 → 反思**的順序串聯起來，每個階段的輸出都會自動饋送到下一個階段。`/office-hours` 生成的設計文件由 `/plan-ceo-review` 閱讀；`/plan-eng-review` 編寫的測試計畫由 `/qa` 執行；`/review` 發現的錯誤由 `/ship` 驗證是否已修復。

## superpowers整體工作流程

[給 AI 超能力？Superpowers 的設計與取捨](https://kaochenlong.com/ai-superpowers-skills)

把上面這些 skill 串起來，一個典型的開發流程可能會像這樣：
```Text
brainstorming
→ using-git-worktrees
→ writing-plans
→ subagent-driven-development
→ finishing-a-development-branch
```

使用者描述一個想法，`brainstorming` skill 啟動，AI 開始問問題釐清需求。問完之後提出幾個可能的方案，使用者選一個，AI 把設計寫成文件。`using-git-worktrees` skill 啟動，在一個新的 worktree 裡建立隔離的工作環境。`writing-plans` skill 啟動，把設計拆成一堆小任務，每個任務都有完整的指令和程式碼。

`subagent-driven-development` skill 啟動，對每個任務派一個 subagent 去做，做完之後派另外兩個 subagent 做規格審查和品質審查。`test-driven-development` skill 在每個 subagent 裡面運作，確保他們先寫測試再寫 code。

所有任務做完之後，`finishing-a-development-branch` skill 啟動，跑完整的測試，然後問使用者要合併、開 PR、保留分支、還是丟掉這些變更。