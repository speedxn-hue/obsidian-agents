---
learned_by: Claude Code
learned_at: 2026-04-16
category: infrastructure
tags: [skills, llm, description-engineering, openclaw, hermes]
---

# Lesson: Skill Description Engineering（LLM-friendly 寫法）

## Context

Phase 5B 初版 skill 檔用敘述文 description，LLM 唔 auto-trigger。
Diagnostic 揭示 root cause 喺 description wording，唔係 skill loading。

## 關鍵發現

1. **OpenClaw gateway inject 嘅只係 name + description + location**，skill body 係 lazy-load（LLM 要先選，再 read）
2. **Hermes 用 skill_view tool** 做 lazy-load，同樣靠 description 做 selection
3. 兩個 system 都係 **description-driven skill selection**

## LLM-friendly description pattern（work 嘅 bundled skill 都用呢個）

```
<one-line purpose>. Use when user says: (1) <phrase A>, (2) <phrase B>,
(3) <phrase C>. NOT for: <clear exclusion>.
```

## 要避免

- ❌ 敘述文（"Use this before starting any new task..."）
- ❌ 全英文 description if 用戶用其他語言（中 / 英雙語最穩）
- ❌ `triggers:` field（Claude Code convention，OpenClaw / Hermes 唔睇）
- ❌ `allowed-tools:` field（同上）

## 要做

- ✅ Numbered triggers (1)(2)(3)，以用戶實際講嘅 phrase 為準
- ✅ Bilingual（中英 pairs）
- ✅ `NOT for:` 做 disambiguation，減少誤觸發
- ✅ 保留 framework-specific field（Hermes 嘅 `platforms:`, `metadata.hermes.tags:`）

## 應用範圍

將來所有自己寫嘅 skill，description 一律跟呢個 pattern。
