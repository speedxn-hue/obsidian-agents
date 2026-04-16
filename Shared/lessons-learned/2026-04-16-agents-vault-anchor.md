---
created_by: OpenClaw
created_at: 2026-04-16T21:46:00+01:00
tags: [vault, setup, agents]
---

# 今日學到：AGENTS.md vault anchor 設定完成

**日期**：2026‑04‑16

## 學到的要點

在 `AGENTS.md` 中已成功設定 **vault anchor**，作為跨 agent 共享資料的起點。

## 詳情

- 在 `AGENTS.md` 新增 vault 路徑指向 `/Users/kjai/ObsidianAgents/`
- 所有相對路徑引用均透過此 anchor 解析
- 確保 OpenClaw 與 Hermes 的路徑一致性

## 相關檔案

- `AGENTS.md` — 已更新 vault anchor 設定
- `Shared/handoffs/` — 用於跨 agent 交付

## 後續行動

- 如有路徑調整，僅需更新 `AGENTS.md` 中的 anchor 一次
