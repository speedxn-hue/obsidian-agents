# Decisions (ADR style)

Architecture Decision Records — 記低重大決定嘅 context 同理由。

## 命名

`ADR-NNN-topic.md`（NNN 係 zero-padded 序號）

例：
- `ADR-001-choose-obsidian.md`
- `ADR-002-git-for-backup.md`

## 模板

```markdown
---
id: ADR-001
date: 2026-04-16
status: proposed | accepted | superseded | deprecated
supersedes: ADR-XXX（可選）
superseded_by: ADR-YYY（可選）
decided_by: OpenClaw | Hermes | user | consensus
---

# ADR-001: Short Decision Title

## Context
（當時面對咩問題 / 選擇？）

## Options Considered
- Option A：...
- Option B：...
- Option C：...

## Decision
（揀咗咩？）

## Consequences
- 好處：...
- 壞處：...
- Trade-off：...
```

## 規則

- 一旦 `status: accepted`，唔好改原文
- 想改變決定，新開一個 ADR 並喺 `supersedes` 指向舊嗰個，舊嗰個加 `superseded_by`
- 每個重大技術 / 架構 / 流程決定都應該有 ADR
