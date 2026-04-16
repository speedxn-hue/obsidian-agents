---
name: vault-handoff
description: "Coordinate with OpenClaw via ~/ObsidianAgents/ shared vault. Use when user says: (1) check vault / 睇 vault / 有冇嘢做, (2) handoff to OpenClaw / 畀 OpenClaw 做, (3) respond to pending handoff / 回覆 handoff, (4) write shared lesson / 共同筆記 / 記低畀 OpenClaw 睇, (5) collaborate with OpenClaw / 同 OpenClaw 合作. Reads Shared/handoffs/ targeted at Hermes and writes responses or new handoffs."
version: 1.0.0
platforms: [macos, linux]
metadata:
  hermes:
    tags: [coordination, multi-agent, shared-memory, obsidian, handoff, openclaw, vault]
prerequisites:
  commands: [grep, ls, cat]
---

# Vault Handoff Skill

## 用途

透過 `~/ObsidianAgents/` vault 同 OpenClaw 做 agent-to-agent coordination。

## Vault 簡介

- Vault 路徑：`~/ObsidianAgents/`
- 我嘅專屬 folder：`Agent-Hermes/`
- 共享空間：`Shared/`
- 規則檔：`Shared/HANDOFF-PROTOCOL.md`（每次 invoke 都要讀一次）
- 安全檔：`SECURITY.md`（絕對唔可以 leak 嘅資料）

## 核心 Workflows

### Workflow 1：Check Pending Handoffs（我要處理嘅）

```bash
cd ~/ObsidianAgents/Shared/handoffs/
# 只掃 YYYY-MM-DD-*.md 而且 frontmatter 頭 30 行先係真 metadata
for f in 2[0-9][0-9][0-9]-*.md; do
  [ -f "$f" ] || continue
  head -30 "$f" | grep -qE '^target_agent:\s*(Hermes|any)' || continue
  head -30 "$f" | grep -qE '^status:\s*awaiting_review' || continue
  echo "📬 $f"
  head -20 "$f"
  echo "---"
done
```

### Workflow 2：Respond to a Handoff

1. 讀 handoff 全文：`cat ~/ObsidianAgents/Shared/handoffs/<file>.md`
2. 理解 request 同 deliverable
3. 完成實際任務
4. 更新 handoff 檔嘅 `## Response` section + status=`in_progress` → `completed`
5. 如果 deliverable 係新檔，寫入適當位置（例如 `Shared/projects/`）

### Workflow 3：Send New Handoff to OpenClaw

寫檔：`Shared/handoffs/YYYY-MM-DD-<slug>.md`

```markdown
---
created_by: Hermes
created_at: 
status: awaiting_review
target_agent: OpenClaw
project: 
priority: normal
related_files: []
---

# 

## Context
## Request
## Deliverable
## Notes
## Response
（留空，等 OpenClaw 填）
```

### Workflow 4：Record Cross-agent Lesson

如發現某個經驗對 OpenClaw 都有用，寫：
`Shared/lessons-learned/YYYY-MM-DD-<slug>.md`

```markdown
---
learned_by: Hermes
learned_at: 
category: 
tags: []
---

# Lesson: 

## Context
## 學到
## 要避免
## 應用場景
```

## 同我自己 memory (`~/.hermes/memories/`) 嘅關係

- `~/.hermes/memories/` = **我私人 memory**（長 context, running notes）
- Vault = **共享 memory**（要 OpenClaw 睇到嘅嘢）

決策：
- 只影響我自己 → 寫私人 memory
- OpenClaw 都要知 → 寫 vault（Shared/ 或 Agent-Hermes/ 畀佢讀）
- 重大 insight → 雙寫（私人記詳細，vault 記 summary）

## Security Checklist（寫之前）

- [ ] 冇 API key / token
- [ ] 冇完整 email / phone / address
- [ ] 用 `[REDACTED]` 代替敏感部分
- [ ] 檔案名唔含敏感字眼

## Auto-sync

Vault 每 10 分鐘自動 commit + push 去 GitHub backup。我寫完唔需要 manual push。

如想即時：`~/bin/vault-sync.sh`
