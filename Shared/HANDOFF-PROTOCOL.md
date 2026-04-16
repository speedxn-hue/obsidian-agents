---
version: 1.0
created_at: 2026-04-16
applies_to: [OpenClaw, Hermes]
purpose: Cross-agent handoff protocol for shared memory vault
---

# Handoff Protocol v1.0

## 基本原則

1. **Write Separation**：每個 agent 只寫自己 folder + Shared/
2. **No Overwrite**：Shared/ 入面，唔可以改另一 agent 已 finalized 嘅檔
3. **YAML Frontmatter 必備**：所有檔案都要有 metadata header
4. **File Naming**：`YYYY-MM-DD-slug.md`（slug 用 kebab-case）

## 資料夾用途

| 路徑 | 邊個寫 | 邊個讀 | 用途 |
|---|---|---|---|
| `Agent-OpenClaw/daily-logs/` | OpenClaw only | Both | 每日工作日誌 |
| `Agent-OpenClaw/mistakes/` | OpenClaw only | Both | 錯誤記錄 |
| `Agent-OpenClaw/working-context/` | OpenClaw only | Both | 當前 context |
| `Agent-Hermes/daily-logs/` | Hermes only | Both | 每日工作日誌 |
| `Agent-Hermes/mistakes/` | Hermes only | Both | 錯誤記錄 |
| `Agent-Hermes/working-context/` | Hermes only | Both | 當前 context |
| `Shared/handoffs/` | Both | Both | 任務交接 |
| `Shared/lessons-learned/` | Both | Both | 共同學習 |
| `Shared/decisions/` | Both | Both | 重要決策記錄 |
| `Shared/projects/` | Both | Both | 共同項目狀態 |

## Handoff 檔案格式

```yaml
---
created_by: OpenClaw | Hermes
created_at: ISO-8601 timestamp
status: draft | awaiting_review | in_progress | completed | abandoned
target_agent: OpenClaw | Hermes | any
project: project-name (kebab-case)
priority: low | normal | high | urgent
related_files: [list of vault paths this handoff references]
---

# Handoff Title

## Context
（點解要做呢件事？前情提要）

## Request
（明確要對方做乜）

## Deliverable
（對方完成時應該寫返咩檔案喺邊）

## Notes
（額外資料、約束、tips）

## Response (target agent 填)
（target_agent 讀完之後喺呢度寫回應、問題、或 link 去新檔）
```

## Response 流程

1. Agent A 寫 handoff，status=`awaiting_review`
2. Agent B 讀到（透過 skill scan Shared/handoffs/）
3. Agent B 改個同名檔嘅 `## Response` section，status=`in_progress`
4. Agent B 完成後，更新 status=`completed`，喺 `Deliverable` 寫結果檔 path

## 絕對禁止

參考 `~/ObsidianAgents/SECURITY.md`：
- 唔寫 API key / token / password
- 唔寫完整 email / phone / address
- 如果 context 需要敏感資料，寫 `[REDACTED: description]`

## Conflict Avoidance

- 一個 agent 改緊某個 Shared/ 檔時，另一個 agent 要等（睇 file mtime + status）
- 如果真係 conflict，由先 commit 嗰個 win（Git 機制）
- Merge conflict 由人手解決，唔好 agent 自己解

## 發現新 handoff 嘅機制

Agent 應該喺每次被 activate 時 scan：
```
Shared/handoffs/*.md
```
過濾 `target_agent: <self>` 或 `target_agent: any` + `status: awaiting_review`。
