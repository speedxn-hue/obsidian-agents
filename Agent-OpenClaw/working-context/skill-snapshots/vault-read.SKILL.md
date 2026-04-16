---
name: vault-read
version: 1.0.0
description: "Read the shared Obsidian vault at ~/ObsidianAgents/. Use when user says: (1) check handoffs / 有冇 handoff / 睇 vault, (2) any pending tasks / 有冇嘢要做, (3) what did we decide / 之前決定咗乜, (4) check shared memory / 共享記憶, (5) before starting work to load Hermes handoffs targeted at OpenClaw. NOT for: writing to vault (use vault-write)."
---

# Vault Read Skill

## ⚠️ CRITICAL: Always Use Absolute Paths

**ALWAYS use absolute paths starting with `/Users/kjai/ObsidianAgents/`.**

- ❌ WRONG: `Shared/handoffs/2026-04-16-foo.md`
- ❌ WRONG: `~/ObsidianAgents/Shared/handoffs/2026-04-16-foo.md` (LLM may write literal `~` as a directory name in some tool calls)
- ✅ CORRECT: `/Users/kjai/ObsidianAgents/Shared/handoffs/2026-04-16-foo.md`

Relative paths resolve to the OpenClaw workspace (`~/.openclaw/workspace/`), NOT to the vault.

## Purpose

讀取 `/Users/kjai/ObsidianAgents/` vault 去獲取：

1. Hermes 畀我嘅 handoff（未處理嘅）
2. 共同 lessons learned
3. 我自己過往嘅 working context
4. 共同項目 status

## Vault 結構

參考：`/Users/kjai/ObsidianAgents/Shared/HANDOFF-PROTOCOL.md`

## Key Absolute Paths

| 目的 | 絕對路徑 |
|---|---|
| Hermes → me handoffs | `/Users/kjai/ObsidianAgents/Shared/handoffs/` |
| Shared lessons | `/Users/kjai/ObsidianAgents/Shared/lessons-learned/` |
| Shared decisions | `/Users/kjai/ObsidianAgents/Shared/decisions/` |
| My working context | `/Users/kjai/ObsidianAgents/Agent-OpenClaw/working-context/` |
| My daily logs | `/Users/kjai/ObsidianAgents/Agent-OpenClaw/daily-logs/` |
| Hermes daily logs (read-only) | `/Users/kjai/ObsidianAgents/Agent-Hermes/daily-logs/` |
| Handoff protocol | `/Users/kjai/ObsidianAgents/Shared/HANDOFF-PROTOCOL.md` |
| Security rules | `/Users/kjai/ObsidianAgents/SECURITY.md` |

## 常見用例

### 用例 1：Check 有冇新 handoff 畀我

```bash
cd /Users/kjai/ObsidianAgents/Shared/handoffs/
# 只掃 YYYY-MM-DD-*.md（避開 README.md 等 template）
# 只睇 frontmatter 頭 30 行，避免 match 到正文 example
for f in 2[0-9][0-9][0-9]-*.md; do
  [ -f "$f" ] || continue
  head -30 "$f" | grep -qE '^target_agent:\s*(OpenClaw|any)' || continue
  head -30 "$f" | grep -qE '^status:\s*awaiting_review' || continue
  echo "📬 Pending handoff: $f"
done
```

### 用例 2：搵過往關於某 topic 嘅 lessons

```bash
grep -r -l "topic-keyword" /Users/kjai/ObsidianAgents/Shared/lessons-learned/
```

### 用例 3：讀我自己最近 working context

```bash
ls -t /Users/kjai/ObsidianAgents/Agent-OpenClaw/working-context/ | head -3
```

## Guidelines

- **永遠讀 SECURITY.md 一次**（cache 喺 working memory）：`/Users/kjai/ObsidianAgents/SECURITY.md`
- **永遠讀 HANDOFF-PROTOCOL.md 一次**：`/Users/kjai/ObsidianAgents/Shared/HANDOFF-PROTOCOL.md`
- 讀完 handoff，如果要處理，用 vault-write skill 更新 `status=in_progress`
- 讀取行為唔需要寫 log（避免 noise）

## Read-only 路徑

呢個 skill 只讀，唔寫。寫要用 `vault-write` skill。
