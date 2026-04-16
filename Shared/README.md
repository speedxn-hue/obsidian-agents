# Shared

兩個 agent（OpenClaw + Hermes）共用嘅協作空間。

## 寫入權限

- **寫入**：OpenClaw + Hermes 都可以
- **讀取**：所有人
- **衝突處理**：跟 handoff protocol，或者用 Git merge 處理

## 子資料夾

| 資料夾 | 用途 |
|--------|------|
| `lessons-learned/` | 已驗證嘅經驗教訓，由 mistakes 升級而來。Agent 下次遇到類似情況應該查呢度 |
| `decisions/` | 重大決定記錄（ADR 風格），例如揀用邊個 tool、點解咁設計 |
| `handoffs/` | 跨 agent 任務交接。**必須跟 `handoffs/README.md` 入面定義嘅 format** |
| `projects/` | 長期項目嘅 context、roadmap、status |

## 寫入規則

1. **Lessons learned** 係 append-only — 只可以加新 entry，唔可以改舊 entry（用 Git history 追）
2. **Decisions** 一旦 `status: accepted` 就唔好改，要改嘅話新開一個 decision 話覆蓋咗舊嗰個
3. **Handoffs** 由 source agent 建立、target agent `status: completed` 後封檔
4. **Projects** 隨時可以更新，但每次大改要喺 `changelog` section 補一句

## 檔案命名慣例

- `lessons-learned/`：`YYYY-MM-DD-topic.md`
- `decisions/`：`ADR-NNN-topic.md`（NNN 係 zero-padded 序號，例：`ADR-001-choose-obsidian.md`）
- `handoffs/`：`YYYY-MM-DD-HHMM-source-to-target-topic.md`
- `projects/`：`project-slug.md`
