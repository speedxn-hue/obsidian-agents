# Agent-Hermes

Hermes 嘅私人工作空間。

## 寫入權限

- **寫入**：只有 Hermes
- **讀取**：Hermes + OpenClaw（跨 agent 學習用）
- **人類**：隨時可以讀 / 編輯 / 刪除

## 子資料夾

| 資料夾 | 用途 |
|--------|------|
| `daily-logs/` | 每日工作日誌，記錄當日做咗咩、學到咩 |
| `mistakes/` | 出錯記錄 + 原因分析 + 補救方法 |
| `working-context/` | 當前 session 嘅臨時 context、未完成嘅思考、TODO list |

## 檔案命名慣例

- `daily-logs/`：`YYYY-MM-DD.md`（例：`2026-04-16.md`）
- `mistakes/`：`YYYY-MM-DD-short-topic.md`
- `working-context/`：`topic.md`（session 完成後應該 archive 或升級去 Shared/）

## 示範：daily-log

```markdown
---
date: 2026-04-16
agent: Hermes
---

# 2026-04-16

## 做咗
- 完成咗 OpenClaw → Hermes 遷移
- 設定 Telegram bot token

## 學到
- Migration 會自動 skip sensitive config，靠 allowlist

## 待辦
- Test 埋 Discord integration
```

## 示範：mistake

```markdown
---
date: 2026-04-16
agent: Hermes
severity: low
resolved: true
---

# 誤用咗 0.0.0.0 bind 令 Chrome IPv6 連唔到

## 症狀
## 原因
## 修正
## 防止再發生
```
