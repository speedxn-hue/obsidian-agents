---
learned_by: Claude Code
learned_at: 2026-04-16
category: infrastructure
tags: [openclaw, filesystem, security, config, symlink]
---

# Lesson: OpenClaw Filesystem Scope 配置

## Context

Phase 5B Fix v1 完成後（description rewrite），OpenClaw 透過 Telegram 觸發 `vault-write` skill，但 `write` tool 嘅 output 去咗 `~/.openclaw/workspace/.learnings/` / `~/.openclaw/workspace/Agent-OpenClaw/...`，而唔係 `~/ObsidianAgents/`。Hermes 完全 work。

## Root Cause（實際確認）

**唔係 `workspaceOnly` guard 攔住**。係 **path resolution** 問題：

1. OpenClaw 嘅 `write` tool（來自 `@mariozechner/pi-coding-agent`）用 `resolveToCwd(path, cwd)`：
   - `cwd` = agent 嘅 `workspaceDir` = `~/.openclaw/workspace/`
   - 只要 path 係 **相對路徑**，就會被 resolve 去 `cwd` 下面
2. `vault-write/SKILL.md` path table 俾嘅係 **相對路徑**（例如 `Agent-OpenClaw/daily-logs/YYYY-MM-DD.md`）
3. 後果：LLM 用返 SKILL.md 嘅 path table 去 call `write(path="Agent-OpenClaw/daily-logs/...", ...)`，實際寫去 `~/.openclaw/workspace/Agent-OpenClaw/...`

至於 `tools.fs.workspaceOnly`：
- Zod schema：`z.object({ workspaceOnly: z.boolean().optional() }).strict()` — **唯一**一個 fs 相關 config key
- Default 係 `undefined` / `false` → `createHostWriteOperations` 用 `writeHostFile` 直接 `fs.writeFile`，**冇** path guard → 絕對路徑本來就寫得入 vault
- 冇 `allowedPaths` / `rootDir` / `additionalRoots` — schema `.strict()` 會 reject

## Fix（實際採用：Option B — Symlink）

兩個 subdirectory symlink：

```bash
ln -s /Users/kjai/ObsidianAgents/Agent-OpenClaw /Users/kjai/.openclaw/workspace/Agent-OpenClaw
ln -s /Users/kjai/ObsidianAgents/Shared       /Users/kjai/.openclaw/workspace/Shared
```

點解揀 Option B：

- **Option A（`tools.fs.allowedPaths`）不存在** — zod schema `.strict()` 下加呢個 key 會 config validation fail
- **Option C（改 `agents.defaults.workspace`）會污染 vault** — OpenClaw `.learnings/` / sessions / `DREAMS.md` 都會寫入 vault
- **Option B（symlink）最 surgical**：
  - 零 config 改動
  - SKILL.md body 唔使改（相對 path table 照用）
  - 只限 `Agent-OpenClaw/` + `Shared/` 兩個 write target（唔包 `Agent-Hermes/` — 防止意外寫入）
  - OS 解 symlink 時寫入 real path，冇 fs-safe realpath guard 阻擋（只用於 `read` 嘅 `readFileWithinRoot`，唔 apply 喺 `createHostWriteOperations` 當 `workspaceOnly === false`）

## Verification

Probe：
```bash
echo "probe" > /Users/kjai/.openclaw/workspace/Agent-OpenClaw/working-context/.probe-test
# 同一個檔同時喺 vault 出現：
ls /Users/kjai/ObsidianAgents/Agent-OpenClaw/working-context/.probe-test
```

兩個 path inode 相同，確認 symlink 透明 write 落 vault。

## Rollback

```bash
rm /Users/kjai/.openclaw/workspace/Agent-OpenClaw
rm /Users/kjai/.openclaw/workspace/Shared
launchctl kickstart -k gui/$(id -u)/ai.openclaw.gateway
# 如有改 config（呢次冇）：
cp ~/.openclaw/openclaw.json.bak.20260416-073940 ~/.openclaw/openclaw.json
```

## 要避免

- ❌ 唔好 disable 全部 fs guard — 但注意 OpenClaw 本身 default 已經係唔 guard，「disable」無從講起
- ❌ 唔好改 `agents.defaults.workspace` 去 vault — 會污染
- ❌ 唔好加 `tools.fs.allowedPaths` 類嘅 schema 外 key — `.strict()` 會 fail
- ✅ 有 filesystem resolution bug，睇下 relative path 同 cwd 點 resolve，先至懷疑 fs guard

## 將來 Agent Skill 設計

- **Skill body 寫 path 嗰陣，優先用 absolute path**（`~/ObsidianAgents/...` 或 `$HOME/ObsidianAgents/...`），唔靠 cwd
- 如果要用相對 path，SKILL.md 頂部要清楚標明 "paths relative to `~/ObsidianAgents/`"
- 寫入前先 probe 目的 path 能唔能 read，fail 就報 user-facing error，好過 silent mis-write
- 唔同 agent framework（OpenClaw / Hermes / Claude Code）嘅 `write_file` tool cwd 語意可能唔同，寫 cross-agent skill 時要明

## Scope 影響

- **只影響** OpenClaw。Hermes 唔受影響（Hermes write_file 直接用 absolute path，冇 workspace cwd 概念）
- **只影響** write 行為。Read 經 Bash tool（cat/ls/grep 絕對路徑）一直 work
- 舊有 `~/.openclaw/workspace/` 結構完整保留（AGENTS.md / DREAMS.md / memory / skills 等）
