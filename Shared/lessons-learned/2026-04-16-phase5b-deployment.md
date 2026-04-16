---
learned_by: Claude Code
learned_at: 2026-04-16
category: infrastructure
tags: [multi-agent, skills, deployment, phase-5b]
---

# Lesson: Multi-Agent Vault Skill 部署

## Context

部署 Phase 5B：OpenClaw 裝 `vault-read` + `vault-write`，Hermes 裝 `coordination/vault-handoff`。目標係畀兩個 agent 經由 shared Obsidian vault 做 cross-agent coordination。

## 關鍵發現

### 1. 非對稱 skill 設計可行
- OpenClaw + Hermes 透過 MCP 互通（雙向）
- OpenClaw 用 2 個 atomic skill（read + write 分開）
- Hermes 用 1 個 workflow skill（handoff lifecycle 包一齊）
- 兩種風格都 work，因為 skill 只係 prompt-level guidance，唔 lock execution

### 2. Skill hot reload
- `launchctl kickstart -k gui/$(id -u)/ai.openclaw.gateway` → gateway restart 後即刻 pick up 新 skill
- Hermes 同理：`launchctl kickstart -k gui/$(id -u)/ai.hermes.gateway`
- 唔使 reboot 電腦，唔使 reinstall

### 3. Skill 驗證命令
- OpenClaw：`openclaw skills` → 列 56 個 bundled + 3 個 managed（skill-vetter + 我哋新加 2 個）
- Hermes：`hermes skills list` → 69 個 skill（68 bundled + 新加 vault-handoff）
- 兩邊新 skill 都 load 成功 ✅

### 4. Scan 邏輯要精準
- 最初用 `grep -q "target_agent: Hermes"` 全檔掃
- 結果 match 到 README.md 入面 example block 內嘅文字 → false positive
- 修正：
  1. Glob `2[0-9][0-9][0-9]-*.md` 只掃 YYYY-MM-DD-*.md
  2. `head -30 | grep -qE '^target_agent:'` 只睇 frontmatter + line-anchored regex
- Lesson：agent 自動化腳本最怕假陽性，filename convention + head limit + anchored regex 係必備

### 5. Skill frontmatter 差異
- OpenClaw 認 `name`, `version`, `description`, `allowed-tools`（可選 `triggers`）
- Hermes 認 `name`, `description`, `version`, `platforms`, `metadata.hermes.tags`, `prerequisites.commands`
- 兩邊都 support 額外 frontmatter（唔識就 ignore）

## 測試結果

### ✅ 機器層 tests（Claude Code 做）
- **Skill load**：OpenClaw 見到 vault-read + vault-write（ready）；Hermes 見到 coordination/vault-handoff
- **Scan 邏輯**：tightened grep 正確 return 0 pending（existing 2026-04-16-vault-system-test.md 係 `status: draft`，合格被 filter 走）
- **Gitleaks scan**：所有新檔 no leaks found
- **Auto-sync**：launchd `com.frankie.vaultsync` 仍 active（每 10 min commit + push）

### ⏸️ 人手 tests（user 要經 Telegram / CLI 驅動）
- **Test 1**：叫 OpenClaw「check handoff」→ 預期 trigger vault-read
- **Test 2**：叫 OpenClaw「寫 handoff 畀 Hermes 諗 5 個 healing music titles」→ 預期 trigger vault-write
- **Test 3**：叫 Hermes「check 有冇嘢要做」→ 預期 trigger vault-handoff，搵到 test 2 寫嘅檔，寫 response

## 要避免

- ❌ Skill 內 grep pattern 太 loose → match 到 README example → false positive handoff
- ❌ 寫 skill 嘅時候忘記 allowed-tools（OpenClaw skill 某啲情況會被 sandbox block）
- ❌ Hermes skill 放錯 category（無 `coordination` 要 mkdir -p）

## 將來可以點樣改進

- **File watcher**：而家 10 min polling 夠用，但 real-time notify（fswatch Shared/handoffs/）可以令 handoff latency 從 10 min 降到秒級
- **Lock 機制**：Hermes 已有 `.lock` file pattern（auth.lock, MEMORY.md.lock），vault 可以 copy
- **Handoff inbox UI**：Obsidian 入面用 Dataview plugin 整 dashboard，睇 pending handoffs
- **MCP native handoff**：而家係 file-based，將來可以 expose `vault.scan_handoffs()` MCP tool，skip filesystem scan

## 遺留問題

- 未驗證 OpenClaw 喺 Telegram group 會唔會主動 suggest 用 vault-* skill（要 user 明確 trigger）
- Hermes 用嘅 personality `kawaii` 可能影響 handoff response 語氣，未測試
- 跨 timezone 問題：handoff `created_at` timestamp format 未強制，有機會兩邊用唔同 format

---

*Generated 2026-04-16 as part of Phase 5B deployment closing.*
