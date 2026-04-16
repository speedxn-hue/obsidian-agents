---
name: vault-write
version: 1.0.0
description: "Write to ~/ObsidianAgents/ vault. Use when user says: (1) log this / 記低 / save to vault, (2) send handoff to Hermes / 畀 handoff Hermes / 叫 Hermes 做, (3) daily log / 日誌, (4) record lesson / 學到咗, (5) decision record / 決定. Writes to Agent-OpenClaw/ or Shared/ only; never to Agent-Hermes/. Pre-commit gitleaks hook blocks secrets. NOT for: reading (use vault-read)."
---

# Vault Write Skill

## Purpose

向 `~/ObsidianAgents/` vault 寫入內容，並遵守安全同協議規則。

## 寫入前必做

1. 讀 `~/ObsidianAgents/SECURITY.md` 確認唔會 leak secret
2. 讀 `~/ObsidianAgents/Shared/HANDOFF-PROTOCOL.md` 確認格式

## 我可以寫邊度

| 目的 | 路徑 |
|---|---|
| Daily log | `Agent-OpenClaw/daily-logs/YYYY-MM-DD.md` |
| 錯誤記錄 | `Agent-OpenClaw/mistakes/YYYY-MM-DD-slug.md` |
| Working context | `Agent-OpenClaw/working-context/<topic>.md` |
| Handoff 畀 Hermes | `Shared/handoffs/YYYY-MM-DD-slug.md` |
| Lesson learned | `Shared/lessons-learned/YYYY-MM-DD-slug.md` |
| Decision record | `Shared/decisions/YYYY-MM-DD-slug.md` |

## 唔可以寫

- `Agent-Hermes/` 下面任何嘢（除非係讀）
- 覆蓋 Shared/ 入面 status=`completed` 嘅檔

## Template：Daily Log

```markdown
---
date: YYYY-MM-DD
agent: OpenClaw
---

# Daily Log — YYYY-MM-DD

## What I worked on
- Task 1：...
- Task 2：...

## What I learned
- （如果係重大 learning，同時 mirror 去 Shared/lessons-learned/）

## Mistakes made
- （如果有，同時詳細寫喺 mistakes/）

## Handoffs
- Sent: [link to handoff file]
- Received: [link to handoff file]

## Tomorrow / Next
- ...
```

## Template：寫 Handoff 畀 Hermes

跟 `HANDOFF-PROTOCOL.md` 定義嘅 YAML frontmatter 格式。
`target_agent: Hermes`，`status: awaiting_review`。

## Secret 自查

寫之前 mental check：
- 有冇 API key？
- 有冇 password / token？
- 有冇完整 email？
- 有冇個人身份識別資料？

如有，用 `[REDACTED]` 或縮寫（例如 `user_8122490033` 只係 ID 冇真名）。

## 寫完之後

- 唔需要 manually commit，launchd auto-sync 每 10 分鐘會 push
- 如想即時 push：`~/bin/vault-sync.sh`

## Pre-commit hook 會自動攔截

如果誤寫 secret，`.git/hooks/pre-commit` 會 block commit，你會見到 ❌ 訊息。
