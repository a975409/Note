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