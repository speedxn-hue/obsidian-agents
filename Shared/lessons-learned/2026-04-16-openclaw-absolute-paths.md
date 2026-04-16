---
learned_by: Claude Code
learned_at: 2026-04-16
category: infrastructure
tags: [openclaw, filesystem, path-resolution, skills]
---

# Lesson: OpenClaw Skill 要用絕對路徑

## Context

Phase 5B Fix v1（description rewrite）之後，OpenClaw trigger `vault-write` skill 成功，但 `write` tool output 去咗 `~/.openclaw/workspace/.learnings/` / `~/.openclaw/workspace/Agent-OpenClaw/...`，唔係入 vault。

Phase 5B Fix v2 第一版試咗 symlink approach（`~/.openclaw/workspace/Agent-OpenClaw → vault`）— work，但呢個係 workaround，唔係根因修正。

## Root Cause

OpenClaw `createWriteTool` 嚟自 `@mariozechner/pi-coding-agent`，path resolution logic：

```js
// pi-coding-agent/dist/core/tools/path-utils.js
export function resolveToCwd(filePath, cwd) {
    const expanded = expandPath(filePath);  // handles ~/ → /Users/kjai/
    if (isAbsolute(expanded)) {
        return expanded;  // ← absolute passes through
    }
    return resolvePath(cwd, expanded);  // ← relative anchors to cwd
}
```

`cwd` = agent `workspaceDir` = `~/.openclaw/workspace/`。Skill SKILL.md path table 原本用相對路徑（`Agent-OpenClaw/daily-logs/YYYY-MM-DD.md`），LLM 照用，實際寫去 `~/.openclaw/workspace/Agent-OpenClaw/...`。

`tools.fs.workspaceOnly` 默認 `false`，其實容許絕對路徑直接寫入任何地方（`createHostWriteOperations` 當 `workspaceOnly === false` 時用 `writeHostFile` 直接 `fs.writeFile`，冇 path guard）。所以問題唔喺 guard，而喺 **SKILL.md 寫咩 LLM 做咩**。

## Fix（Phase 5B Fix v2 最終版）

1. `vault-read/SKILL.md` + `vault-write/SKILL.md` 全部 vault path 改絕對（`/Users/kjai/ObsidianAgents/...`）
2. 最上加 banner：
   ```
   ## ⚠️ CRITICAL: Always Use Absolute Paths
   ❌ WRONG: Agent-OpenClaw/daily-logs/2026-04-16.md
   ❌ WRONG: ~/ObsidianAgents/Shared/handoffs/test.md
   ✅ CORRECT: /Users/kjai/ObsidianAgents/Agent-OpenClaw/daily-logs/2026-04-16.md
   ```
3. 保留 v2 第一版建立嘅兩個 symlink（`Agent-OpenClaw`, `Shared`）作為 belt-and-suspenders safety net — 就算 LLM 偶爾 regress 用相對路徑，OS 都會 resolve 去正確位置

## 點解避開 `~/` 寫法

雖然 `expandPath` line 39 有處理 `~/` → `$HOME/`，但：

- 某啲 tool call path（e.g. bash `mkdir -p ~/ObsidianAgents/...` 喺 quote 入面）可能令 shell 唔展開 `~`，變成 literal 目錄名
- Absolute `/Users/kjai/...` 冇歧義
- LLM 寫 SKILL.md 入面嘅 example 時更 robust

## 應用範圍

將來 OpenClaw skill 涉及 filesystem 操作時：

- ✅ **Absolute path always**（`/Users/kjai/...`）
- ✅ **Banner 示警**放最頂
- ❌ 避免 `~` 或 `$HOME` 寫法
- ❌ 避免 relative path in path table

Hermes 冇呢個問題（Hermes 嘅 bash/write tool cwd 語意唔同，亦已經 work）。

## 反面教材（pre-v2）

Phase 5B Fix v1 只改 description 吸引 LLM trigger skill，冇 audit skill body。Body 入面用相對路徑，trigger 後 LLM 就照錯路徑寫。

**教訓**：Skill description 決定「會唔會 trigger」，skill body 決定「trigger 後做咩」。兩者都要 engineered for LLM，唔可以假設 LLM 會自動轉換相對 → 絕對。

## Rollback

```bash
cp ~/.openclaw/skills/vault-read/SKILL.md.bak ~/.openclaw/skills/vault-read/SKILL.md
cp ~/.openclaw/skills/vault-write/SKILL.md.bak ~/.openclaw/skills/vault-write/SKILL.md
launchctl kickstart -k gui/$(id -u)/ai.openclaw.gateway
```
