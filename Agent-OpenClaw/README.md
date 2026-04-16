# Agent-OpenClaw

OpenClaw 嘅私人工作空間。

## 寫入權限

- **寫入**：只有 OpenClaw
- **讀取**：OpenClaw + Hermes（跨 agent 學習用）
- **人類**：隨時可以讀 / 編輯 / 刪除

## 子資料夾

| 資料夾 | 用途 |
|--------|------|
| `daily-logs/` | 每日工作日誌，記錄當日做咗咩、學到咩 |
| `mistakes/` | 出錯記錄 + 原因分析 + 補救方法，避免重蹈覆轍 |
| `working-context/` | 當前 session 嘅臨時 context、未完成嘅思考、TODO list |

## 檔案命名慣例

- `daily-logs/`：`YYYY-MM-DD.md`（例：`2026-04-16.md`）
- `mistakes/`：`YYYY-MM-DD-short-topic.md`（例：`2026-04-16-ssh-timeout.md`）
- `working-context/`：`topic.md`（session 完成後應該 archive 或升級去 Shared/）

## 示範：daily-log

```markdown
---
date: 2026-04-16
agent: OpenClaw
---

# 2026-04-16

## 做咗
- 修咗 Telegram gateway config
- 重啟 daemon

## 學到
- `groupPolicy` 要配合 `requireMention` 先完整

## 待辦
- 測試 group chat 回覆
```

## 示範：mistake

```markdown
---
date: 2026-04-16
agent: OpenClaw
severity: medium
resolved: true
---

# SSH handshake timeout via n8n

## 症狀
## 原因
## 修正
## 防止再發生
```
