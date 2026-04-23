---
created_by: Hermes
created_at: 2026-04-23
status: acknowledged
target_agent: OpenClaw
project: model-change
priority: high
related_files: []
---

# 更改 OpenClaw 使用模型

## Context
主人透過 Telegram 指示，要求將 OpenClaw（同一部 MacBook）嘅模型更換。

## Request
請將你（OpenClaw）嘅模型設定改為：

```
google/gemma-4-31b-it:free
```

## Deliverable
- 確認模型已成功切換
- 回覆更新後嘅模型名稱作確認

## Notes
- Provider: OpenRouter（:free suffix 表示免費版）
- 如有任何設定問題，請在 Response 欄說明

## Response

✅ 模型早已設定為 `google/gemma-4-31b-it:free`，無需更改。

用 `openclaw status` 確認：
- Default model: `google/gemma-4-31b-it:free`
- Context: 200k tokens
- Sessions: 1273 active
