---
created_by: OpenClaw
created_at: 2026-04-16
updated_at: 2026-04-16
category: infrastructure
tags: [phase-5a, discovery, inventory, mac-host]
status: active
---

# Mac Agent Infrastructure Map（Phase 5A Discovery）

呢份 inventory 記錄 Frankie 部 MacBook（user `kjai`）上面兩個 AI agent 嘅完整基礎設施，為 Phase 5B（vault-access skill 落地）做 reference。

> ⚠️ **Secrets policy**：所有 API key / token / password **只記錄路徑**，唔寫入內容。詳見 `SECURITY.md`。

---

## 🟢 OpenClaw

### 運行狀態
- **Status**：✅ running
- **Gateway PID**：`37201`（`openclaw-gateway`）
- **Port**：`18789`（loopback only, bind = `loopback`）
- **Started**：today（由 launchd 自動啟動）

### Binary / 版本
- **CLI entry**：`/opt/homebrew/bin/openclaw` → `/opt/homebrew/lib/node_modules/openclaw/openclaw.mjs`
- **Gateway main**：`/opt/homebrew/lib/node_modules/openclaw/dist/index.js`
- **Runtime**：Node.js（via `/opt/homebrew/opt/node/bin/node`）
- **Version**：`OpenClaw 2026.4.10 (44e5b62)`
- **安裝方式**：`npm install -g openclaw`

### 配置
- **Config dir**：`~/.openclaw/`
- **Main config**：`~/.openclaw/openclaw.json`（5 個時間戳 backup 並存）
- **Key sections**：
  - `agents.defaults.model.primary` = `openrouter/auto`
  - `gateway.port` = 18789
  - `gateway.bind` = `loopback`
  - `gateway.auth.mode` = `token`（token 值 → 只喺 config file 內）
  - `channels.telegram.enabled` = true
  - `channels.telegram.groupPolicy` = `open`
  - `channels.telegram.groups["*"].requireMention` = `false`
  - `channels.telegram.botToken` → 只喺 config file 內
  - `tools.profile` = `coding`
  - `hooks.internal.entries.session-memory` = enabled
  - `hooks.internal.entries.self-improvement` = enabled
  - `plugins.entries.memory-core.config.dreaming.enabled` = true

### Subdirectories（`~/.openclaw/`）
```
agents/       canvas/     credentials/   cron/
devices/      flows/      hooks/         identity/
logs/         media/      memory/        qqbot/
skills/       tasks/      telegram/      workspace/
```

### Skills
- **Location**：`~/.openclaw/skills/`
- **Count**：2
  - `skill-vetter/` — `SKILL.md` + `_meta.json`（v1.0.0，security vetting protocol）
  - `web-automation/` — Node.js package（`package.json` + `node_modules/` + `scripts/`）
- **Skill format**（兩種並存）：
  1. **Markdown-centric**：folder 內 `SKILL.md` 有 frontmatter（`name`, `version`, `description`）+ 正文用 Markdown。可選 `_meta.json` 記 `ownerId`, `slug`, `version`, `publishedAt`
  2. **Code package**：folder 係完整 Node.js package（有 `package.json` + `scripts/*.js`）
- **Loading**：推斷由 gateway 啟動時掃 `~/.openclaw/skills/` 自動 register（未 verify runtime 行為）

### Logs
- **Gateway stdout**：`~/.openclaw/logs/gateway.log`
- **Gateway stderr**：`~/.openclaw/logs/gateway.err.log`
- **Rolling log**：`/tmp/openclaw/openclaw-YYYY-MM-DD.log`（每日一檔）

### Startup（launchd）
- **Plist**：`~/Library/LaunchAgents/ai.openclaw.gateway.plist`
- **Label**：`ai.openclaw.gateway`
- **Mode**：`RunAtLoad=true`, `KeepAlive=true`（常駐）
- **Status**：`launchctl list` → `37201  0  ai.openclaw.gateway`（exit 0, healthy）

### LLM Routing
- **Provider**：OpenRouter（`https://openrouter.ai/api/v1`）
- **Model**：`openrouter/auto`
- **API key storage**：喺 `~/.openclaw/openclaw.json` / auth profile（路徑 only，**not reproduced**）

### External connections
- **Telegram Bot**：connected（token 喺 config file）
- **MCP servers**（outbound）：
  - `playwright`：`npx -y @playwright/mcp`
  - `hermes`：`/Users/kjai/.local/bin/hermes mcp serve` ← **bidirectional bridge to Hermes**

---

## 🟢 Hermes Agent

### 運行狀態
- **Status**：✅ running
- **Gateway PID**：`37280`（`hermes-gateway`）
- **MCP serve subprocesses**：~10+（每次 OpenClaw 或 Claude call MCP 都會 spawn 一個 `hermes mcp serve`）
- **Gateway state**：`running`
- **Telegram platform state**：`connected`

### Binary / 版本
- **CLI entry**：`/Users/kjai/.local/bin/hermes` → `/Users/kjai/.hermes/hermes-agent/venv/bin/hermes`
- **Project root**：`/Users/kjai/.hermes/hermes-agent`
- **Runtime**：Python 3.11.15（在 `venv/`）
- **Version**：`Hermes Agent v0.8.0 (2026.4.8)`
- **安裝方式**：`install.sh` + `uv` + Python venv

### 配置
- **Config dir**：`~/.hermes/`
- **Main config**：`~/.hermes/config.yaml`（268 行，`_config_version: 16`）
- **Key sections**：
  - `model.default` = `openrouter/auto`
  - `model.base_url` = `https://openrouter.ai/api/v1`
  - `model.api_key` → 只喺 config file 內
  - `agent.max_turns` = 60
  - `agent.reasoning_effort` = `medium`
  - `display.personality` = `kawaii`（人格 preset，十幾個內建）
  - `memory.memory_enabled` = true
  - `memory.user_profile_enabled` = true
  - `memory.memory_char_limit` = 2200
  - `memory.user_char_limit` = 1375
  - `session_reset.mode` = `both`（idle 1440 min + 每日 4am）
  - `group_sessions_per_user` = true
  - `security.redact_secrets` = true
  - `security.tirith_enabled` = true（policy sandbox）
  - `TELEGRAM_HOME_CHANNEL` → group ID 喺 config file

### Subdirectories（`~/.hermes/`）
```
hermes-agent/    venv + source code（76 items）
skills/          31 categories
memories/        MEMORY.md + USER.md（shared memory）
sessions/        56 active/recent sessions
cron/            scheduled tasks
logs/            gateway + error logs
cache/           audio_cache, image_cache
bin/             helper scripts
scripts/         runtime scripts
sandboxes/       tirith security sandboxes
```

### Memory Layer
- **Shared memory**：`~/.hermes/memories/MEMORY.md`（agent-level scratchpad，2200 char cap）
- **User profile**：`~/.hermes/memories/USER.md`（per-user profile，1375 char cap）
- **Session state**：`~/.hermes/state.db`（SQLite, ~1.7 MB）
- **Channel routing**：`~/.hermes/channel_directory.json`
- **Auth state**：`~/.hermes/auth.json`

### Skills
- **Location**：`~/.hermes/skills/`
- **Top-level categories**：31（apple, research, devops, productivity, etc.）
- **Total `SKILL.md` files**：68
- **Skill format**：two-level folder → `category/skill-name/SKILL.md`
  - Frontmatter schema：`name`, `description`, `version`, `author`, `license`, `platforms`, `metadata.hermes.tags`, `prerequisites.commands`
  - 正文：Markdown with `## When to Use`, `## When NOT to Use`, `## Quick Reference` 等 section
  - 例：`~/.hermes/skills/apple/apple-reminders/SKILL.md`
- **Loading**：Hermes 啟動掃 `skills/` 自動 register；`config.yaml` 有 `skills.external_dirs: []` 可加 external path
- **Toolsets**：config 定義 `toolsets: [hermes-cli]`，每個 platform 有自己 toolset list（`platform_toolsets.telegram: [hermes-telegram]` 等）

### Logs
- **Gateway stdout**：`~/.hermes/logs/gateway.log`
- **Gateway stderr**：`~/.hermes/logs/gateway.error.log`
- **Log rotation**：config `logging.max_size_mb: 5`, `backup_count: 3`

### Startup（launchd）
- **Plist**：`~/Library/LaunchAgents/ai.hermes.gateway.plist`
- **Label**：`ai.hermes.gateway`
- **Command**：`venv/bin/python -m hermes_cli.main gateway run --replace`
- **Mode**：`RunAtLoad=true`, `KeepAlive.SuccessfulExit=false`（crash 會重啟）
- **Status**：`launchctl list` → `37280  -15  ai.hermes.gateway`（-15 = 上次被 SIGTERM, 已 respawn）

### LLM Routing
- **Provider**：OpenRouter（custom provider `Openrouter.ai`）
- **Model**：`openrouter/auto`
- **Compression summary model**：`google/gemini-3-flash-preview`
- **API key storage**：`~/.hermes/config.yaml` + `~/.hermes/.env`（路徑 only）

### External connections
- **Telegram**：connected
- **MCP servers**（outbound）：
  - `playwright`：`npx -y @playwright/mcp`
  - `openclaw`：`/opt/homebrew/bin/openclaw mcp serve` ← **bidirectional bridge to OpenClaw**
- **Secondary security**：`tirith` policy engine（command sandbox）

---

## 🔗 Agent Interconnect

兩個 agent **已經透過 MCP 互相 mount**：
- Hermes 有 `mcp_servers.openclaw` → 叫得動 OpenClaw 嘅 tools
- OpenClaw 有 `mcp.servers.hermes` → 叫得動 Hermes 嘅 tools
- 兩邊都可以 spawn 對方做 subprocess

即係 Phase 5B 嘅 vault-access skill 只要落其中一邊，另一邊都間接用得到（但為咗 symmetry + performance，建議兩邊都落）。

---

## 🎯 Phase 5B 建議（vault-access skill 落地）

### 複雜度評估：🟢 **Simple**

### 建議放置位置
- **OpenClaw**：`~/.openclaw/skills/vault-access/`
  - 檔案：`SKILL.md`（主入口）+ `_meta.json`（metadata）+ 可選 `scripts/` 放 helper
  - 用 markdown-centric format（跟 `skill-vetter` 同 pattern）
- **Hermes**：`~/.hermes/skills/<category>/vault-access/SKILL.md`
  - Category 建議：`productivity` 或新開 `knowledge-management`
  - Frontmatter：`name`, `description`, `version`, `author: OpenClaw`, `platforms: [macos]`, `prerequisites.commands: [git]`

### Skill 內容要 cover
1. **Read** vault files（`Shared/**`, 自己 Agent-* folder）
2. **Write** 去自己個 folder（OpenClaw 寫 `Agent-OpenClaw/`, Hermes 寫 `Agent-Hermes/`）
3. **Handoff protocol**：寫入 `Shared/handoffs/YYYY-MM-DD-*.md` 加 frontmatter
4. **Lessons**：append `Shared/lessons-learned/`
5. **Safety**：唔好寫 secret（call gitleaks 或 pattern match 先 write）
6. **Git-aware**：寫完唔駛手動 commit（launchd 10 分鐘自動 push）

### Blockers / 要注意
- ⚠️ **SECURITY.md 規則要強制 enforce**：skill 應該 refuse 寫 `~/.openclaw/credentials/`、`.env`、config file path
- ⚠️ **Write conflict**：兩個 agent 同時寫同一份 Shared/ 檔會 race。建議：
  - Agent-specific folder 絕對分隔（唔會衝突）
  - `Shared/handoffs/` 用 timestamp 檔名（已有 convention）
  - Lock 機制考慮用 `.lock` file（睇 Hermes 已有 `auth.lock` / `MEMORY.md.lock` 可以參考 pattern）
- ⚠️ **Vault path hardcode**：`~/ObsidianAgents/`（兩個 agent 都跑喺 `kjai` 用戶下，無問題）
- ⚠️ **Skill discoverability**：OpenClaw 點樣告訴 agent "handoff 收到要去睇"？需要 hook（`session-memory` 已 enabled，可以 piggyback）或者 cron polling

### 下一步建議順序
1. 寫 `vault-access` skill 兩版本（Markdown-centric）
2. Deploy 去兩邊 skills folder
3. 寫 integration test handoff（OpenClaw 寫 → Hermes 讀 → Hermes 回應 handoff → OpenClaw 讀）
4. 觀察 launchd 10 min sync 夠唔夠快；如唔夠快考慮 fswatch event trigger

---

## 📦 Summary Table

| 屬性 | OpenClaw | Hermes |
|------|----------|--------|
| Runtime | Node.js | Python 3.11.15 |
| Version | 2026.4.10 | v0.8.0 (2026.4.8) |
| Gateway PID | 37201 | 37280 |
| Port | 18789 (loopback) | internal IPC |
| Config | `~/.openclaw/openclaw.json` | `~/.hermes/config.yaml` |
| Skills count | 2 | 68 (across 31 categories) |
| Skill format | `SKILL.md` + `_meta.json` 或 Node package | `category/name/SKILL.md` 兩級 |
| Memory | `~/.openclaw/memory/` | `~/.hermes/memories/` + `state.db` |
| launchd label | `ai.openclaw.gateway` | `ai.hermes.gateway` |
| LLM | OpenRouter (auto) | OpenRouter (auto) |
| MCP outbound | playwright, hermes | playwright, openclaw |
| Telegram | enabled, open groups | connected, home channel set |

---

*Generated on 2026-04-16 by OpenClaw during Phase 5A discovery sweep.*
