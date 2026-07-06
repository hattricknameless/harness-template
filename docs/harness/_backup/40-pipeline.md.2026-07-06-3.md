# 40 — 開發 Pipeline：需求 → 規格 → 設計 → 拆解 → 實作 → 驗收

> 讀者：主對話模型（指揮官）。觸發：使用者打 `/pipeline`，或提出「做一個功能」
> 等級的需求。單點小修（改一兩個檔、行為明確）不走本檔，直接照 10/20 辦。
> 本檔只管**流程與產物**；派給誰、重試上限、驗證不自驗等底層規則在
> `10-model-dispatch.md` 與 `20-judgment.md`，本檔不重複、衝突時以它們為準。
> 方法論來源：mattpocock/skills 萃取，全文見 `docs/notes/mattpocock-skills-extract.md`。

## 0. 總覽

| 階段 | 做什麼 | 誰做 | 產物 | 模板 |
|---|---|---|---|---|
| S0 摸底 | 掃現況與慣例 | Explore（haiku/sonnet） | 口頭結論 | `templates/search.md` |
| S1 規格 | 逼問對齊 → spec | 指揮官 ↔ 使用者 | `spec.md` | 本檔 §S1 |
| S2 設計 | 介面與 seam | Plan（sonnet/opus） | `design.md` | `templates/design.md` |
| S3 拆解 | 切 vertical slice | 指揮官 | `tasks.md` | 本檔 §S3 |
| S4 實作 | 逐片 red→green | general-purpose（sonnet） | 程式碼＋測試 | `templates/tdd-task.md` |
| S5 驗收 | 兩軸審查＋重構 | fresh-context（sonnet） | 審查報告 | `templates/feature-review.md` |
| S6 收尾 | 記錄與誠實回報 | 指揮官 | 更新的文件 | — |

- **產物目錄**：`docs/specs/<YYYYMMDD-slug>/`（spec.md、design.md、tasks.md、
  審查報告）。一個功能一個目錄。
- **必經的人類 gate 有兩個：S1 規格核准、S2 設計核准**。S2 核准後 S3–S6
  全自動跑完，中途只有命中 `20-judgment.md` §3「必問」條件時才回頭問使用者。
- **中斷恢復**：知道需求後、進 S0 之前，先看 `docs/specs/` 有無 tasks.md
  未全部完成的目錄；有就問使用者續跑還是新開，續跑則從 tasks.md 的狀態接續。

## S0 摸底

派 Explore 掃：相關模組、既有慣例（測試風格、目錄結構）、可複用的 seam。
若存在 `CONTEXT.md`（詞彙表）與 `docs/adr/`，指揮官自己讀——之後所有階段的
用詞照 glossary、決策尊重 ADR。

**完成判準**：能說出「這個需求會碰到哪些既有東西」，或確認是綠地專案。
（綠地且需求是從零開始 → S0 可跳過。）

## S1 規格（grill → spec.md）

指揮官與使用者對齊需求，規則：

1. **一次只問一個問題，每題必附建議答案**。一次拋一串問題會讓人無所適從。
2. **能靠讀碼回答的不問使用者**（派 Explore 查）。
3. 問到雙方對「要做什麼、怎麼驗收」沒有歧義為止；沒有歧義的需求不硬問。
4. 使用者若說「全自動／不要問我」：改為批次列出所有決策點＋你採用的建議答案，
   全部寫進 spec 的 Assumptions 段，直接進 gate。

**spec.md 欄位**（依序）：
- `## Problem` — 使用者視角的問題。
- `## Solution` — 使用者視角的解法。
- `## User Stories` — 編號列表，格式固定 `As a <角色>, I want <功能>, so that <效益>`，
  盡量窮盡各面向。
- `## Implementation Decisions` — 模組、介面、schema、API 合約等決策。
  **禁止寫具體檔案路徑與程式碼片段**（會過時）；例外：prototype 產出的
  狀態機／reducer／type shape 片段可內嵌並註明來源。
- `## Testing Decisions` — **seam（測試邊界）清單**：測試只打在這裡。
  越少越好，理想是 1 個；優先複用既有 seam。此清單需使用者點頭。
- `## Out of Scope` — 明列不做什麼。
- `## Assumptions` — 未經使用者確認、由你代答的決策（全自動模式必填）。

**Gate**：使用者對 spec.md 說「可以」才進 S2。**核准前禁止動任何程式碼。**

**完成判準**：spec.md 落檔、seam 清單被核准（或以 Assumptions 標明代答）。

### 可選支線：prototype（拋棄式原型）

規格階段對「狀態模型合不合理／UI 該長怎樣」拿不準時，先派 sonnet 做拋棄式
原型回答那一個問題：一個指令能跑、不持久化、不寫測試、不抽象化，
問題答完就刪（答案記進 spec）。禁止把原型碼直接搬進正式程式。

## S2 設計（design.md）

照 `templates/design.md` 派 Plan agent（一般 sonnet；跨檔架構或約束互相牽制
達 3 個以上用 opus）。

**高風險設計（介面難逆轉、影響超過 3 個模組）→ Design It Twice**：
平行派 2–3 個 agent，各給不同的設計約束（例：「介面最小化」vs.「為最常見
呼叫者優化」vs.「ports & adapters」），再派一個評審 agent 用
深度／locality／seam 位置比較並給出**單一推薦**（可以是混合方案）。

**驗**：派 haiku read-back——「照這份設計能不能直接開工？有無內部矛盾？」
（`templates/review.md`）。不過就退回修。

**Gate**：read-back 通過後，把 design.md 的摘要（模組、seam、要動的檔案、
最有風險的決策）給使用者過目，點頭才進 S3。

**完成判準**：design.md 落檔、read-back 判定 PASS（退回重修計入
`10-model-dispatch.md` §5 的重試輪數）、且使用者核准。

## S3 拆解（design → tasks.md）

指揮官把設計切成 **vertical slice（tracer bullet）**：

- 每片是**貫穿所有整合層的薄縱切片**（schema＋邏輯＋介面＋測試都含），
  不是「先做完整個資料層」的橫切片。
- 每片完成後**可獨立展示或驗證**。
- 發現「先整理結構才好下刀」→ prefactor 片放最前面：
  *make the change easy, then make the easy change*。
- 每片大小以 **sonnet 一次派工能完成**為準；拿不準就切小。

**tasks.md 格式**：頂部一行指向 spec.md；然後每片一節：

```
## T1：<標題>
- 狀態：pending | in-progress | done | blocked
- Blocked by：<片編號或無>
- 涵蓋 user stories：<編號>
- What to build：<端到端行為描述，講「系統該做什麼」，不講改哪個檔案>
- 驗收 checklist：<每條可獨立客觀驗證，禁止「運作正常」這種空話>
- Out of scope：<這片不做的>
```

**完成判準**：spec 的每個 user story 至少被一片涵蓋；依賴排好序；
每片的驗收 checklist 逐條可驗。

## S4 實作（逐片 red→green）

依依賴序逐片照 `templates/tdd-task.md` 派工（sonnet 預設；純機械式套模式
可 haiku）。核心紀律寫在模板裡：先紅後綠、測試只打核准的 seam、
不重構、不加投機功能。

指揮官每片收工時做三件事：
1. 核對回報的三段式（做了什麼／驗證輸出／沒做什麼），驗證輸出必須是實跑結果。
2. 更新 tasks.md 該片狀態。
3. 決定下一片或處理卡點。

**卡住**：照 `10-model-dispatch.md` §5 升降級。**難 bug** 另有紀律（見下方
「診斷支線」），禁止讓 subagent 原地重試超過上限。

**完成判準**：tasks.md 全部 done，每片有實跑測試輸出為證。

### 診斷支線（難 bug 時進入，適用任何階段）

核心：**先建立會變紅的緊迴圈，才准開始假設。**

1. **建迴圈**：一個能命中該 bug 確切症狀、秒級、確定性、agent 可無人值守
   執行的指令（failing test → 腳本 → bisect harness，由簡入繁）。
   在這裡花不成比例的力氣；沒有迴圈就去讀碼建理論是本支線要禁止的行為。
   真的建不出來 → 停下，向使用者要重現環境／捕獲產物（log、trace）。
2. **重現＋最小化**：確認紅的是使用者描述的那個症狀；一次剪一個元素，
   剪到「拿掉任一個就變綠」。
3. **假設**：先列 3–5 個排序過的可證偽假設（「若 X 是原因，改 Y 應使 bug
   消失」），才准動手驗證；講不出預測的假設丟掉。
4. **插樁**：一次改一個變數；debugger 優於針對性 log；禁止「全部 log 再
   grep」；每條 debug log 加唯一前綴（如 `[DEBUG-a4f2]`）供清理。
   效能問題：先建 baseline 量測再二分，不靠 log。
5. **修復＋回歸測試**：修復前先在正確的 seam 寫回歸測試（看它紅→修→看它綠）；
   沒有正確 seam 就記錄這個事實，不硬寫假信心測試。
6. **清理**：grep 前綴清光插樁；重跑第 1 步的原始迴圈確認不再重現；
   把證實為真的假設寫進 commit 訊息。

## S5 驗收（兩軸審查＋重構）

1. **全量測試實跑**：跑整個測試套件（輸出大就派 agent 跑、落檔、回結論）。
2. **兩軸審查**：照 `templates/feature-review.md` 派 fresh-context sonnet——
   **Spec 軸**（對照 spec.md＋tasks.md：缺漏、scope creep、假完成）與
   **Standards 軸**（程式異味，判斷題）。兩軸**分開回報、不合併排名**：
   程式可能「規範全過但做錯了東西」，混在一起會互相遮蓋。
3. **重構在這裡做**（不在 S4 的 red→green 迴圈裡）：Standards 軸值得修的
   發現，照 `templates/refactor.md` 派工（行為驗證手段＝第 1 步的全量測試），
   修完重跑測試。
4. Spec 軸的缺漏 → 開新 slice 回 S4 補。

**完成判準**：`20-judgment.md` §2 的完成 checklist 全部打勾。

## S6 收尾

- 過程中定案的新術語 → 更新根目錄 `CONTEXT.md`（沒有就建，只放詞彙定義，
  **絕不放實作細節**）。難逆轉＋日後會令人驚訝＋真 trade-off 的決策
  （三者全中才寫）→ `docs/adr/NNNN-slug.md`，1–3 句講脈絡與為什麼。
- 向使用者**誠實回報**：做了什麼／沒做什麼／假設了什麼，附最終測試輸出。
- 踩了坑 → 照 `30-maintenance.md` §3 寫教訓。
- 更新 `docs/STATUS.md` 與 `docs/DEVLOG.md`（格式規則見
  `.claude/skills/wrap/SKILL.md`，那是唯一權威來源）。

## Bug 修復模式（`/pipeline fix <描述>`）

小 bug 不走全流程：S0 摸底 → 迷你 S1（一頁 spec：重現步驟＋期望行為＋
驗收條件，仍要使用者核准）→ S4 單片（直接進診斷支線）→ S5 只跑 Spec 軸＋
全量測試 → S6。

## 建制時的假設（日後可依實況修正）

- 假設主對話模型是 Opus（2026-07-05 使用者自述），但流程以 Sonnet 也能
  指揮為底線設計。
- 假設專案無 issue tracker，故 tasks.md 充當 issue 清單；若日後接上
  GitHub Issues，S3 的產物改發 issue、S5 的 Spec 來源改抓 issue，狀態機
  可參考萃取檔中 triage 一節。
- 假設 `docs/specs/` 是規格產物的家；若專案日後有自己的慣例，改放並更新本檔。
