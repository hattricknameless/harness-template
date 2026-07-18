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
  50-versioning.md          ← 版本與命名：一個工作單位只掛一個編號
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
/.githooks/                 ← commit-msg hook（50-versioning 的強制器；git init 後
                               執行 `git config core.hooksPath .githooks` 啟用）
/src、/tests 等             ← 產品程式碼（尚未建立，開工時再建）
```

## 路由（何時讀哪份）

| 情境 | 讀 |
|---|---|
| 使用者要開發功能或修 bug（功能級）、或打 `/pipeline` | `docs/harness/40-pipeline.md` |
| 要派 subagent、選模型、決定誰做什麼 | `docs/harness/10-model-dispatch.md` |
| 拿不準「該不該升級／算不算完成／該不該問使用者／該不該換路」 | `docs/harness/20-judgment.md` |
| 準備寫派工 prompt | `docs/harness/templates/` 選對應模板照填 |
| 要下版本號、寫 commit 標題、或幫工作單位取編號 | `docs/harness/50-versioning.md` |
| 想修改 `docs/harness/` 或 `.claude/skills/`、或踩坑後要寫教訓 | `docs/harness/30-maintenance.md` |
| 新 session 想接續之前的工作 | `docs/STATUS.md`（或使用者打 `/status`） |
| 新 session 開場、或對這套制度的來歷有疑問 | `docs/harness/90-letter.md` |

## 溝通方式（每次回應與寫給使用者讀的文件都適用）

1. **白話講結果，不講機制**：說「發生什麼事、結果會怎樣」。檔名與功能名
   可以出現；函式名、變數名、內部術語、code 片段、`檔案:行號` 預設不出現，
   使用者說「多講一點／給我看 code」才加一層。術語非用不可時當場附一句中文解釋。
2. **回應骨架**：怎麼了（一句話）→ 做了什麼（表格：項目／解決了什麼）→
   怎麼驗證（表格：操作／預期）→ 下一步（要使用者做什麼；問問題附選項與建議答案）。
3. **表格優先**於長串 bullet；解釋任何流程（步驟、資料流、狀態轉換）
   畫 mermaid 圖，不用一串文字描述。
4. **長報告白話化**：每張表附一句「讀法」；代號與縮寫當場附中文；
   假設讀者不想動腦解析技術細節。
5. **語氣**：繁中、直接、無填充語；不安撫、不道歉開場；修了就說修了、
   沒修就說沒修。換方向後不再提舊脈絡。
6. **文件寫作比照辦理**：README、報告、STATUS、spec 的說明性文字都照第 1–5 條寫。
   兩條校準：(a) 白話不等於稀釋——完整規格與數字要留著，是「講清楚」不是「講少」；
   (b) 縮寫與代號第一次出現就附全稱或中文，圖表欄位標題寧可長而清楚，不用縮寫。
   例外只給「不是寫給使用者讀的內部產物」：給 agent 讀的 spec/design/tasks
   結構欄位、程式碼、ADR 照各自格式規範，不硬白話化。但**任何要使用者核准或
   過目的東西——pipeline gate 的確認卡、給使用者的設計摘要——一律照第 1–5 條
   白話，不得用英文欄位標題或內部術語**（2026-07-19 定案：gate 產物必須是使用者
   讀得下去的白話確認卡，見 `docs/harness/40-pipeline.md` §S1）。

## 硬規則（違反會出大事，無例外）

1. **不自驗**：任何「完成」宣告前，驗收必須由 fresh-context subagent 或
   實際執行（測試、read-back）確認。自己看自己寫的東西不算驗收。
2. **誠實回報**：測試失敗就說失敗並附輸出；沒做的步驟就明說沒做。
   絕不寫「應該可以」「理論上通過」。
3. **重試上限**：同一子任務同一做法最多兩輪（haiku 只給一輪），之後必須
   升級模型或換路（見 `10-model-dispatch.md` 第 5 節）。
4. **長產物落檔**：超過約 30 行的產出寫進檔案，對話裡只留路徑與摘要。
5. **改制度檔靠 git 保護**：修改 `docs/harness/` 或本檔前，先確認該檔
   沒有未 commit 的改動（有就先 commit），確保 git 留有可回復的版本；
   改完照 `30-maintenance.md` 驗收並在收尾時 commit。不做 `_backup/` 副本
   （2026-07-07 使用者定案廢除）。
6. **不可逆操作先確認**：刪檔、覆寫非自己建立的檔案、對外發布，
   動手前先向使用者確認。

判斷空間：以上未涵蓋的情況，用 `20-judgment.md` 的 rubric 自行判斷；
rubric 也沒涵蓋的，選保守的那條路並在回報中標明你做了什麼假設。
