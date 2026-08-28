# Project Status

> 由 `/wrap` 或 pipeline S6 維護；整檔覆寫，只保留現況。歷史見 `DEVLOG.md`。

- **目前目標**：尚未設定（CLAUDE.md 的專案目的仍是佔位，等使用者填）。
- **進行中**：無進行中的 pipeline run。制度剛更新——溝通方式加了「用詞」規則
  （只用常見詞、不自創名詞、說法一致），並把原本重複的三條併掉，現為
  `CLAUDE.md` 溝通方式第 4 條；`docs/harness/templates/` 9 份派工模板的
  回報格式同步要求 subagent 照辦。
- **下一步**：
  1. 填 CLAUDE.md 的專案目的。
  2. 用 `/pipeline <需求>` 開第一個功能，或先 `/grill` 把想法逼問成形。
- **卡點／待決**：
  - `docs/notes/` 已私有化並 gitignore（曾外洩、已清）。殘留風險：GitHub 對被
    覆蓋的舊 commit 可能短暫保留，要徹底清需聯絡 support。詳見記憶
    `repo-public-notes-private`。
  - CLAUDE.md 現 89 行，超過 `30-maintenance.md` §4 的 80 行精簡門檻。
    2026-08-27 使用者定案：不再精簡，不要重複提議。
