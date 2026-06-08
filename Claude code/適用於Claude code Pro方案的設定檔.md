
## Token 消耗估算（官方配置優化後）

|操作|並行模式|序列模式|節省|
|---|---|---|---|
|`/gsd-plan-phase`|~80,000|~25,000|69%|
|`/gsd-execute-phase`|~120,000|~40,000|67%|
|Research（4 agents）|~40,000|~10,000|75%|
|Discuss|~30,000|~15,000|50%|

### gsd:settings
```shell
❯ gsd:settings

● Skill(/gsd:settings)
Successfully loaded skill · 4 tools allowed
Read .claude\gsd-core\workflows\settings.md (567 lines)

先讀取目前的設定檔。

  Read 1 file

讀取完成，目前設定為 adaptive model profile。現在開始互動式設定流程。

● User answered Claude's questions:
· Which model profile for agents? → Adaptive (Recommended)

● User answered Claude's questions:
· Spawn Plan Researcher? (researches domain before planning) → Yes (Recommended)
· Spawn Plan Checker? (verifies plans before execution) → Yes (Recommended)
· Enable Plan Drift Guard? (verifies that symbols cited in plans exist in source at review time) → Yes (Recommended)
· Run Pattern Mapper? (maps new files to existing codebase analogs between research and planning) → Yes (Recommended)

● User answered Claude's questions:
· Enable Nyquist Validation? (researches test coverage during planning) → Yes (Recommended)
· Enable UI Phase? (generates UI-SPEC.md design contracts for frontend phases) → Yes (Recommended)
· Enable UI Safety Gate? (prompts to run /gsd-ui-phase before planning frontend phases) → Yes (Recommended)
· Enable AI Phase? (framework selection + eval strategy for AI phases) → No

● User answered Claude's questions:
· Spawn Execution Verifier? (verifies phase completion) → Yes (Recommended)
· Enable TDD Mode? (RED/GREEN/REFACTOR gates for eligible tasks) → No (Recommended)
· Enable Code Review? (/gsd-code-review and /gsd-code-review --fix commands) → Yes (Recommended)
· Enable UI Review? (visual quality audit via /gsd-ui-review in autonomous mode) → Yes (Recommended)

Code Review 開啟，繼續詢問深度。

● User answered Claude's questions:
· Code Review Depth? (default depth for /gsd-code-review — override per-run with --depth=) → Standard (Recommended)
· Commit .planning/ files to git? (controls whether plans/artifacts are tracked in your repo) → Yes (Recommended)
· Skip discuss-phase in autonomous mode? (use ROADMAP phase goals as spec) → No (Recommended)
· Use git worktrees for parallel agent isolation? → Yes (Recommended)

● User answered Claude's questions:
· Enable Intel? (queryable codebase intelligence via /gsd-map-codebase --query) → No (Recommended)
· Enable Graphify? (project knowledge graph via /gsd-graphify) → No (Recommended)
● User answered Claude's questions:
· Enable Intel? (queryable codebase intelligence via /gsd-map-codebase --query) → No (Recommended)
· Enable Graphify? (project knowledge graph via /gsd-graphify) → No (Recommended)
· Auto-advance pipeline? (discuss → plan → execute automatically) → No (Recommended)
· Git branching strategy? → None (Recommended)

● User answered Claude's questions:
· Create git tags on milestone completion? → Yes (Recommended)
· Enable context window warnings? (injects advisory messages when context is getting full) → Yes (Recommended)
· Research best practices before asking questions? (web search during new-project and discuss-phase) → No (Recommended)

所有問題回答完成，現在更新設定檔。唯一的變更是 AI Phase 改為關閉。
```

### .planning/config.json（官方參數 - Claude Pro 優化版）
```json
{
  "model_profile": "adaptive",
  "commit_docs": true,
  "parallelization": {
    "enabled": false,
    "plan_level": false,
    "task_level": false,
    "skip_checkpoints": true,
    "max_concurrent_agents": 1,
    "min_plans_for_parallel": 999
  },
  "search_gitignored": false,
  "brave_search": false,
  "firecrawl": false,
  "exa_search": false,
  "git": {
    "branching_strategy": "none",
    "create_tag": true,
    "phase_branch_template": "gsd/phase-{phase}-{slug}",
    "milestone_branch_template": "gsd/{milestone}-{slug}",
    "quick_branch_template": null
  },
  "workflow": {
    "research": true,
    "plan_check": true,
    "verifier": true,
    "nyquist_validation": true,
    "auto_advance": false,
    "node_repair": true,
    "node_repair_budget": 2,
    "ui_phase": true,
    "ui_safety_gate": true,
    "ai_integration_phase": false,
    "tdd_mode": false,
    "human_verify_mode": "end-of-phase",
    "text_mode": false,
    "research_before_questions": false,
    "discuss_mode": "discuss",
    "skip_discuss": false,
    "code_review": true,
    "code_review_depth": "standard",
    "code_review_command": null,
    "pattern_mapper": true,
    "plan_bounce": false,
    "plan_bounce_script": null,
    "plan_bounce_passes": 2,
    "auto_prune_state": false,
    "post_planning_gaps": true,
    "security_enforcement": true,
    "security_asvs_level": 1,
    "security_block_on": "high",
    "ui_review": true,
    "use_worktrees": true,
    "_auto_chain_active": false
  },
  "ship": {
    "pr_body_sections": [
      {
        "heading": "User Stories & Acceptance Criteria",
        "enabled": true,
        "source": "REQUIREMENTS.md ## User Stories || REQUIREMENTS.md ## Acceptance Criteria",
        "fallback": "- Acceptance criteria are covered by the linked requirements and verification evidence."
      },
      {
        "heading": "Risks & Dependencies",
        "enabled": true,
        "source": "PLAN.md ## Risks || PLAN.md ## Dependencies",
        "fallback": "- No known high-risk rollout dependencies."
      },
      {
        "heading": "Success Metrics & Release Criteria",
        "enabled": true,
        "source": "REQUIREMENTS.md ## Definition of Done || VERIFICATION.md ## Release Criteria",
        "fallback": "- Release when automated verification and required manual checks pass."
      },
      {
        "heading": "Stakeholder Review & Approval",
        "enabled": true,
        "template": "- Product owner approval pending for {phase_name}."
      }
    ]
  },
  "hooks": {
    "context_warnings": true
  },
  "project_code": null,
  "phase_naming": "sequential",
  "agent_skills": {},
  "claude_md_path": "./CLAUDE.md",
  "plan_review": {
    "source_grounding": true,
    "source_grounding_authority": "grep"
  },
  "mode": "interactive",
  "granularity": "standard",
  "intel": {
    "enabled": false
  },
  "graphify": {
    "enabled": false
  }
}
```


# GSD Core 官方配置參數完整說明

## 核心設定（Core Settings）

| 參數 | 類型 | 選項 | 預設值 | 說明 |
|------|------|------|--------|------|
| `mode` | enum | `interactive`, `yolo` | `interactive` | `yolo` 自動批准所有決策；`interactive` 每步確認 |
| `granularity` | enum | `coarse`, `standard`, `fine` | `standard` | Phase 數量：`coarse` (3-5), `standard` (5-8), `fine` (8-12) |
| `model_profile` | enum | `quality`, `balanced`, `budget`, `adaptive`, `inherit` | `balanced` | 模型層級選擇 |
| `model_overrides` | object | - | `{}` | 覆蓋特定 agent 的模型 |
| `models` | object | - | `{}` | Per-phase-type 模型設定 |
| `dynamic_routing` | object/null | - | `null` | 動態路由與失敗升級機制 |
| `project_code` | string | - | `null` | Phase 目錄前綴（如 `"ABC"` → `ABC-01-setup/`）|
| `response_language` | string | 語言代碼 | `null` | Agent 回應語言（如 `"zh"`, `"ja"`, `"ko"`）|

$CITE_1

---

## Planning 設定

| 參數 | 類型 | 預設值 | 說明 |
|------|------|--------|------|
| `planning.commit_docs` | boolean | `true` | 是否提交規劃文件到 Git |
| `planning.search_gitignored` | boolean | `false` | 是否搜尋 .gitignore 的檔案 |
| `planning.sub_repos` | array | `[]` | 子 repository 列表 |

$CITE_1

---

## Workflow 設定（最重要的區塊）

### 品質關鍵 Agents

| 參數                            | 類型      | 預設值        | 說明                          | Claude Pro 建議       |
| ----------------------------- | ------- | ---------- | --------------------------- | ------------------- |
| `workflow.research`           | boolean | `true`     | 啟用 Research agents          | ✅ **保留**            |
| `workflow.plan_check`         | boolean | `true`     | 計畫品質檢查                      | ✅ **保留**            |
| `workflow.verifier`           | boolean | `true`     | Phase 結束驗證                  | ✅ **保留**            |
| `workflow.nyquist_validation` | boolean | `true`     | 生成 VALIDATION.md            | ✅ **保留**            |
| `workflow.code_review`        | boolean | `true`     | 程式碼審查                       | ✅ **保留**            |
| `workflow.code_review_depth`  | enum    | `standard` | `light`, `standard`, `deep` | `standard` 節省 token |
| `workflow.node_repair`        | boolean | `true`     | 自動修復                        | ✅ **保留**            |
| `workflow.node_repair_budget` | integer | `2`        | 修復嘗試次數上限                    | `2` 避免無限循環          |

### Token 節省設定

| 參數                                   | 類型      | 預設值     | 說明           | Claude Pro 建議              |
| ------------------------------------ | ------- | ------- | ------------ | -------------------------- |
| `workflow.auto_advance`              | boolean | `false` | 自動進入下一 phase | ❌ **停用**（手動控制）             |
| `workflow.max_discuss_passes`        | integer | `3`     | 討論循環次數上限     | `2` 節省 30%                 |
| `workflow.research_before_questions` | boolean | `false` | 提問前自動研究      | ❌ **停用**                   |
| `workflow.plan_bounce`               | boolean | `false` | 計畫來回優化       | ❌ **停用**（高 token 消耗）       |
| `workflow.cross_ai_execution`        | boolean | `false` | 跨 AI 執行      | ❌ **停用**（Pro 不需要）          |
| `workflow.tdd_mode`                  | boolean | `false` | TDD 模式       | ❌ **停用**（由 Superpowers 處理） |

### UI Phase 設定

| 參數 | 類型 | 預設值 | 說明 | Claude Pro 建議 |
|------|------|--------|------|-----------------|
| `workflow.ui_phase` | boolean | `true` | 啟用 UI Phase | ⚠️ 非 UI 專案停用 |
| `workflow.ui_safety_gate` | boolean | `true` | UI 安全檢查 | ✅ 保留 |
| `workflow.ui_review` | boolean | `true` | UI 審查 | ✅ 保留 |

### 驗證與安全

| 參數 | 類型 | 預設值 | 說明 |
|------|------|--------|------|
| `workflow.human_verify_mode` | enum | `end-of-phase` | `per-plan`, `end-of-phase`, `milestone` |
| `workflow.use_worktrees` | boolean | `true` | 使用 Git worktrees 隔離 |
| `workflow.security_enforcement` | boolean | `true` | 安全檢查 |
| `workflow.security_asvs_level` | integer | `1` | ASVS 等級（1-3）|
| `workflow.security_block_on` | enum | `high` | `critical`, `high`, `medium` |

### 其他工作流設定

| 參數                            | 類型          | 預設值       | 說明                         |
| ----------------------------- | ----------- | --------- | -------------------------- |
| `workflow.discuss_mode`       | enum        | `discuss` | `discuss`, `quick`, `skip` |
| `workflow.skip_discuss`       | boolean     | `false`   | 跳過討論階段                     |
| `workflow.text_mode`          | boolean     | `false`   | 純文字模式（無程式碼）                |
| `workflow.plan_chunked`       | boolean     | `false`   | 分塊規劃                       |
| `workflow.post_planning_gaps` | boolean     | `true`    | 規劃後檢查遺漏                    |
| `workflow.build_command`      | string/null | `null`    | 自訂建置指令                     |
| `workflow.test_command`       | string/null | `null`    | 自訂測試指令                     |

$CITE_1

---

## Parallelization 設定（最關鍵的 Token 節省）

> Parallelization 代表是否要啟用子代理並行

| 參數                                       | 類型      | 預設值     | 說明             | Claude Pro 建議                 |
| ---------------------------------------- | ------- | ------- | -------------- | ----------------------------- |
| `parallelization.enabled`                | boolean | `true`  | 啟用並行執行         | ❌ **`false`**（省 60-70% token） |
| `parallelization.plan_level`             | boolean | `true`  | Plan 層級並行      | ❌ **`false`**                 |
| `parallelization.task_level`             | boolean | `false` | Task 層級並行      | ❌ **`false`**                 |
| `parallelization.skip_checkpoints`       | boolean | `true`  | 跳過檢查點          | ✅ 保留                          |
| `parallelization.max_concurrent_agents`  | integer | `3`     | 最大並行 agents 數  | `1`（序列執行）                     |
| `parallelization.min_plans_for_parallel` | integer | `2`     | 啟動並行的最小 plan 數 | `999`（永不並行）                   |

> **這是最重要的區塊！停用並行是節省 60-70% token 的關鍵。**

$CITE_1 $CITE_2

---

## Code Quality 設定

| 參數 | 類型 | 預設值 | 說明 | Claude Pro 建議 |
|------|------|--------|------|-----------------|
| `code_quality.fallow.enabled` | boolean | `false` | 啟用 Fallow 程式碼品質掃描 | ❌ **停用**（額外 token 消耗）|
| `code_quality.fallow.scope` | enum | `phase` | `phase`, `project` | - |
| `code_quality.fallow.profile` | enum | `standard` | `light`, `standard`, `deep` | - |
| `code_quality.fallow.mcp` | boolean | `false` | 使用 MCP 整合 | - |

$CITE_1

---

## Git 設定

| 參數 | 類型 | 預設值 | 說明 |
|------|------|--------|------|
| `git.branching_strategy` | enum | `none` | `none`, `phase`, `milestone`, `quick` |
| `git.create_tag` | boolean | `true` | 建立 Git tags |
| `git.phase_branch_template` | string | `gsd/phase-{phase}-{slug}` | Phase 分支命名模板 |
| `git.milestone_branch_template` | string | `gsd/{milestone}-{slug}` | Milestone 分支模板 |
| `git.quick_branch_template` | string/null | `null` | Quick 分支模板 |

$CITE_1

---

## Gates 設定（確認點）

| 參數 | 類型 | 預設值 | 說明 |
|------|------|--------|------|
| `gates.confirm_project` | boolean | `true` | 確認專案設定 |
| `gates.confirm_phases` | boolean | `true` | 確認 phase 規劃 |
| `gates.confirm_roadmap` | boolean | `true` | 確認 roadmap |
| `gates.confirm_breakdown` | boolean | `true` | 確認任務分解 |
| `gates.confirm_plan` | boolean | `true` | 確認執行計畫 |
| `gates.execute_next_plan` | boolean | `true` | 執行下一個 plan 前確認 |
| `gates.issues_review` | boolean | `true` | Issue 審查確認 |
| `gates.confirm_transition` | boolean | `true` | Phase 轉換確認 |

$CITE_1

---

## Safety 設定

| 參數 | 類型 | 預設值 | 說明 |
|------|------|--------|------|
| `safety.always_confirm_destructive` | boolean | `true` | 破壞性操作前確認 |
| `safety.always_confirm_external_services` | boolean | `true` | 外部服務呼叫前確認 |

$CITE_1

---

## Features 設定

| 參數 | 類型 | 預設值 | 說明 | Claude Pro 建議 |
|------|------|--------|------|-----------------|
| `features.thinking_partner` | boolean | `false` | 啟用 Thinking Partner | ⚠️ 高 token 消耗 |
| `features.global_learnings` | boolean | `false` | 全域學習機制 | ⚠️ 額外開銷 |

$CITE_1

---

## Learnings 設定

| 參數 | 類型 | 預設值 | 說明 |
|------|------|--------|------|
| `learnings.max_inject` | integer | `10` | 最大注入學習數量 |

$CITE_1

---

## 其他設定

| 參數 | 類型 | 預設值 | 說明 |
|------|------|--------|------|
| `ship.pr_body_sections` | array | `[]` | PR 描述區段 |
| `hooks.context_warnings` | boolean | `true` | Context 警告 |
| `hooks.workflow_guard` | boolean | `false` | 工作流保護 |
| `statusline.context_position` | enum | `end` | `start`, `end` |
| `review.default_reviewers` | string/null | `null` | 預設審查者 |
| `review.models` | object | `{}` | 審查模型設定 |
| `intel.enabled` | boolean | `false` | 啟用 Intel 功能 |
| `claude_md_path` | string | `./CLAUDE.md` | CLAUDE.md 路徑 |
| `agent_skills` | object | `{}` | Agent 技能設定 |

$CITE_1