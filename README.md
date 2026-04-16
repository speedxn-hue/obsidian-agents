# ObsidianAgents — AI Multi-Agent Shared Memory Vault

一個由兩個 AI agent（OpenClaw 同 Hermes）共用嘅長期記憶系統，用 Obsidian 管理，Git 版本控制，GitHub 做 offsite backup。

## 設計理念

- **Agent 獨立記憶**：每個 agent 有自己嘅工作空間（daily logs、mistakes、working-context），對方可讀但唔可寫。
- **Shared 協作層**：兩個 agent 共用 lessons-learned、decisions、handoffs、projects。handoff 跟固定 protocol。
- **人類可讀可編輯**：全部 Markdown，方便用戶隨時睇、改、搜尋。
- **安全優先**：見 `SECURITY.md` — vault 永遠唔應該存 secrets。

## 資料夾導覽

```
ObsidianAgents/
├── README.md              ← 你而家睇緊
├── SECURITY.md            ← 寫入前必讀
├── .gitignore
│
├── Agent-OpenClaw/        ← 只 OpenClaw 寫，Hermes 可讀
│   ├── daily-logs/
│   ├── mistakes/
│   └── working-context/
│
├── Agent-Hermes/          ← 只 Hermes 寫，OpenClaw 可讀
│   ├── daily-logs/
│   ├── mistakes/
│   └── working-context/
│
└── Shared/                ← 兩個 agent 都可以讀寫
    ├── lessons-learned/
    ├── decisions/
    ├── handoffs/          ← 重要！跟 handoff protocol
    └── projects/
```

## 寫入原則

1. 寫之前問自己：「如果呢個 vault 出街，我會唔會後悔？」（見 SECURITY.md）
2. 跟資料夾嘅檔案命名慣例（通常係 `YYYY-MM-DD-topic.md`）
3. Handoff 必須跟 `Shared/handoffs/README.md` 入面定義嘅 format
4. Agent 唔可以寫入對方嘅 Agent-* 資料夾

## 相關工具

- **Obsidian**：日常睇、編輯
- **Git**：版本控制，每個 commit 係一個 snapshot
- **GitHub (private)**：offsite backup（`speedxn-hue/obsidian-agents`）
