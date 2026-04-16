# Handoffs — 跨 Agent 任務交接

Handoff 係一個 agent 將工作交畀另一個 agent（或畀未來嘅自己）嘅 checkpoint。

## 為咩要有 handoff？

- Agent session 有限，context 會消失
- 跨 agent 協作（例如 OpenClaw 起草、Hermes 審批）
- 畀人類監察：每個 handoff 都可以被讀、被 block、被 redirect

## 強制 Format

**每個 handoff 檔案必須有以下 YAML frontmatter 同 sections：**

```yaml
---
created_by: OpenClaw | Hermes
created_at: 2026-04-16T14:30:00+01:00
status: draft | awaiting_review | completed
target_agent: OpenClaw | Hermes | any
project: project-name
---

# Handoff Title

## Context
（背景：點解要做呢件事？之前發生過咩？）

## Request
（具體想 target agent 做咩？越明確越好）

## Deliverable
（完成 criteria：點樣先算做完？）

## Notes
（補充：注意事項、已知風險、參考連結）
```

## Status 生命週期

```
draft           ← 寫緊，未準備好交出
  ↓
awaiting_review ← 交出，等 target agent 處理
  ↓
completed       ← target agent 做完咗，封檔
```

## 命名慣例

`YYYY-MM-DD-HHMM-source-to-target-topic.md`

例子：
- `2026-04-16-1430-openclaw-to-hermes-telegram-migration.md`
- `2026-04-17-0900-hermes-to-any-review-skill-vetter.md`（target `any` 表示邊個都得）

## 完成後點處理

1. Target agent 更新 `status: completed`
2. 如果有重要學習，抽出嚟寫入 `../lessons-learned/`
3. 檔案留喺度做歷史記錄（唔好刪，Git 都有但 Obsidian link 可以保留）
