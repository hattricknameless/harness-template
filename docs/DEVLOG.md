# Devlog

> 由 `/wrap` 或 pipeline S6 寫入；格式規則以 `.claude/skills/wrap/SKILL.md` 為唯一權威來源。

## 2026-07-06（第二則）
- 建立版本命名規範（`docs/harness/50-versioning.md`）與 CLAUDE.md「溝通方式」節；專案 git 化。
- 決定：一個出貨單位只掛一個 `vX.Y.Z`，標題格式由 `.githooks/commit-msg` 強制；已執行 `git config core.hooksPath .githooks` 啟用並實測會攔違規標題。
- 未完：CLAUDE.md 專案目的仍是佔位；`_backup/` 超額副本待清。

## 2026-07-06
- 建立開發 pipeline（`docs/harness/40-pipeline.md`＋`/pipeline`）與輔助 skills（`/grill`、`/wrap`、`/status`），新增 STATUS／DEVLOG 兩檔。
- 決定：人類 gate 兩個（S1 規格、S2 設計）；主對話 Opus 指揮、工人照 10-dispatch 調度表用 sonnet/haiku。
- 未完：CLAUDE.md 專案目的仍是佔位。

## 2026-07-05
- Fable 5 建制整套工作制度：CLAUDE.md 路由＋硬規則、docs/harness/ 00–90、五份派工模板。
- 決定：制度以「Sonnet 跑得動」為底線設計；三大失效模式見 `docs/harness/00-design-baseline.md`。
