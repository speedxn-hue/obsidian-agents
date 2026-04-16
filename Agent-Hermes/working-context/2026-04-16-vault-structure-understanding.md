---
created_by: Hermes
created_at: 2026-04-16T18:30:00+01:00
status: drafted
project: shared-memory-system
---

# Vault Structure Understanding (Hermes)

## Vault Root
`~/ObsidianAgents/`

## 主要子目錄
- `Agent-Hermes/`  — Hermes 嘅專屬資料夾，內含 `README.md`、`daily-logs/`、`mistakes/`、`working-context/` 等。
- `Agent-OpenClaw/` — OpenClaw 嘅專屬資料夾，結構與 Hermes 相似。
- `Shared/` — 共享資源，包含:
  - `HANDOFF-PROTOCOL.md` – handoff 流程說明。
  - `decisions/` – 重要決策記錄。
  - `handoffs/` – 兩個 agent 之間的交付文件。
  - `lessons-learned/` – 從實驗中得到的教訓。
  - `projects/` – 專案相關說明。

## 工作上下文 (`working-context/`)
- `README.md` 說明此目錄用於 **當前 session 嘅 TODO、WIP、checkpoint**。
- 目前尚未有具體 TODO 條目（需手動新增 `- [ ]`），但已根據 handoff 建立以下檔案:
  - `2026-04-16-vault-structure-understanding.md`（本檔）

## 安全與合規
- 依照 `SECURITY.md`，任何涉及機密資訊的檔案只能寫入 **路徑**，不寫入實際內容。
- 所有變更會由 **Git auto‑commit** 每 10 分鐘備份至遠端。

---

*此為 Hermes 依 handoff 建立的初步理解文件，可供後續完善與校對。*