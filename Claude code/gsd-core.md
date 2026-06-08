
說明文件：
[gsd-core](https://github.com/open-gsd/gsd-core/tree/next/docs/zh-CN)
[你的 AI 助理變笨了嗎？這套 GSD 實戰框架，教你如何精準控制 AI 產出神級程式碼！](https://www.youtube.com/watch?v=IECtPdCNlf4)
[[適用於Claude code Pro方案的設定檔]]
## 完整工作流總覽
1. new-project：問答->研究->需求->路線圖，適用於第一次建置開發專案
2. 依據路線圖所列的每一項需求進行階段開發，每一階段開發的流程如下：
	discuess(鎖定偏好)->plan(研究+計畫)->execute(並行執行)->verify(驗收測試)

## 全新專案

1. 初始化項目：
```shell
# 全新專案啟動
/gsd:new-project

# 指定現有的規格文件自動初始化
/gsd:new-project --auto @prd.md @prd2.md

# 如果執行 gsd:new-project 發現已有代碼庫，就會自動先執行以下指令
/gsd:map-codebase
/gsd:new-project
```

執行完畢後，會產生以下文件：

| 文件名稱            | 說明                              |
| --------------- | ------------------------------- |
| PROJECT.md      | 專案描述和功能模組清單                     |
| REQUIREMENTS.md | 依據功能模組清單編列驗收計畫                  |
| ROADMAP.md      | 針對不同的需求列出實作階段(含編號)，階段編號進行開發時會用到 |
| STATE.md        | 記錄整個專案目前進行的狀況                   |

![[Pasted image 20260606114606.png]]

以下是針對每一項需求進行開發時，依序要進行的步驟：

2. [`/gsd-discuss-phase`](https://github.com/open-gsd/gsd-core/blob/next/docs/zh-CN/COMMANDS.md#gsd-discuss-phase)
> 在规划前通过自适应提问收集阶段上下文。

| 参数  | 必填  | 描述            |
| --- | --- | ------------- |
| `N` | 否   | 阶段编号（默认为当前阶段） |
```shell
/gsd-discuss-phase 1                # 阶段 1 的交互式讨论
/gsd-discuss-phase 1 --all          # 不经选择步骤讨论所有灰色地带
/gsd-discuss-phase 3 --auto         # 自动为阶段 3 选择默认值
/gsd-discuss-phase --batch          # 当前阶段的批量模式
/gsd-discuss-phase 2 --analyze      # 含权衡分析的讨论
/gsd-discuss-phase 1 --power        # 从文件批量解答
/gsd-discuss-phase 3 --assumptions  # 在规划前呈现 Claude 的假设
```

| 标志              | 描述                           |
| --------------- | ---------------------------- |
| `--all`         | 跳过领域选择 — 交互式讨论所有灰色地带（不自动推进）  |
| `--auto`        | 自动为所有问题选择推荐的默认值              |
| `--batch`       | 将问题分组批量输入，而非逐条处理             |
| `--analyze`     | 在讨论期间添加权衡分析                  |
| `--power`       | 基于文件的批量问题解答，从预先准备的答案文件中读取    |
| `--assumptions` | 无需交互会话，直接呈现 Claude 对该阶段实现的假设 |

**前提条件：** `.planning/ROADMAP.md` 已存在，**執行後產出以下文件：**

| 文件                          | 描述                      |
| --------------------------- | ----------------------- |
| `{phase}-CONTEXT.md`        | 討論後的實作偏好，之後的計畫和實作會沿用此文件 |
| `{phase}-DISCUSSION-LOG.md` |                         |

3. [`/gsd-plan-phase`](https://github.com/open-gsd/gsd-core/blob/next/docs/zh-CN/COMMANDS.md#gsd-plan-phase)
```shell
/gsd-plan-phase 1                              # 研究 + 规划 + 验证阶段 1
/gsd-plan-phase 3 --skip-research              # 无需研究直接规划（熟悉的领域）
/gsd-plan-phase --auto                         # 非交互式规划
/gsd-plan-phase 2 --validate                   # 规划前验证状态
/gsd-plan-phase 1 --bounce                     # 规划 + 外部弹回验证
/gsd-plan-phase 2 --ingest docs/adr/0010.md   # 使用 ADR 快速通道进行上下文综合
/gsd-plan-phase 2 --ingest 'docs/adr/00*.md' --ingest-format auto
/gsd-plan-phase --research-phase 4             # 仅研究阶段 4（RESEARCH.md 存在时提示）
/gsd-plan-phase --research-phase 4 --view      # 打印现有 RESEARCH.md，不生成新报告
/gsd-plan-phase --research-phase 4 --research  # 强制刷新研究，不提示
/gsd-plan-phase 1 --mvp                        # 阶段 1 的垂直切片规划
/gsd-plan-phase 1 --mvp --tdd                  # 垂直切片 + 每个行为添加任务以失败测试开始
```

研究、规划并验证一个阶段。

|参数|必填|描述|
|---|---|---|
|`N`|否|阶段编号（默认为下一个未规划的阶段）|

| 标志                                                | 描述                                                                                                                                                             |
| ------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--auto`                                          | 跳过交互式确认                                                                                                                                                        |
| `--research`                                      | 即使 RESEARCH.md 已存在也强制重新研究                                                                                                                                      |
| `--skip-research`                                 | 跳过领域研究步骤                                                                                                                                                       |
| `--research-phase <N>`                            | 仅研究模式：为阶段 `<N>` 生成研究报告，写入 RESEARCH.md 后退出，不进入规划器。取代已删除的独立研究命令（#3042）。                                                                                          |
| `--view`                                          | 仅研究模式修饰符：与 `--research-phase` 配合使用时，将现有 RESEARCH.md 打印到标准输出并退出（不生成新报告）。RESEARCH.md 不存在时报错。                                                                     |
| `--gaps`                                          | 差距闭合模式（读取 VERIFICATION.md，跳过研究）                                                                                                                                |
| `--skip-verify`                                   | 跳过计划检查器验证循环                                                                                                                                                    |
| `--prd <file>`                                    | 使用 PRD 文件而非 discuss-phase 获取上下文                                                                                                                                |
| `--ingest <path-or-glob>`                         | 使用 ADR 文件代替 discuss-phase 进行上下文综合                                                                                                                              |
| `--ingest-format <auto\|nygard\|madr\|narrative>` | `--ingest` 的可选 ADR 解析器格式覆盖                                                                                                                                     |
| `--reviews`                                       | 根据 REVIEWS.md 中的跨 AI 审查反馈重新规划                                                                                                                                  |
| `--validate`                                      | 在规划开始前运行状态验证                                                                                                                                                   |
| `--bounce`                                        | 规划完成后运行外部计划弹回验证（使用 `workflow.plan_bounce_script`）                                                                                                              |
| `--skip-bounce`                                   | 即使配置中已启用也跳过计划弹回                                                                                                                                                |
| `--mvp`                                           | 垂直 MVP 模式 — 规划器将任务组织为功能切片（UI→API→DB），而非水平分层。在无先前阶段摘要的新项目第 1 阶段使用时，还会生成 `SKELETON.md`（行走骨架）。可通过在 ROADMAP.md 中设置 `**Mode:** mvp` 持久化应用于某阶段，届时无需标志即可自动应用 `--mvp`。 |
| `--tdd`                                           | TDD 模式 — 规划器对符合条件的行为添加任务应用 `type: tdd`，使每个任务以失败测试开始。可与 `--mvp` 组合：`--mvp --tdd` 产生每个行为添加任务以红-绿流程开始的垂直切片。                                                       |

**前提条件：** `.planning/ROADMAP.md` 已存在，**产出：** `{phase}-RESEARCH.md`、`{phase}-{N}-PLAN.md`、`{phase}-VALIDATION.md`；行走骨架模式触发时产出 `{phase}/SKELETON.md`

**仅研究模式（`--research-phase <N>`）：**

- 无修饰符：如果 RESEARCH.md 已存在，提示 `update / view / skip`。
- 加 `--research`：强制刷新 — 无条件重新生成，不提示。
- 加 `--view`：将现有 RESEARCH.md 打印到标准输出，不生成新报告。RESEARCH.md 不存在时报错。

**包合法性检查门（v1.42.1）：** 当研究者推荐外部包时，会对每个包运行 `slopcheck install <pkg> --json` 并在 RESEARCH.md 中写入 `## Package Legitimacy Audit` 表格，记录注册表、年龄、下载量、源码仓库和 slopcheck 裁决。裁决结果：

- `[SLOP]` — 包从 RESEARCH.md 中完全移除，永远不会进入规划器
- `[SUS]` — 包被标记；规划器在安装任务前插入 `checkpoint:human-verify`
- `[OK]` — 包已批准，不添加检查点

来自 WebSearch 的包被标记为 `[ASSUMED]`（而非 `[VERIFIED]`），处理方式与 `[SUS]` 相同 — 安装前需要人工检查点。如果无法安装 `slopcheck`，所有推荐的包都会被标记为 `[ASSUMED]` 并加以限制。

完整的检查点格式、裁决表和故障排除，请参阅[用户指南中的包合法性检查门](https://github.com/open-gsd/gsd-core/blob/next/docs/zh-CN/USER-GUIDE.md#package-legitimacy-gate-v1421)。
