# CLAUDE.md

## 專案目的

> （佔位：由使用者填寫。填寫前，本專案唯一的內容是 `docs/harness/` 下的
> 工作制度檔——先讀完路由再動手。）

## 目錄結構

```
/CLAUDE.md                  ← 本檔：只放路由與硬規則，不放內容
/docs/harness/              ← 工作制度檔（編號 = 閱讀順序）
  00-design-baseline.md     ← 制度的設計依據（改制度前必讀）
  10-model-dispatch.md      ← 模型調度：派工、升降級、驗證
  20-judgment.md            ← 判斷 rubric：升級／完成／該問／換路／品質底線
  30-maintenance.md         ← 如何安全地更新以上檔案
  40-pipeline.md            ← 開發 pipeline：需求→規格→設計→拆解→實作→驗收
  90-letter.md              ← 建制者給未來 session 的信
  templates/                ← 派工 prompt 模板（搜尋/實作/重構/研究/審查/
                               設計/TDD單片/整功能兩軸審查）
/.claude/skills/            ← 指令入口：pipeline（開發流程，薄殼→40-pipeline）、
                               grill（逼問訪談→CONTEXT/ADR）、wrap（收工）、
                               status（現況摘要，唯讀）、calibrate（分析歷史
                               session 校準制度與記憶）
/docs/STATUS.md             ← 專案現況快照（/wrap 維護，整檔覆寫）
/docs/DEVLOG.md             ← 開發日誌（只追加，一 session 一則）
/docs/specs/                ← pipeline 產物：每功能一目錄（spec/design/tasks）
/docs/notes/                ← 研究報告與長產物落檔處
/src、/tests 等             ← 產品程式碼（尚未建立，開工時再建）
```

## 路由（何時讀哪份）

| 情境 | 讀 |
|---|---|
| 使用者要開發功能或修 bug（功能級）、或打 `/pipeline` | `docs/harness/40-pipeline.md` |
| 要派 subagent、選模型、決定誰做什麼 | `docs/harness/10-model-dispatch.md` |
| 拿不準「該不該升級／算不算完成／該不該問使用者／該不該換路」 | `docs/harness/20-judgment.md` |
| 準備寫派工 prompt | `docs/harness/templates/` 選對應模板照填 |
| 想修改 `docs/harness/` 或 `.claude/skills/`、或踩坑後要寫教訓 | `docs/harness/30-maintenance.md` |
| 新 session 想接續之前的工作 | `docs/STATUS.md`（或使用者打 `/status`） |
| 新 session 開場、或對這套制度的來歷有疑問 | `docs/harness/90-letter.md` |

## 硬規則（違反會出大事，無例外）

1. **不自驗**：任何「完成」宣告前，驗收必須由 fresh-context subagent 或
   實際執行（測試、read-back）確認。自己看自己寫的東西不算驗收。
2. **誠實回報**：測試失敗就說失敗並附輸出；沒做的步驟就明說沒做。
   絕不寫「應該可以」「理論上通過」。
3. **重試上限**：同一子任務同一做法最多兩輪（haiku 只給一輪），之後必須
   升級模型或換路（見 `10-model-dispatch.md` 第 5 節）。
4. **長產物落檔**：超過約 30 行的產出寫進檔案，對話裡只留路徑與摘要。
5. **改制度檔先備份**：修改 `docs/harness/` 或本檔前，先照
   `30-maintenance.md` 的備份程序執行。
6. **不可逆操作先確認**：刪檔、覆寫非自己建立的檔案、對外發布，
   動手前先向使用者確認。

判斷空間：以上未涵蓋的情況，用 `20-judgment.md` 的 rubric 自行判斷；
rubric 也沒涵蓋的，選保守的那條路並在回報中標明你做了什麼假設。
