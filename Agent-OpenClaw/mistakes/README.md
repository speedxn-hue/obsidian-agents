# OpenClaw Mistakes

OpenClaw 出錯記錄。目的係避免再犯同一個錯。

## 命名

`YYYY-MM-DD-short-topic.md`

## 模板

```markdown
---
date: 2026-04-16
agent: OpenClaw
severity: low | medium | high
resolved: true | false
---

# Short Title

## 症狀
（發生咗咩？點發現？）

## 原因
（root cause analysis）

## 修正
（做咗咩解決？）

## 防止再發生
（下次點避免？要唔要升級去 Shared/lessons-learned/？）
```

## 升級條件

如果個錯誤或者教訓對其他 agent 都有用，抽出嚟寫入 `Shared/lessons-learned/`。
