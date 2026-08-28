# Devlog

> 由 `/wrap` 或 pipeline S6 寫入；格式規則以 `.claude/skills/wrap/SKILL.md` 為唯一權威來源。

## 2026-08-27
- 溝通規則加「用詞」一條（只用常見詞、不自創名詞、術語首次附解釋、說法一致），再把原第 1、4、7 條合併成 6 條，用詞規則成為第 4 條；9 份派工模板的回報格式同步加一行。commit 61ff70b、5bdee79、6c08c60（docs:，非出貨故無版本號）。
- 決定：CLAUDE.md 現 89 行、超過 80 行精簡門檻，使用者定案不再精簡——溝通方式與硬規則必須留在會自動載入的檔裡。
- 驗收：合併由獨立閱卷者比對前後版本，判定無規則消失或變弱（被刪的一句說明已補回）。
- 未完：CLAUDE.md 專案目的仍佔位。

## 2026-08-16（事後補記，依 commit 內容還原）
- 把實戰教訓抽成通則寫進制度規則本文（不留案例）；溝通規則移除「每張表附一句讀法」。commit ef8bd24、2034dc2。
- 未完：當時未寫 DEVLOG，此則為 2026-08-27 session 補記。

## 2026-07-22
- pipeline 驗收改三角色分立（出題／實作／閱卷獨立、閱卷升 opus）＋清理公開 repo 洩漏。commit 40406ac、8332b2b（docs:，非出貨故無版本號）。
- 發現／處理：repo 是公開的，`docs/notes/` 校準分析含跨專案機密曾外洩約一天，已 filter-repo 全歷史移除＋force-push、`docs/notes/` 全 gitignore（教訓見 calibrate SKILL；記憶 `repo-public-notes-private`）。
- 未完：CLAUDE.md 專案目的仍佔位。

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
