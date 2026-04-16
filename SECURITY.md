# SECURITY

呢個 vault 會 push 去 GitHub（雖然 private），同時可能會被其他 agent / session 讀取。任何寫入前，先讀呢份文件。

## 寫入前嘅 Golden Rule

> **如果呢個 vault 出街，我會唔會後悔？**

如果答案係「會」，就唔好寫。寫之前要問自己呢條問題。

---

## 🚫 永遠唔可以寫入 vault

寫呢啲嘢係重大違規，應該即刻修正 + 喺 `Agent-*/mistakes/` 寫記錄。

| 類別 | 例子 | 正確做法 |
|------|------|---------|
| API key / token | `sk-...`、`ghp_...`、`AIza...` | 寫 `[REDACTED]` 或 `[API_KEY]` |
| Password | login password、MFA secret、recovery code | `[REDACTED]` |
| SSH private key | `-----BEGIN OPENSSH PRIVATE KEY-----` | **絕對唔寫**，連 fingerprint 都唔需要 |
| Telegram bot token | `123456:ABC-...` | `[TELEGRAM_BOT_TOKEN]` |
| 信用卡 / 銀行資料 | 卡號、CVV、戶口號碼 | **絕對唔寫** |
| 完整 email | `john.doe@example.com` | `user@[redacted]` 或 `user_N` |
| 家居地址 / 電話 | `123 Main St`、`+852-...` | **絕對唔寫** |
| 完整姓名（如非必要） | `John Doe`、`陳小明` | 用代號例如 `user` / `owner` |
| 其他人嘅 PII | 朋友 / 同事 / 客戶嘅任何識別資料 | **絕對唔寫** |

---

## ⚠️ 可以寫，但要小心

| 類別 | 原則 |
|------|------|
| 用戶內部 ID | 可以寫 `user_8122490033` format，但**唔好連埋真名 / email** |
| 項目名 | 可以，但如果項目名本身已經敏感（例如內部 codename），用代號 |
| 公開 URL | 可以，但要確認係真正 public（唔係 signed URL） |
| Config key name | 可以寫 `TELEGRAM_BOT_TOKEN` 作為 variable name，但**唔好寫 value** |
| Hostname / IP（public） | 公開 domain 可以，但唔好寫內部 IP / VPN address |

---

## 🧹 事後清理

如果唔小心寫咗敏感資料：

1. **即刻改檔案**，將敏感部分改成 `[REDACTED]`
2. **`git add` + `git commit`** — 但注意 Git history 仍然會保留舊 content
3. **Rotate credential**：將泄漏嘅 key / token / password **立即作廢**，唔好估冇人睇到
4. 如果 commit 已 push 去 GitHub，用 `git filter-repo` 或者 BFG 清 history，然後 force push
5. 喺 `Agent-*/mistakes/` 寫低事件，升級去 `Shared/lessons-learned/` 畀對方參考

---

## Agent 嘅責任

每個 agent 寫入任何 vault 檔案前，應該：

1. 讀咁樣寫會唔會泄漏 secret
2. 用 `[REDACTED]` 代替任何可疑 value
3. 對 user PII 零容忍
4. 如果唔肯定，**唔好寫**，等人類確認

---

## 人類嘅責任

- 定期 review 新 commit（至少每週）
- 發現 agent 泄漏資料即刻 rotate credential + 清 Git history
- 考慮用 `git-secrets` 或 `gitleaks` 做 pre-commit scan（Phase 3 可以加）
