---
name: pipeline
description: 從需求到驗收的自動開發流程（規格→設計→拆解→TDD 實作→兩軸驗收）。用法：/pipeline <需求描述>；修 bug 用 /pipeline fix <描述>。
disable-model-invocation: true
---

# 開發 Pipeline

流程本體是 `docs/harness/40-pipeline.md`，本 skill 只負責進場：

1. 讀 `docs/harness/40-pipeline.md`，照 S0–S6 執行；派工與升降級規則照
   `docs/harness/10-model-dispatch.md`，判斷疑難照 `docs/harness/20-judgment.md`。
2. 解析 `$ARGUMENTS`：
   - 空 → 先問使用者要做什麼。
   - 以 `fix` 開頭 → 走 40-pipeline.md 的「Bug 修復模式」。
   - 其他 → 視為功能需求。
3. 知道要做什麼之後、進 S0 之前，檢查 `docs/specs/` 有無未完成的 run
   （某個 tasks.md 還有非 done 的片）：有 → 問使用者「續跑它，還是新開一個？」
   （這一問可與 S1 的提問合併，不必單獨停一次。）
4. 必經的人類 gate 有兩個：S1 規格核准、S2 設計核准。S2 核准後不再停下
   等指令，一路自動跑到 S6 收尾回報；中途只有命中 20-judgment §3「必問」
   條件才回頭問。
