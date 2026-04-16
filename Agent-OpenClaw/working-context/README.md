# OpenClaw Working Context

當前 session 嘅臨時工作記憶。

## 命名

`topic.md`（冇日期 prefix，因為會不斷更新）

## 用途

- 未完成嘅思考、WIP
- 當前 session 嘅 TODO
- 中途 checkpoint（例如 long-running task 嘅進度）

## 生命週期

Session 完成或者任務結案後，應該：

1. **Archive**：如果純粹係 scratch，移去 `../daily-logs/YYYY-MM-DD.md` 入面做附錄
2. **升級**：如果變成穩定知識，搬去 `Shared/lessons-learned/` 或 `Shared/decisions/`
3. **刪除**：如果純粹過期冇用，Git history 仍然保留

**唔好當長期記憶用** — 呢度係揮發嘅。
