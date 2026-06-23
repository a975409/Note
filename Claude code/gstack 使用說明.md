
[gstack](https://github.com/garrytan/gstack)
[gstack 的 Build 是空的，Superpowers 怎麼補上這個缺口](https://heymaibao.com/gstack-superpowers-build-gap/)

gstack 是一個流程，而不是工具的集合。各項技能的執行順序與迭代衝刺的順序一致：
>**思考 → 計劃 → 建構(由Superpowers執行) → 審查 → 測試 → 發布 → 反思**

## 執行順序

把 gstack 的規劃和審查接上 Superpowers 的構建，完整流程是這樣：
1. **gstack /office-hours** → 定義問題，最終會產出設計文件。例如你說要做日曆 app，它可能會說你其實在做 AI 行政助理
2. **gstack /plan-ceo-review** → ，提交設計文件，質疑方向，確認範圍。四種模式：擴展、選擇性擴展、維持範圍、縮減
3. **gstack /plan-eng-review** → 鎖定技術架構，畫資料流圖，列邊界情況
4. 建構階段由`Superpowers`執行，請參照[[gstack 結合 superpowers 開發專案]]
5. **gstack /review** → 結構性程式碼審查
6. **gstack /qa** → 開真實瀏覽器做 QA 測試
7. **gstack /ship** → 跑測試、審計覆蓋率、建 PR

## 上述指令的實際運作方式

``` markdown
You:    I want to build a daily briefing app for my calendar.
You:    /office-hours
Claude: [asks about the pain — specific examples, not hypotheticals]

You:    Multiple Google calendars, events with stale info, wrong locations.
        Prep takes forever and the results aren't good enough...

Claude: I'm going to push back on the framing. You said "daily briefing
        app." But what you actually described is a personal chief of
        staff AI.
        [extracts 5 capabilities you didn't realize you were describing]
        [challenges 4 premises — you agree, disagree, or adjust]
        [generates 3 implementation approaches with effort estimates]
        RECOMMENDATION: Ship the narrowest wedge tomorrow, learn from
        real usage. The full vision is a 3-month project — start with
        the daily briefing that actually works.
        [writes design doc → feeds into downstream skills automatically]

You:    /plan-ceo-review
        [reads the design doc, challenges scope, runs 10-section review]

You:    /plan-eng-review
        [ASCII diagrams for data flow, state machines, error paths]
        [test matrix, failure modes, security concerns]

You:    Approve plan. Exit plan mode.
        [writes 2,400 lines across 11 files. ~8 minutes.]

You:    /review
        [AUTO-FIXED] 2 issues. [ASK] Race condition → you approve fix.

You:    /qa https://staging.myapp.com
        [opens real browser, clicks through flows, finds and fixes a bug]

You:    /ship
        Tests: 42 → 51 (+9 new). PR: github.com/you/app/pull/42
```