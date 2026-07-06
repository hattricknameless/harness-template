# mattpocock/skills — 方法論擷取

來源：https://github.com/mattpocock/skills（分支 main）
抓取方式：`raw.githubusercontent.com` 直接下載（非透過 WebFetch 摘要，避免內容失真）。
抓取日期：2026-07-05

**路徑更正**：使用者原給的路徑（`engineering/xxx/SKILL.md`）少了倉庫的頂層目錄
`skills/`。實際路徑一律是 `skills/engineering/xxx/SKILL.md` 或
`skills/productivity/xxx/SKILL.md`。用 `raw.githubusercontent.com/mattpocock/skills/main/engineering/...`
直接抓會 404；改成 `raw.githubusercontent.com/mattpocock/skills/main/skills/engineering/...` 後全部成功。

---

## 抓取清單

### 成功（14 個主檔 + 11 個補充檔，共 25 個）

| Skill | SKILL.md | 補充檔 |
|---|---|---|
| engineering/grill-with-docs | ✅ | 無 |
| engineering/to-prd | ✅ | 無 |
| engineering/to-issues | ✅ | 無 |
| engineering/tdd | ✅ | mocking.md ✅、tests.md ✅ |
| engineering/code-review | ✅ | 無 |
| engineering/diagnosing-bugs | ✅ | scripts/hitl-loop.template.sh ✅ |
| engineering/research | ✅ | 無（skill 本文極短，只有這些） |
| engineering/domain-modeling | ✅ | ADR-FORMAT.md ✅、CONTEXT-FORMAT.md ✅ |
| engineering/codebase-design | ✅ | DEEPENING.md ✅、DESIGN-IT-TWICE.md ✅ |
| engineering/prototype | ✅ | LOGIC.md ✅、UI.md ✅ |
| engineering/triage | ✅ | AGENT-BRIEF.md ✅、OUT-OF-SCOPE.md ✅ |
| productivity/grilling | ✅ | 無 |
| productivity/handoff | ✅ | 無 |
| productivity/writing-great-skills | ✅ | GLOSSARY.md ✅ |

### 抓不到

無。全部 25 個目標檔案（14 個 SKILL.md + 11 個 SKILL.md 內連結引用的補充檔）皆成功抓取。

---

## 各 Skill 詳解

### 1. engineering/grill-with-docs

- **目的**：一個「relentless interview」，邊逼問邊把決策沉澱成文件（ADR、glossary）。
- **觸發時機**：`disable-model-invocation: true`（user-invoked，只能手動打 `/grill-with-docs`）。
- **核心步驟**：只有一行 —— 執行 `/grilling`，同時套用 `/domain-modeling` skill。是一個「組合 skill」，本身無獨立邏輯，靠拼裝兩個現有 skill 達成「邊問邊記錄」。
- **關鍵規則**：無獨立規則，繼承 grilling + domain-modeling 的規則。
- **輸出產物**：由 domain-modeling 產生的 `CONTEXT.md` / ADR 檔案（見該節）。
- **銜接關係**：grilling（逼問機制）+ domain-modeling（記錄機制）的組合。這種「一行 skill = 呼叫兩個既有 skill」的寫法，是一種可複用的 pipeline 組裝模式。

### 2. productivity/grilling

- **目的**：對一個計畫/設計做逼問式訪談，直到雙方達成「shared understanding」。
- **觸發時機**：使用者想在動工前壓力測試一個計畫，或說出任何「grill」觸發語。model-invoked（有 description）。
- **核心步驟**：
  1. 逐一走過設計樹的每個分支，一次解決一個決策點的依賴關係。
  2. 每個問題都要附上「建議答案」（不是純粹拋問題）。
  3. **一次只問一個問題**，等使用者回覆才問下一個 —— 一次問多個會讓人「bewildering」（無所適從）。
  4. 若問題可以靠探索程式碼回答，就去探索程式碼，不要問使用者。
  5. 在使用者確認「達成共識」之前，不要開始實作計畫。
- **關鍵規則/禁令**：禁止一次問多題；禁止在未確認前動手實作。
- **輸出產物**：無獨立產物（本身是互動過程），共識本身會被 domain-modeling 等其他 skill 落成文件。
- **銜接關係**：被 grill-with-docs、triage（grill 步驟）呼叫；常與 domain-modeling 搭配使用（逼問的同時更新 glossary/ADR）。

### 3. engineering/to-prd

- **目的**：把目前對話內容「合成」為一份 PRD 並發布到 issue tracker——**不訪談**，純粹整理已知資訊。
- **觸發時機**：user-invoked（`disable-model-invocation: true`），需要手動呼叫。前提：issue tracker 與 triage 標籤詞彙應已由 `/setup-matt-pocock-skills` 提供。
- **核心步驟**：
  1. 探索 repo 理解現況，PRD 內文使用專案的 domain glossary 詞彙，並尊重相關 ADR。
  2. 先勾勒要測試的「seam」（測試邊界）——優先用既有 seam、用最高層次的 seam，新 seam 也要盡量放在最高點；**seam 數量越少越好，理想是 1 個**。跟使用者確認這些 seam 是否符合預期。
  3. 用下方模板寫 PRD，發布到 issue tracker，套用 `ready-for-agent` 標籤（不需額外 triage）。
- **關鍵規則/禁令**：
  - Implementation Decisions 段落**禁止**寫具體檔案路徑或程式碼片段（會很快過時）；例外：若 prototype 產出的片段比文字更精準地編碼了一個決策（狀態機、reducer、schema、type shape），可以內嵌並註明來自 prototype。
- **輸出產物（PRD 模板）**：
  - `## Problem Statement`（使用者視角的問題）
  - `## Solution`（使用者視角的解法）
  - `## User Stories`（大量、編號、格式固定為「1. As an <actor>, I want a <feature>, so that <benefit>」，要求極度詳盡覆蓋所有面向）
  - `## Implementation Decisions`（模組、介面、技術澄清、架構決策、schema 變更、API 合約、具體互動——不含檔案路徑/程式碼）
  - `## Testing Decisions`（什麼是好測試、哪些模組會被測、程式庫中類似測試的先例）
  - `## Out of Scope`
  - `## Further Notes`
- **銜接關係**：**PRD 是 to-issues 的輸入**——PRD 寫完後，交給 to-issues 拆解成 vertical slice issues。

### 4. engineering/to-issues

- **目的**：把一份 plan/spec/PRD 拆解成「獨立可認領」的 issue，用 vertical slice（tracer bullet）方法論。
- **觸發時機**：user-invoked。
- **核心步驟**：
  1. **蒐集脈絡**：用對話中已有的內容；若使用者傳入 issue 參照（編號/URL/路徑），去抓它的完整 body + comments。
  2. **探索程式碼**（可選）：理解現況，issue 標題/描述要用專案 domain glossary，尊重 ADR；尋找「prefactor」機會——**"Make the change easy, then make the easy change."**
  3. **草擬 vertical slice**：每個 issue 是一個「tracer bullet」——**貫穿所有整合層的薄縱切片**，不是切一層的橫切片。規則：每片要交付一條窄但完整的路徑（schema、API、UI、測試都要有）；完成的切片本身可被獨立展示/驗證；任何 prefactor 要先做。
  4. **向使用者確認**：以編號列表呈現拆分方案，每片附 Title / Blocked by / User stories covered，詢問「粒度對嗎？依賴關係對嗎？要合併或再拆嗎？」，反覆直到使用者核准。
  5. **依賴序發布**：依賴序（blocker 優先）發布到 issue tracker，套用 `ready-for-agent`（除非另有指示），用模板；**禁止關閉或修改 parent issue**。
- **關鍵規則/禁令**：Implementation Decisions 段落同樣禁止具體檔案路徑/程式碼片段（同 to-prd 的例外規則）；禁止修改 parent issue。
- **輸出產物（issue 模板）**：`## Parent`（如有）、`## What to build`（端到端行為描述，非逐層實作）、`## Acceptance criteria`（checklist）、`## Blocked by`。
- **銜接關係**：**輸入**可以是 to-prd 產出的 PRD，也可以是任意 plan/spec；**輸出**的每個 issue 會進入 triage 的狀態機（`ready-for-agent`），最終由 tdd + codebase-design 去實作，由 code-review 去驗收。

### 5. engineering/tdd

- **目的**：讓 red→green 迴圈**產出值得留下的測試**——本身是「reference」型 skill（規則集，不是流程步驟），每個 cycle 前後都要對照。
- **觸發時機**：使用者想 test-first 開發/修 bug，提到 "red-green-refactor"，或想要 integration tests。model-invoked。
- **核心內容（非步驟式，是規則集）**：
  - **好測試的定義**：透過公開介面驗證行為，不管實作細節；程式碼可以全部重寫、測試不必變；好測試讀起來像規格書。
  - **Seam（測試邊界）**：測試永遠打在 seam 上，不打內部。**"Test only at pre-agreed seams."** 寫任何測試前，先寫下要測的 seam 並跟使用者確認；沒有預先核准的 seam 不准寫測試——測試精力才會落在關鍵路徑而非窮舉邊界情況。
  - **反模式三種**：
    1. **Implementation-coupled**（實作耦合）：mock 內部協作者、測 private method、繞道側寫（直接查資料庫而非走介面）。判斷法：重構後行為沒變但測試掛了。
    2. **Tautological**（套套邏輯）：期望值用跟程式碼一樣的算法算出來（如 `expect(add(a,b)).toBe(a+b)`），永遠不會不一致。期望值必須來自獨立來源（已知字面量、手算範例、規格）。
    3. **Horizontal slicing**（橫切）：先寫完所有測試才寫實作。會測到「想像中」的行為，測試對真正的變更變得不敏感。應改用 **vertical slices**：一個測試→一個實作→重複，每個測試是回應上一輪學到的東西的 **tracer bullet**。
  - **迴圈規則**：Red before green（先寫會失敗的測試，再寫剛好夠通過的程式碼，不要預判未來測試或加投機功能）；一次一片（one seam, one test, one minimal impl）；**重構不屬於這個迴圈**——重構屬於 review 階段（code-review skill），不屬於 red→green。
- **補充檔**：
  - `tests.md`：好/壞測試的具體程式碼範例，包含「透過介面驗證 vs. 繞道查資料庫驗證」對照，以及套套邏輯測試的具體反例。
  - `mocking.md`：只在「系統邊界」mock（外部 API、資料庫、時間/亂數、檔案系統有時）；不要 mock 自己的 class/module；可 mock 性設計技巧——dependency injection、SDK 風格介面優於通用 fetcher（每個端點各自可獨立 mock）。
- **銜接關係**：與 codebase-design 的「seam」概念完全共用；規則明確聲明「重構是 code-review 的事」——形成 tdd（實作） → code-review（重構+驗收）的分工。

### 6. engineering/code-review

- **目的**：對「自某個固定點以來的變更（diff）」做**兩軸審查**：Standards（是否符合本repo 的程式規範）與 Spec（是否符合原始 issue/PRD 的要求），平行子代理各自審查、分開回報。
- **觸發時機**：使用者想 review 一個 branch/PR/WIP 變更，或說「review since X」。model-invoked。
- **核心步驟**：
  1. **釘住固定點**：使用者給的 commit SHA / branch / tag 等；沒給就問。指令固定為 `git diff <fixed-point>...HEAD`（三點，對比 merge-base）及 `git log <fixed-point>..HEAD --oneline`。**先確認 fixed point 能 resolve、diff 非空**——這步該在外層失敗，不要留到兩個平行子代理裡才炸。
  2. **找 Spec 來源**（依序）：commit message 中的 issue 參照（`#123`、`Closes #45`）→ 使用者傳入路徑 → `docs/`、`specs/`、`.scratch/` 下符合 branch/feature 名的檔案 → 都沒有就問使用者，若使用者說沒有，Spec 子代理就跳過並回報「no spec available」。
  3. **找 Standards 來源**：repo 內文件化的規範（`CODING_STANDARDS.md`、`CONTRIBUTING.md`）。此外永遠疊加一份「**smell baseline**」——固定的 Fowler code smell 清單（即使 repo 什麼都沒文件化也適用）。兩條規則：**repo 規範優先**（repo 認可的東西即使違反 baseline 也不算）；**baseline 永遠只是判斷題**（不是硬性違規），且跳過任何 tooling 已強制的項目。
     - Smell 清單（12 種，每種「是什麼→怎麼修」）：Mysterious Name、Duplicated Code、Feature Envy、Data Clumps、Primitive Obsession、Repeated Switches、Shotgun Surgery、Divergent Change、Speculative Generality、Message Chains、Middle Man、Refused Bequest。
  4. **平行派兩個子代理**（單一訊息內兩個 `Agent` tool call，都用 `general-purpose`）：
     - **Standards 子代理**：給 diff 指令+commit 列表、Standards 來源檔+完整 smell baseline（子代理沒有其他管道拿到這份清單，必須整份貼給它）；brief 要求「逐檔/逐 hunk 回報：(a) 違反文件化規範之處，引用規範來源；(b) 任何 baseline smell，命名+引用 hunk；區分『硬性違規』vs.『判斷題』——文件化規範違反可以是硬性的，baseline smell 永遠是判斷題，且文件化規範優先於 baseline；跳過 tooling 已強制的；400 字以內」。
     - **Spec 子代理**：給 diff 指令+commit 列表、spec 路徑或全文；brief 要求「回報：(a) spec 要求但缺失/部分完成的需求；(b) diff 中沒被要求的行為（scope creep）；(c) 看似做了但實作有問題的需求；每項引用 spec 原文；400 字以內」。若無 spec 就跳過此子代理。
  5. **彙整**：兩份報告分別放在 `## Standards` 與 `## Spec` 標題下，逐字或輕微整理呈現，**禁止合併或跨軸重新排名**。結尾一行摘要：各軸總發現數+各軸內最嚴重的問題（不跨軸挑單一贏家）。
- **關鍵規則/禁令**：兩軸絕不合併排名——因為程式碼可能「通過 Standards 但 Spec 錯」或反之，分開回報避免一軸遮蓋另一軸。
- **輸出產物**：`## Standards` + `## Spec` 兩節報告 + 一行總結。
- **銜接關係**：Spec 來源優先抓 to-issues/to-prd 產出的 issue/PRD；審查對象是 tdd 產出的 diff；「重構」被 tdd skill 明確指定屬於這裡的範疇（而非 red-green 迴圈本身）。

### 7. engineering/diagnosing-bugs

- **目的**：困難 bug/效能退化的**診斷紀律**（六階段）。
- **觸發時機**：使用者說 "diagnose"/"debug this"，或回報東西壞掉/丟例外/失敗/變慢。model-invoked。
- **核心步驟（六階段，只在有明確理由時才跳過某階段）**：
  1. **建立回饋迴圈（這是本 skill 的核心）**：如果有一個對「這個 bug」會變紅的緊（tight）pass/fail 訊號，之後的二分搜尋/假設驗證/插樁都只是消耗這個訊號而已；沒有訊號，再怎麼盯程式碼也沒用。**在這裡花不成比例的力氣。要有攻擊性、有創意、絕不放棄。**
     - 建構順序（大致）：failing test → curl/HTTP script → CLI 呼叫比對 snapshot → headless browser script → 重放捕獲的 trace → 拋棄式 harness → property/fuzz loop → bisection harness（`git bisect run`）→ differential loop（新舊版本同輸入對比）→ **HITL bash script**（最後手段：人類需要點擊時，用 `scripts/hitl-loop.template.sh` 讓人類的操作也保持結構化——腳本印出指示、等待 Enter、或問問題把回答讀進變數，最後印出 `KEY=VALUE` 給 agent 解析）。
     - **收緊迴圈**：更快（快取設置、跳過無關 init、縮小測試範圍）、訊號更銳利（斷言具體症狀而非「沒當機」）、更確定性（釘住時間、seed 亂數、隔離檔案系統、凍結網路）。30 秒的不穩定迴圈幾乎等於沒有；2 秒的確定性迴圈才是「緊」的。
     - **非確定性 bug**：目標不是乾淨重現，而是**更高的重現率**——迴圈觸發 100 次、平行化、加壓力、縮小時序窗、注入 sleep。50% 機率可 debug，1% 不行。
     - **真的做不出迴圈時**：明確停下來說明，列出試過的方法，向使用者要求：(a) 能重現的環境權限、(b) 捕獲的產物（HAR、log dump、core dump、有時間戳的螢幕錄影）、(c) 加臨時 production 插樁的許可。**不准在沒有迴圈前就開始假設。**
     - **完成判準**：迴圈必須「緊且能變紅」——能講出一個「已經至少跑過一次」的指令（貼出呼叫與輸出），且滿足：Red-capable（能命中該 bug 的確切症狀，不只是「沒出錯」）、Deterministic、Fast（秒級非分鐘級）、Agent-runnable（可無人值守執行，人類只透過 HITL script 介入）。**若發現自己在讀程式碼建立理論卻還沒有這個指令——停下，這正是本 skill 要防止的失誤。**
  2. **重現+最小化**：跑迴圈確認變紅，且是**使用者描述的那個症狀**（不是碰巧鄰近的另一個失敗——抓錯 bug=修錯東西）；跨多次執行可重現（或非確定性 bug 達到高重現率）；捕獲確切症狀供後續驗證。**最小化**：一次剪掉一個輸入/呼叫者/設定/資料/步驟，每剪一次重跑迴圈，只留下「拿掉任一個就會變綠」的最小必要元素。
  3. **假設**：測試任何假設前，先生成 **3–5 個排序過的假設**（單一假設容易錨定在第一個看似合理的想法上）。每個假設必須**可證偽**：格式「若 X 是原因，則改變 Y 會讓 bug 消失／改變 Z 會讓它更糟」。講不出預測的假設就是憑感覺，丟掉或磨利它。**排序清單先給使用者看**（他們常有能瞬間重排的領域知識，或已經排除過的假設）——不要卡在這裡，使用者不在就照自己的排序繼續。
  4. **插樁**：每個探針對應 Phase 3 的一個具體預測，**一次只改一個變數**。工具優先序：debugger/REPL（一個中斷點勝過十個 log）> 打在能區分假設的邊界上的**針對性 log** > **絕不「全部 log 然後 grep」**。每條 debug log 都要打上唯一前綴（如 `[DEBUG-a4f2]`），清理時一次 grep 搞定。**效能分支**：效能退化的 log 通常沒用，改成先建立 baseline 量測（timing harness、`performance.now()`、profiler、query plan）再二分搜尋——先量測，後修。
  5. **修復+回歸測試**：**在修復前先寫回歸測試**——但只有在存在「正確的 seam」時才寫。正確的 seam 是能重現「真實 bug 模式」的位置（若唯一可用的 seam 太淺——單一呼叫者測試但 bug 需要多個呼叫者才會觸發——寫在那裡的回歸測試只是假信心）。**若不存在正確 seam，這件事本身就是一個發現**——記下來，這代表架構本身在阻止把 bug 鎖死，留給下一步。若存在正確 seam：把最小化的重現寫成失敗測試→看它失敗→套用修復→看它通過→用原始（未最小化）的情境重跑 Phase 1 的迴圈。
  6. **清理+事後回顧**：宣告完成前必須：原始重現不再重現（重跑 Phase 1 迴圈）；回歸測試通過（或已記錄沒有 seam 這件事）；所有 `[DEBUG-...]` 插樁都清除（grep 前綴確認）；拋棄式原型刪除或移到明確標記的除錯位置；**在 commit/PR 訊息中寫下最終證實正確的假設**，讓下一個除錯者受益。**然後問：什麼可以預防這個 bug？** 若答案涉及架構改變（沒有好的測試 seam、糾纏的呼叫者、隱藏耦合），交給 `/improve-codebase-architecture` skill 並附上具體細節——**修復完成後才提這個建議，不要在修復前提**（這時候資訊比開始時更多）。
- **補充檔**：`scripts/hitl-loop.template.sh` —— 一個 bash 模板，提供 `step "<指示>"`（顯示指示、等 Enter）與 `capture VAR "<問題>"`（顯示問題、把回覆讀進變數）兩個 helper，最後印出 `KEY=VALUE` 給 agent 解析，讓「人類必須手動操作」的步驟也保持腳本化、可重複執行。
- **關鍵規則/禁令**：沒有 red-capable 的迴圈前絕不進入下一階段；不准「log everything and grep」；效能 bug 不准只靠 log；修復前若無正確 seam 不准硬寫假回歸測試；架構建議只能在修復後提出。
- **輸出產物**：無固定文件模板，但要求 commit/PR 訊息記錄「驗證為真的假設」；可能交棒給 improve-codebase-architecture skill（一份具體問題清單）。
- **銜接關係**：Phase 5 明確指向 tdd 的「正確 seam」概念；Phase 6 的架構建議出口指向 improve-codebase-architecture（此 skill 使用者未列在本次抓取清單中，屬 repo 內另一 skill）。

### 8. engineering/research

- **目的**：針對一個問題，對照「高信任的一手來源」進行調查，並把結果寫成 repo 裡的一份 Markdown 檔。
- **觸發時機**：使用者想研究某個主題、蒐集文件/API 事實、或想把讀資料的苦工委派給背景 agent。model-invoked。
- **核心步驟**（全文極短，只有三點）：
  1. 派一個**背景 agent**去做研究，這樣使用者可以繼續工作、agent 邊讀邊做。
  2. Agent 的工作：對照**一手來源**（官方文件、原始碼、規格、第一方 API——不是二手轉述）調查問題，每個論述都要追溯回其來源。
  3. 把發現寫進單一 Markdown 檔，每個論點都要引用來源；存放位置比照 repo 既有慣例，若無慣例則放在合理位置並說明放在哪裡。
- **關鍵規則/禁令**：只信一手來源，不採用二手轉述；每個 claim 都要能追溯到來源。
- **輸出產物**：單一 Markdown 檔，含來源引用。
- **銜接關係**：本身是一個「背景派工」模式的範例——本次任務其實正在示範這個 skill 描述的行為（派 agent 讀一手來源、寫成帶引用的 md 檔）。

### 9. engineering/domain-modeling

- **目的**：**主動**建立並磨利專案的 domain model——挑戰用詞、發明邊界情境、當場把 glossary 和決策寫下來。（只是「讀」`CONTEXT.md` 不算這個 skill，那是任何 skill 都該有的一行習慣；這個 skill 是在**改動**模型時用的。）
- **觸發時機**：使用者想釘死 domain 術語/ubiquitous language、記錄架構決策，或其他 skill 需要維護 domain model 時。model-invoked。
- **檔案結構**：
  - 單一 context（多數 repo）：根目錄 `CONTEXT.md` + `docs/adr/0001-slug.md...`。
  - 多重 context：根目錄有 `CONTEXT-MAP.md` 列出各 context 位置與彼此關係；各 context 自己的 `CONTEXT.md` 與 `docs/adr/` 放在對應子目錄。
  - **懶惰建檔**：只在真的有東西要寫時才建立檔案（第一個術語定案才建 `CONTEXT.md`；第一個 ADR 需要才建 `docs/adr/`）。
- **核心行為（session 中持續進行，非一次性步驟）**：
  - **對照 glossary 挑戰用詞**：使用者用詞跟 `CONTEXT.md` 既有定義衝突時，立刻指出。
  - **磨利模糊語言**：使用者用模糊/多義詞時，提出精確的標準用詞。
  - **用具體情境討論**：討論 domain 關係時，用具體情境壓力測試，發明邊界案例逼使用者講精確。
  - **與程式碼交叉驗證**：使用者說明運作方式時，檢查程式碼是否一致，發現矛盾要點出來。
  - **即時更新 `CONTEXT.md`**：術語一旦定案立刻更新，不要累積起來一次寫。`CONTEXT.md` 必須完全不含實作細節——它是 glossary，不是 spec、不是 scratch pad、不是實作決策倉庫。
  - **謹慎提議 ADR**：只有同時滿足三條才提議寫 ADR：(1) 難以逆轉、(2) 沒有脈絡會讓人驚訝「為什麼這樣做」、(3) 是真正 trade-off 的結果（有真正的替代方案且為特定理由選了一個）。三者缺一就跳過。
- **補充檔**：
  - `CONTEXT-FORMAT.md`：`CONTEXT.md` 的格式規範——每個詞條含一兩句定義+`_Avoid_:` 列出要避免的同義詞；規則：要有主見（多詞同義時選一個最好的，其餘放 Avoid）、定義要精簡（一兩句，定義「是什麼」而非「做什麼」）、只收本 context 特有的詞（通用程式概念如 timeout、error type 不算，即使專案大量使用）、自然分群時用子標題分組。多 context 時用 `CONTEXT-MAP.md` 列出各 context 位置+彼此關係（用領域事件描述關係，如 "Ordering emits OrderPlaced events; Fulfillment consumes them"）。
  - `ADR-FORMAT.md`：ADR 放 `docs/adr/`，序號編碼 `0001-slug.md`。模板極簡：一個標題+1-3句「脈絡是什麼、決定了什麼、為什麼」。**可選段落**（多數 ADR 不需要）：Status frontmatter、Considered Options、Consequences——只在真的有價值時才加。何時該寫 ADR 的判準同 SKILL.md 的三條件；並列出「什麼夠格」的具體類別：架構形狀、context 間整合模式、有鎖定成本的技術選型、邊界/範圍決策（含明確的「不」）、刻意偏離常規路徑的做法、程式碼看不出來的限制條件、非顯而易見的被拒絕替代方案。
- **關鍵規則/禁令**：`CONTEXT.md` 絕不能含實作細節；ADR 三條件缺一不寫。
- **輸出產物**：`CONTEXT.md`（+可能的 `CONTEXT-MAP.md`）、`docs/adr/NNNN-slug.md`。
- **銜接關係**：被 grill-with-docs、triage（grill 步驟）呼叫；tdd、diagnosing-bugs、to-prd、to-issues 都在「探索程式碼」步驟提到「讀 CONTEXT.md 取得詞彙、尊重 ADR」——**CONTEXT.md/ADR 是貫穿所有其他 engineering skill 的共享語境輸入**。

### 10. engineering/codebase-design

- **目的**：設計「**深模組**」（deep module）的共享詞彙——小介面背後藏大量行為，放在乾淨的 seam 上，可透過該介面測試。目標是給呼叫者 leverage、給維護者 locality、給所有人 testability。
- **觸發時機**：使用者想設計/改善模組介面、找「加深」機會、決定 seam 放哪裡、讓程式碼更可測試/更適合 AI 導航，或其他 skill 需要這套深模組詞彙。model-invoked。
- **核心詞彙**（必須精確使用，不可替換）：
  - **Module**：任何有介面+實作的東西（函式、類別、套件，或跨 tier 的切片）。避免用 "unit/component/service"。
  - **Interface**：呼叫者要正確使用模組必須知道的一切——型別簽章+不變量+順序限制+錯誤模式+必要設定+效能特性。避免用 "API/signature"（太窄，只指型別層面）。
  - **Implementation**：模組內部的程式碼本體，與 **Adapter** 不同（一個東西可以是小 adapter+大 implementation，如 Postgres repo；也可以是大 adapter+小 implementation，如記憶體內假物件）。
  - **Depth**：介面上的槓桿——呼叫者/測試每學一單位介面能操作多少行為。**深**=小介面藏大量行為；**淺**=介面幾乎跟實作一樣複雜（要避免）。
  - **Seam**（Michael Feathers）：能在不修改該處程式碼的情況下改變行為的**位置**——模組介面所在之處。Seam 放哪裡本身是獨立的設計決策。避免用 "boundary"（跟 DDD 的 bounded context 語意衝突）。
  - **Adapter**：在 seam 上滿足介面的具體實作物。描述的是「角色」，不是「內容物」。
  - **Leverage**：深帶給呼叫者的好處——一份實作回本在 N 個呼叫點+M 個測試上。
  - **Locality**：深帶給維護者的好處——變更、bug、知識、驗證都集中在一處，不是散落各呼叫者。
- **核心原則**：
  - Depth 是**介面**的屬性，不是實作的屬性——深模組內部可以由小的、可 mock、可替換的零件組成，只是這些零件不屬於介面（模組可以有「內部 seam」給自己測試用，也有「外部 seam」給呼叫者用）。
  - **刪除測試法**：想像刪掉這個模組，如果複雜度就此消失，它只是個 pass-through；如果複雜度在 N 個呼叫者裡重新冒出來，代表它真的有價值。
  - **介面就是測試面**：呼叫者和測試走同一個 seam；如果想測「介面之後」，代表模組的形狀可能不對。
  - **一個 adapter 是假設性 seam，兩個 adapter 才是真實 seam**——沒有東西真的隨 seam 變化，就不要引入 seam。
- **可測試性設計三招**：(1) 接受依賴而非自己創建（dependency injection）；(2) 回傳結果而非產生副作用；(3) 小介面（方法少=測試少，參數少=setup簡單）。
- **補充檔**：
  - `DEEPENING.md`：如何安全地「加深」一群淺模組。依賴分四類決定跨 seam 怎麼測：(1) In-process（純計算，永遠可加深，直接合併測介面）；(2) Local-substitutable（有本地測試替身，如 PGLite，seam 是內部的）；(3) Remote but owned（自己的跨網路服務，走 Ports & Adapters——定義 port，production 用真 adapter、測試用記憶體內 adapter）；(4) True external（第三方服務如 Stripe，注入 port，測試用 mock adapter）。Seam 紀律同 SKILL.md 的「兩個 adapter 才是真 seam」；測試策略是**替換不疊加**——舊的單元測試在深化後的介面測試存在後就該刪除，不要兩層並存。
  - `DESIGN-IT-TWICE.md`：基於 Ousterhout 的「Design It Twice」——第一個想法通常不是最好的。流程：先寫一份使用者可讀的問題空間說明（含約束、依賴分類、示意程式碼），給使用者看的同時**立刻**平行派 3+ 個子代理，每個子代理拿到不同設計限制（如「介面最小化、1-3 個進入點」vs.「最大化彈性」vs.「為最常見呼叫者優化」vs.「圍繞 ports & adapters 設計」），各自產出介面+使用範例+隱藏的實作內容+依賴策略+trade-off，最後依序呈現並用 depth/locality/seam placement 比較，**給出自己的推薦**（可能是混合方案）——"Be opinionated — the user wants a strong read, not a menu."
- **關鍵規則/禁令**：詞彙必須精確（不可用 component/service/API/boundary 替代）；深度是介面屬性不是實作屬性；沒有兩個 adapter 不要引入 seam。
- **輸出產物**：介面設計文件/比較報告（無固定檔案模板，通常是對話中呈現）。
- **銜接關係**：Seam 概念被 tdd、to-prd、to-issues 直接引用（"test only at pre-agreed seams" / "existing seams should be preferred"）；DEEPENING 的 seam 紀律與 tdd 的「反模式：implementation-coupled」互相呼應。

### 11. engineering/prototype

- **目的**：建立**拋棄式程式碼來回答一個問題**——問題決定原型的形狀。
- **觸發時機**：使用者想確認一個狀態模型/邏輯是否合理，或想探索 UI 該長什麼樣子。model-invoked。
- **核心步驟**：
  1. **選分支**：「這個邏輯/狀態模型感覺對嗎？」→ 走 LOGIC.md；「這應該長怎樣？」→ 走 UI.md。若問題真的模稜兩可且使用者不在，依「周邊程式碼」判斷（後端模組→邏輯；頁面/元件→UI）並在原型頂部寫明假設。
  2. **兩分支共同規則**：
     - 從第一天就明確標示為拋棄式，放在實際會用到它的地方附近，但命名要讓人一看就知道是原型。
     - **一個指令啟動**，用專案既有的 task runner。
     - **預設不持久化**，狀態活在記憶體；若問題明確涉及資料庫，用 scratch DB 或有「PROTOTYPE — wipe me」字樣的本地檔。
     - **跳過打磨**：不寫測試、不寫超出「能跑」以外的錯誤處理、不做抽象。
     - **每次動作後印出/渲染完整相關狀態**。
     - **完成後刪除或吸收進正式程式碼**，不要放著爛掉。
  3. **完成時**：只有「答案」值得留下——記在 commit message / ADR / issue / 原型旁的 `NOTES.md`，連同它回答的問題一起記錄。
- **補充檔**：
  - `LOGIC.md`：建一個小型互動終端機 app 讓使用者手動驅動狀態模型。適用時機：不確定狀態機是否處理某邊界情況、資料模型能否表達某case、想在寫 API 前先摸清楚它該長怎樣。流程：(1) 先寫下要回答的問題（原型 README 或檔頭註解一段話）；(2) 用主專案的語言；(3) 把邏輯關進一個可攜的純模組（pure reducer / state machine / 一組純函式 / 有清楚方法介面的 class——挑最適合問題的形狀，不是最好接 TUI 的形狀；保持純：無 I/O、無終端機程式碼），這樣問題回答完後這個模組能被搬進正式程式碼、TUI 殼可丟；(4) 建最小 TUI，每個 tick 清畫面重繪整個畫面（不要一直往下滾），畫面兩部分：目前狀態（粗體欄位名、暗色次要資訊）+ 鍵盤快捷鍵列表；(5) 一個指令跑起來；(6) 交給使用者驅動，他們說「等等這不該發生」的瞬間就是 idea 裡的 bug，這才是重點；(7) 捕捉答案。反模式：不加測試、不接真資料庫、不要「順便支援以後可能要的 X」、邏輯模組不能混進 console.log/終端機逃逸碼、不要把 TUI 殼直接上 production。
  - `UI.md`：在單一路由上產生**數個結構上徹底不同**的 UI 變體，用 `?variant=` URL 參數+浮動底部切換列切換。兩種子形狀，**強烈偏好子形狀 A**（依附在既有頁面上：同一路由、同樣的資料抓取/params/auth，只換渲染部分——因為 UI 原型「跟 app 其餘部分並排」才容易判斷，孤立的路由是真空，每個變體單獨看都會顯得OK）；子形狀 B（全新拋棄式路由）只在真的沒有既有頁面可依附時才用。流程：(1) 預設 3 個變體，超過 5 個就不叫「徹底不同」而是雜訊；(2) 產生結構上真正不同的變體（不同版面、不同資訊層級、不同主要功能點，不只是換顏色——三個微調的卡片格線不算 UI 原型）；(3) 用 `searchParams.get('variant')` 接線；(4) 建浮動切換列（左箭頭/變體標籤/右箭頭，點擊更新 URL 讓變體可分享、reload 穩定，方向鍵也能切換但輸入框聚焦時不要攔截，且要在 production build 隱藏）；(5) 交給使用者翻閱（最有價值的回饋通常是「我要 B 的 header 配 C 的 sidebar」）；(6) 選定後刪掉輸家與切換器，把贏家 fold 進正式程式碼（子形狀 B 則是把贏家升級成正式路由）。反模式：只換顏色/文案不算變體；變體間共用太多程式碼（共用 `<Header>` OK，共用 `<Layout>` 就失去意義）；接上真的 mutation；直接把原型程式碼上 production（要重寫，原型是在「不寫測試、最少錯誤處理」的前提下寫的）。
- **關鍵規則/禁令**：拋棄式；不持久化；不加測試；不要泛化；不要把 TUI/切換列上 production。
- **輸出產物**：原型程式碼（拋棄式）+ 一份記錄「答案」的文字（commit/ADR/issue/NOTES.md）。
- **銜接關係**：to-prd 與 to-issues 的「Implementation Decisions」段落都有例外規則：若 prototype 產出了編碼決策的程式碼片段（狀態機/reducer/schema/type shape），**可以內嵌到 PRD/issue 裡並註明來自 prototype**——prototype 是這兩者的可選前置輸入。

### 12. engineering/triage

- **目的**：把 issue tracker 上的 issue（含外部 PR）推過一個小型 triage 狀態機。
- **觸發時機**：user-invoked（`disable-model-invocation: true`）。維護者打 `/triage` 並用自然語言描述想要什麼。
- **核心規則**：
  - 若這個 repo 把外部 PR 當作請求入口，triage 也涵蓋 PR：**「PR 就是附帶程式碼的 issue」**——同樣的角色、狀態、狀態機，只有少數標記「for a PR」的差異。
  - **每一則在 issue tracker 上發布的留言/issue 都必須以這行免責聲明開頭**：`> *This was generated by AI during triage.*`
  - **角色**：兩個 category role（`bug` / `enhancement`）+ 五個 state role（`needs-triage` / `needs-info` / `ready-for-agent` / `ready-for-human` / `wontfix`）。每個 issue 必須恰好一個 category + 一個 state；state 衝突時要先跟維護者確認再動作。狀態轉移：未標籤→`needs-triage`→（`needs-info`/`ready-for-agent`/`ready-for-human`/`wontfix`）；`needs-info` 在回報者回覆後轉回 `needs-triage`；維護者可隨時覆蓋，但異常轉移要先確認。
- **核心步驟（triage 單一 issue/PR）**：
  1. **蒐集脈絡**：讀完整 issue/PR（body、comments、labels、作者、日期；PR 還要看 diff），解析先前的 triage 記錄避免重問已解決的問題。探索程式碼（用 domain glossary、尊重 ADR）。做兩項檢查：(a) **重複性檢查**——依 domain concept（不只是字面用詞）搜尋是否已有實作，回報搜尋範圍，若找到即為「已實作」的 `wontfix`；(b) **先前拒絕檢查**——讀 `.out-of-scope/*.md`，找出相似的先前拒絕紀錄。
  2. **給建議**：告知維護者 category/state 建議+理由，附上與此請求相關的程式碼摘要（含是否已實作），等待方向。
  3. **驗證claim**：任何 grill 之前先確認 claim 站得住腳——bug 就照回報步驟重現；PR 就 checkout 出來、跑相關測試/指令確認 diff 真的做了它宣稱的事。回報結果：確認（附程式碼路徑）/失敗/資訊不足（強烈的 `needs-info` 訊號）。確認過的驗證能撐出更強的 agent brief。
  4. **必要時 grill**：需要補充細節時，同時執行 `/grilling` 與 `/domain-modeling`——一次問一題把它逼問成形，邊逼問邊即時更新 `CONTEXT.md`/ADR。
  5. **套用結果**：
     - `ready-for-agent` → 貼 agent brief 留言（見 AGENT-BRIEF.md）。
     - `ready-for-human` → 結構同 agent brief，但要註明為何不能委派給 agent（判斷題、需要外部存取、設計決策、手動測試）。
     - `needs-info` → 貼 triage notes 模板。
     - `wontfix` → 依原因決定留言內容：**已實作**（指出實作位置，**不**寫入 `.out-of-scope/`——那是給「被拒絕」的請求用的，不是給「已建成」的）；**拒絕（bug）**→禮貌說明後關閉；**拒絕（enhancement）**→寫入 `.out-of-scope/`、留言連結、關閉。
     - `needs-triage` → 套用角色，若有部分進度可留言。
  - **快速狀態覆蓋**：維護者直接說「把 #42 移到 ready-for-agent」時，信任並直接套用（確認要做的動作後執行），跳過 grill；若未經 grill 就轉 `ready-for-agent`，要問是否要寫 agent brief。
  - **恢復先前 session**：若已有先前 triage notes，先讀、檢查回報者是否已回答未決問題，再呈現更新後的情況，不要重問已解決的問題。
- **補充檔**：
  - `AGENT-BRIEF.md`：Agent brief 是貼在 issue/PR 上、狀態轉為 `ready-for-agent` 時的結構化留言，是 AFK agent 工作的**權威規格**（原始 body/討論只是脈絡，brief 才是合約）。三大原則：
    1. **耐久優於精確**——issue 可能在 `ready-for-agent` 躺好幾天/週，程式碼會變。要描述介面/型別/行為合約，**不要**引用檔案路徑或行號，不要假設目前的實作結構會維持不變。
    2. **描述行為而非流程**——講系統該做什麼，不要講怎麼實作（好例子："The `SkillConfig` type should accept an optional `schedule` field..."；壞例子："Open src/types/skill.ts and add a schedule field on line 42"）。
    3. **完整的驗收標準**——每條要可獨立驗證，禁止空泛（好："Running `gh issue list --label needs-triage` returns issues..."；壞："Triage should work correctly"）。
    4. **明確的範圍界線**——列出「不在此範圍內」的東西，避免 agent 過度發揮或誤判鄰近功能。
    - 模板欄位：Category / Summary / Current behavior / Desired behavior / Key interfaces / Acceptance criteria（checklist）/ Out of scope。附有 bug、enhancement、PR 三種「好範例」與一個「壞範例」對照（壞範例的問題：無 category、描述含糊、引用會過期的檔案路徑行號、無驗收標準、無範圍界線、無現況/期望對照）。PR 的 brief 與 issue 的差異：「Current behavior」描述的是 diff 現狀，brief 要求 agent 把它做完/修好而非從零開始。
  - `OUT-OF-SCOPE.md`：`.out-of-scope/` 目錄儲存被拒絕的功能請求，作用二：制度記憶（為什麼被拒）+ 去重（新 issue 比對到已拒絕概念時可直接引用）。**一個概念一個檔**（不是一個 issue 一個檔），檔名用簡短 kebab-case。格式偏向「小型設計文件」而非資料庫紀錄，含 `# 概念名`、`## Why this is out of scope`（引用專案範圍/哲學、技術限制、策略決策，理由要「持久」，不要引用「我們現在太忙」這種暫時性理由——那是延後不是拒絕）、`## Prior requests`（issue 連結列表）。何時檢查：triage 步驟1 蒐集脈絡時讀取全部 `.out-of-scope/*.md`，用「概念相似度」而非關鍵字比對（"night theme" 要能比對到 `dark-mode.md`）；找到相符時要呈現給維護者三選一：確認（併入 prior requests 並關閉）/ 重新考慮（刪除或更新 out-of-scope 檔，issue 走正常流程）/ 不同意（相關但不同，走正常流程）。何時寫入：**只有 enhancement（非 bug）被拒絕為 `wontfix` 時**才寫；PR 同理。**已實作**的 `wontfix` 絕不寫入這裡（會污染去重檢查，變成假拒絕紀錄），改為在關閉留言指出實作位置。維護者改變心意時：刪除該 `.out-of-scope/` 檔，不需要重開舊 issue（它們是歷史紀錄），新的觸發 issue 走正常 triage。
- **輸出產物**：triage notes（`## Triage Notes` + established so far / 待回答問題）、agent brief 留言、`.out-of-scope/*.md`。
- **銜接關係**：與 grilling + domain-modeling 組合使用（grill 步驟）；`ready-for-agent` 狀態與 to-issues/to-prd 發布時套用的標籤相同——**triage 的狀態機是連接「需求蒐集」與「agent 實作」的閘門**；驗證步驟與 diagnosing-bugs 的「重現」概念呼應（PR 驗證即是跑測試確認 diff 真的做了它宣稱的事）。

### 13. productivity/handoff

- **目的**：把目前對話「濃縮」成一份 handoff 文件，讓另一個 agent（或未來的 session）可以接手。
- **觸發時機**：user-invoked（`disable-model-invocation: true`），可帶 argument 說明「下個 session 要focus 什麼」。
- **核心步驟**：
  1. 寫一份摘要當前對話的 handoff 文件，**存到使用者作業系統的暫存目錄**（不是專案工作區）。
  2. 文件中要包含一個「suggested skills」段落，建議接手的 agent 該呼叫哪些 skill。
  3. **不要重複**已經被其他產物（PRD、plan、ADR、issue、commit、diff）捕捉過的內容，改用路徑/URL 引用它們。
  4. **redact 敏感資訊**（API key、密碼、個資）。
  5. 若使用者有傳入參數，視為下個 session 的重點方向，據此調整文件內容。
- **關鍵規則/禁令**：禁止重複既有產物內容（只能引用）；必須 redact 敏感資訊；必須存到 OS temp 而非工作區。
- **輸出產物**：純 Markdown 的 handoff 文件（存於 OS temp 目錄）。
- **銜接關係**：是一個「session 間橋接」skill，獨立於需求→實作 pipeline 之外，屬於「跨 session 連續性」的輔助機制——本次任務本身也用到了類似概念（scratchpad 目錄）。

### 14. productivity/writing-great-skills

- **目的**：如何寫/編輯出「好」的 skill——讓 skill 可預測（predictable）的詞彙與原則。**本檔全篇是 reference（規則集），沒有步驟。**
- **觸發時機**：user-invoked（`disable-model-invocation: true`）。使用者想寫/改自己的 skill 時查閱。
- **核心原則**：
  - **根本目的**：skill 存在是為了從隨機系統中逼出**決定性**（Predictability）——agent 每次都走同一套「流程」，不是每次都產出同一個「結果」（一個腦力激盪 skill 應該可預測地發散）。以下每個槓桿都是為了服務這個根本目的。
  - **兩種呼叫方式**（Invocation）：
    - **Model-invoked**：保留 `description`，agent 可自主觸發、其他 skill 也可觸發它；但每回合都要付出 **context load**（description 常駐 context window）。機制：省略 `disable-model-invocation`，寫「model-facing」的 description（豐富的觸發語，如 "Use when the user wants…, mentions…"）。
    - **User-invoked**：把 description 從 agent 的觸及範圍剔除，只有人類打名字才能呼叫（其他 skill 也不能呼叫它）。零 context load，但付出 **cognitive load**（人類自己要記得它存在）。機制：設 `disable-model-invocation: true`，description 變成給人看的一行摘要。
    - **選擇原則**：只有當 agent 必須自主觸及、或其他 skill 必須觸及它時，才選 model-invoked；若永遠只靠手動呼叫，就做成 user-invoked、不付 context load。
    - User-invoked skill 多到記不住時，用一個 **router skill**（一個 user-invoked skill，專門列出其他 skill 及何時該用哪個）來解決 cognitive load 問題。
  - **寫 description 的技巧**：
    - description 要做兩件事——說明 skill 是什麼、列出該觸發它的**分支**。每個字都增加 context load，所以 description 要比本文修剪得更狠。
    - **把 skill 的 leading word 放最前面**——description 是它做「呼叫」工作的地方。
    - **每個分支只留一個觸發語**——同義詞重複命名同一分支是 duplication（例如「build features using TDD」和「asks for test-first development」是同一分支寫兩次），要合併，只留真正不同的分支。
    - **砍掉本文已經有的身份說明**，description 只留觸發語+「其他 skill 何時需要它」的 reach clause。
  - **資訊層級**（Information Hierarchy）：內容分兩型——**steps**（有序動作，主層級）與 **reference**（定義/規則/事實，按需查閱），一個 skill 可以全是 steps、全是 reference、或混合。三階梯子：
    1. **In-skill step**：SKILL.md 裡的有序動作；每個 step 結束於一個 **completion criterion**（判斷是否做完的條件）——要**可檢查**（能分辨完成/未完成嗎？）、必要時要**窮盡式**（"every modified model accounted for" 而非 "produce a change list"）——模糊的判準會招致 **premature completion**（過早收工）。
    2. **In-skill reference**：SKILL.md 裡按需查閱的定義/規則/事實，常是合理的「扁平同層級清單」（例如一次 review 的所有規則都在同一層）——這是好的安排，不是壞味道。
    3. **External reference**：推出 SKILL.md 之外的獨立檔案，靠 **context pointer** 觸及，只在 pointer 被觸發時才載入（涵蓋從 disclosed reference——如同資料夾的 GLOSSARY.md——到完全的 external reference——skill 系統之外、任何 skill 都能指向的檔案）。
    - 要求越苛刻的 completion criterion 會驅動越徹底的 **legwork**（agent 私下做的挖掘工作），不論 skill 有沒有 steps。
    - **Progressive disclosure**：把 reference 往階梯下方移（移出 SKILL.md、放進連結檔案），讓最上層保持易讀。用 **branching**（分支）當作最乾淨的判斷法——每條分支都需要的東西放本文內，只有部分分支會用到的放到 pointer 後面。
    - **Co-location**：把同一概念的定義/規則/注意事項放在同一標題底下，而非散落各處。
  - **何時該拆分 skill**（Granularity）：
    - **按呼叫方式拆**：當有一個獨特的 **leading word** 該獨立觸發它、或其他 skill 必須觸及它時，才拆出一個 model-invoked skill（因為新的常駐 description 要付出 context load，這筆帳要划算）。
    - **按順序拆**：當一串 steps 裡「後面還有的 steps」（**post-completion steps**）會誘使 agent 對眼前這步草草收工（**premature completion**）時，才拆分序列，把後面的步驟藏起來、鼓勵 agent 在當前任務做更多 legwork。
  - **修剪**（Pruning）：每個含義只留一個**唯一真實來源**（single source of truth）；檢查每一行是否還有 **relevance**（還跟 skill 的作用有關嗎）；然後**逐句**（不只是逐行）獵殺 **no-op**——對每句話單獨做 no-op 測試，失敗就整句刪掉而非修剪字詞，要有攻擊性，大多數不合格的散文該刪不該改寫。
  - **Leading word**（領頭詞）：一個模型預訓練裡已經存在的緊湊概念（如 _lesson_、_fog of war_、_tracer bullets_），agent 執行 skill 時用它來思考。全文重複出現（未必需要，強的領頭詞可能只需出現一次），累積出一個分散式定義、用最少 token 錨定一整塊行為，靠調用模型已有的先驗知識。在本文裡錨定「執行」（agent 每次看到這詞就用同一套行為），在 description 裡錨定「呼叫」（同一詞出現在 prompt/文件/程式碼裡，agent 就會把它跟這個 skill 連起來、更可靠地觸發）。獵殺重複三次講同一件事的段落（duplication）、description 花一整句話比手畫腳講一個概念的地方——這些都是該收斂成單一詞的候選。範例：「fast, deterministic, low-overhead」→ _tight_；「a loop you believe in」→ _red_。
  - **失效模式**（用來診斷使用者可能遇到的 skill 問題）：
    - **Premature completion**（過早收工）：對策依序是——先磨利 completion criterion（便宜、局部）；只有在判準本質上模糊**且**你真的觀察到搶進度時，才藏起後續步驟（拆分序列）。
    - **Duplication**：同一含義出現在不只一處，耗費維護成本和 token，並把該含義的重要性抬高過其真實地位。
    - **Sediment**（沉積）：因為「加東西感覺安全、刪東西感覺有風險」而沉積下來的舊內容層——沒有修剪紀律的 skill 的預設命運。
    - **Sprawl**（蔓延）：skill 就是太長，即使每行都活著且獨特。解方是階梯：把 reference 藏到 pointer 後面，按 branch 或 sequence 拆分。
    - **No-op**：一行模型預設就會做的指令，等於白付了 context load 卻沒改變行為。測試法：這行跟預設行為比，有沒有改變行為？弱的領頭詞（"be thorough" 而 agent 本來就有點 thorough）是 no-op，修法是換更強的詞（"relentless"），而非換技巧。
- **補充檔 `GLOSSARY.md`**：本 skill 的 disclosed reference，把上述每個 bold 詞彙做完整定義，並按四軸分組：Invocation（Model-Invoked / User-Invoked / Description / Context Pointer / Context Load / Cognitive Load / Router Skill / Granularity）、Information Hierarchy（含 Steps / Reference / External Reference / Progressive Disclosure / Co-location / Sprawl _failure mode_）、Steering（Branch / Leading Word / Completion Criterion / Legwork / Post-Completion Steps / Premature Completion _failure mode_）、Pruning（Single Source of Truth / Duplication _failure mode_ / Relevance / Sediment _failure mode_ / No-Op _failure mode_）。每個詞條附 `_Avoid_:` 列出容易誤用的近義詞（例如 Module 該避免 "component/service"，Predictability 該避免 "consistency/reliability/robustness"）。
- **關鍵規則/禁令**：description 每個字都要為 context load 負責，要狠修剪；每個含義只准一個唯一來源；no-op 測試要逐句做且要積極刪除。
- **輸出產物**：無固定產物模板——本身是「怎麼寫 skill」的方法論參考。
- **銜接關係**：這份文件的原則直接解釋了為何本次抓取的其他 13 個 skill 長這個樣子——例如 to-prd/to-issues/grilling/handoff/writing-great-skills 自己都設了 `disable-model-invocation: true`（user-invoked，因為它們是使用者主動觸發的明確動作，不需要 agent 自主判斷觸發）；tdd/code-review/diagnosing-bugs/domain-modeling/codebase-design/prototype/research 都是 model-invoked（description 含豐富觸發語，因為它們該在對話中被自動偵測觸發）；codebase-design 用大量「Avoid: xxx」正是 writing-great-skills 教的「詞彙要精確」原則的實踐；diagnosing-bugs 大量使用 leading word（"tight"、"red-capable"）也正是本檔教的技巧。

---

## Skill 間銜接關係總覽（pipeline 視角）

按「需求→設計→實作→驗收」的順序整理各 skill 如何餵給下一個：

1. **grilling**（逼問澄清）＋ **domain-modeling**（同步記錄 glossary/ADR）→ 組合成 **grill-with-docs**。這一步的輸出是清楚的需求/決策共識 + 更新過的 `CONTEXT.md`/ADR。
2. 共識/對話內容 → **to-prd**：合成 PRD（不訪談，只整理），發布時標記 `ready-for-agent`。PRD 的 Implementation Decisions 段落若有 **prototype**（LOGIC.md/UI.md）產出的程式碼片段可以內嵌。
3. PRD（或任意 plan/spec）→ **to-issues**：拆解成 vertical slice（tracer bullet）issue，發布順序依依賴關係，同樣可套用 `ready-for-agent`，同樣可內嵌 prototype 片段。
4. 新 issue（未標記）或外部 PR → **triage**：狀態機（`needs-triage`→`needs-info`/`ready-for-agent`/`ready-for-human`/`wontfix`），驗證 claim、必要時呼叫 grilling+domain-modeling、產出 **AGENT-BRIEF**（給 AFK agent 的權威合約）、或寫入 **.out-of-scope/**（被拒絕的 enhancement 記錄，供未來去重）。
5. `ready-for-agent` 的 issue/brief → 實作階段：**tdd**（red→green，測試打在 codebase-design 定義的 **seam** 上）+ **codebase-design**（深模組設計詞彙，決定 seam 放哪、要不要 DEEPENING/DESIGN-IT-TWICE）。**diagnosing-bugs** 在此階段遇到難 bug 時介入（先建回饋迴圈、再重現最小化、假設、插樁、修復+回歸測試、清理+事後回顧），其架構性建議出口指向（未抓取的）`improve-codebase-architecture` skill。
6. 實作完成的 diff → **code-review**：兩軸（Standards + Spec）平行子代理審查，Spec 來源優先找 issue/PRD；**tdd 明確聲明「重構屬於這裡，不屬於 red-green 迴圈」**，等於把「重構」這個動作正式放在驗收/審查階段。
7. 任何階段的一手資料蒐集需求（讀官方文件/API/規格）→ **research**：背景 agent 對照一手來源、寫成帶引用的 md 檔。
8. session 結束需要交接 → **handoff**：濃縮對話成交接文件（存 OS temp），引用而非重複既有產物，並建議下個 session 該呼叫哪些 skill。
9. **writing-great-skills**（+GLOSSARY.md）不在這條 pipeline 上，而是**後設層**——教你怎麼寫/改上述每一個 skill 本身，包括該不該讓某個 skill model-invoked 還是 user-invoked、內容該放本文還是拆到補充檔、如何用 leading word 精簡表達。

## 給「怎麼寫自己 skill」用的關鍵原則摘要（writing-great-skills 精華）

1. **Predictability 是唯一目的**：判斷任何寫法好壞都回到「這樣寫會不會讓 agent 每次走同一套流程」。
2. **Invocation 二選一，且成本不同**：model-invoked 花 context load（常駐），user-invoked 花 cognitive load（人要記得）。只有 agent 必須自己判斷觸發時才選 model-invoked。
3. **Description 要對 branch 一一對應觸發語**，同義詞重複是 duplication，要合併。
4. **三層資訊階梯**：steps（有序動作，每步要有可檢查、必要時窮盡的 completion criterion）> in-skill reference（按需查閱）> external reference（拆檔用 context pointer 連）。用「這個內容是不是所有分支都需要」判斷要不要拆出去。
5. **只在拆分真的划算時才拆 skill**：按 invocation 拆（新增常駐 description 值得嗎）或按 sequence 拆（後面步驟會不會誘發 agent 對眼前步驟搶收工）。
6. **修剪要狠**：single source of truth、逐句做 no-op 測試（這句話跟模型預設行為比有沒有差異，沒有就整句刪）。
7. **善用 leading word**：找模型預訓練裡已有的緊湊概念詞（tight, red, tracer bullet 等）取代反覆用長句子講同一件事，同一詞在本文和 description 裡重複出現，兼顧執行錨定與呼叫錨定。
8. **五種失效模式要主動診斷**：premature completion（先修 criterion，才拆步驟）、duplication、sediment（沉積無修剪紀律）、sprawl（太長，用階梯+拆分解）、no-op（模型本來就會做的廢話指令）。
