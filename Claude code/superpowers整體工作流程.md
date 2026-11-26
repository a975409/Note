[Obra/superpowers 使用指南：用 Skill 做 Coding + Planning](https://university.tenten.co/t/obra-superpowers-skill-coding-planning/2275)
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