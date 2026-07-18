---
name: pipeline
description: 開發流程（規格→設計→拆解→TDD 實作→兩軸驗收）的手動入口。注意：pipeline 已是預設自動流程，任何功能/fix 交辦都自動走 40-pipeline，不需打此指令；此指令僅作手動強制發動、指定續跑或指定 fix 之用。
disable-model-invocation: true
---

# 開發 Pipeline（手動入口）

**pipeline 現在是預設**：使用者交辦任何功能或 bug 修復，主對話模型都自動照
`docs/harness/40-pipeline.md` 走，不需要使用者打 `/pipeline`。本 skill 只在使用者
**想手動強制發動、或明確指定 fix／續跑**時進場，非主要入口。

流程本體是 `docs/harness/40-pipeline.md`，本 skill 只負責進場：

1. 讀 `docs/harness/40-pipeline.md`，照 S0–S6 執行；派工與升降級規則照
   `docs/harness/10-model-dispatch.md`，判斷疑難照 `docs/harness/20-judgment.md`。
2. 解析 `$ARGUMENTS`：
   - 空 → 先問使用者要做什麼。
   - 以 `fix` 開頭 → 走 40-pipeline.md：fix 與功能同流程，深度按份量縮放。
   - 其他 → 視為功能需求。
3. 知道要做什麼之後、進 S0 之前，檢查 `docs/specs/` 有無未完成的 run
   （某個 tasks.md 還有非 done 的片）：有 → 問使用者「續跑它，還是新開一個？」
   （這一問可與 S1 的提問合併，不必單獨停一次。）
4. 人類 gate：S1 確認卡核准永遠必經；S2 設計核准是完整深度才有的第二道
   gate，小份量可併入 S1 或省略（見 40-pipeline §0 份量分級）。最後一道 gate
   通過後不再停下等指令，一路自動跑到 S6 收尾回報；中途只有命中 20-judgment
   §3「必問」條件才回頭問。
