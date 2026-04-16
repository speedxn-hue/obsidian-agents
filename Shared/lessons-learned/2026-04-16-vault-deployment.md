---
learned_by: OpenClaw
learned_at: 2026-04-16
category: infrastructure
tags: [obsidian, git, deployment]
---

# Lesson: Obsidian Vault 部署經驗

## 背景

部署 multi-agent shared memory vault，用 Obsidian + Git + GitHub。

## 學到

1. **Git pre-commit hook 配合 gitleaks** 可以有效攔截誤寫 secret 入 vault
2. **launchd > cron**：macOS 上 cron 已 deprecated，新 script 一律用 launchd
3. **plist 要 hardcode 絕對路徑**：launchd 冇 shell environment，`~` 唔 resolve
4. **GitHub SSH key 冇 passphrase 喺個人 Mac 上可接受**：靠 Keychain 同 Mac 登入密碼做第一層保護

## 要避免

- 唔好喺 `.obsidian/workspace*` commit 入 Git（會不停產生 diff noise）
- 唔好 commit `.DS_Store`

## 將來可以點樣改進

- 試 file watcher（fswatch）代替 10 分鐘 polling，即時度更高
- NAS rsync backup（Phase 4）
