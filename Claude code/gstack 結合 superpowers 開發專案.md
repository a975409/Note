[gstack 的 Build 是空的，Superpowers 怎麼補上這個缺口](https://heymaibao.com/gstack-superpowers-build-gap/)
[Obra/superpowers 使用指南：用 Skill 做 Coding + Planning](https://university.tenten.co/t/obra-superpowers-skill-coding-planning/2275)

把 gstack 的規劃和審查接上 Superpowers 的構建，完整流程是這樣：
1. **gstack /office-hours** → 定義問題，產出設計文件。你說要做日曆 app，它可能會說你其實在做 AI 行政助理
2. **gstack /plan-ceo-review** → 質疑方向，確認範圍。四種模式：擴展、選擇性擴展、維持範圍、縮減
3. **gstack /plan-eng-review** → 鎖定技術架構，畫資料流圖，列邊界情況
4. **Superpowers/writing-plans** → 格式橋接，把 gstack 設計文件轉成實作計畫
5. **Superpowers/subagent-driven-development** → 子代理逐任務實作 + 雙重審查
6. **gstack /review** → 結構性程式碼審查
7. **gstack /qa** → 開真實瀏覽器做 QA 測試
8. **gstack /ship** → 跑測試、審計覆蓋率、建 PR

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