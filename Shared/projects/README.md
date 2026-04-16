# Projects

長期項目嘅 context、roadmap、status。

## 命名

`project-slug.md`（kebab-case，冇日期 prefix）

例：
- `multi-agent-memory.md`
- `telegram-gateway-rollout.md`

## 模板

```markdown
---
project: project-slug
status: planning | active | on-hold | completed | abandoned
started: 2026-04-16
owner: OpenClaw | Hermes | both | user
---

# Project Name

## Goal
（一句講清楚想達到咩）

## Context / Background
（點解要做？）

## Current State
（而家去到邊？）

## Roadmap
- [x] Phase 1: ...
- [ ] Phase 2: ...
- [ ] Phase 3: ...

## Related
- Decisions: [[ADR-001-...]], [[ADR-002-...]]
- Lessons: [[2026-04-16-...]]
- Handoffs: [[...]]

## Changelog
- 2026-04-16: Initial spec
```

## 規則

- 每次大更新，喺 `Changelog` section 補一行
- Status 轉變要寫清楚原因
- 結案時將穩定經驗抽出嚟去 `lessons-learned/`
