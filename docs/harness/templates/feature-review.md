# 派工模板：整功能兩軸審查（pipeline S5）

> 用途：功能全部 slice 完成後的整體驗收。單件產物的驗收用 `review.md`，
> 這份是「整個 diff 對照整份 spec」的審查。
> 建議派法：subagent_type=`general-purpose`，model=`sonnet`。
> **鐵則：fresh-context——只給下列素材，不給實作過程的推理或「應該沒問題」暗示。**
> 兩軸也可平行派成兩個 agent（各拿自己那軸的素材），指揮官原樣彙整。

```
【目標與動機】
審查功能 ＜slug＞ 的完整變更。你是獨立驗收者，找不出其實存在的問題
就是驗收失敗。兩軸分開審、分開報，禁止合併排名——程式可能規範全過
但做錯了東西，也可能做對了東西但寫得一團糟。

【素材】
- spec：docs/specs/＜slug＞/spec.md（含驗收基準的 user stories 與 Out of Scope）
- tasks：docs/specs/＜slug＞/tasks.md（各片驗收 checklist）
- 變更範圍：＜git diff 指令，或無 git 時的檔案清單＞
- 測試指令：＜全量測試指令＞——自己重跑，不採信派工者轉述的結果。

【Spec 軸】
- 逐條 user story 與驗收 checklist：達成（附證據 檔案:行號／測試輸出）／
  部分達成／缺漏。
- 列出 spec 沒要求卻出現的行為（scope creep），對照 Out of Scope。
- 列出「看似做了但實作有問題」的項目（例如空殼函式、被 skip 的測試）。

【Standards 軸（全部是判斷題，不是硬性違規）】
- 對照異味清單：Mysterious Name／Duplicated Code／Feature Envy／
  Data Clumps／Primitive Obsession／Repeated Switches／Shotgun Surgery／
  Divergent Change／Speculative Generality／Message Chains／Middle Man／
  Refused Bequest。每個發現：命名異味＋引用位置＋一句話為什麼。
- 專案已文件化的慣例（CONTEXT.md、CONTRIBUTING 等）優先於上述清單；
  lint/formatter 已強制的項目跳過。
- 另檢查：吞錯誤的空 catch、殘留的 debug 輸出、註解掉的程式碼。

【驗收條件（審查工作本身的完成定義）】
- Spec 軸：spec.md 的每條 user story 與 tasks.md 的每條 checklist 都有判定，
  一條不漏；測試指令實跑過且輸出已納入判定。
- Standards 軸：異味清單 12 項逐項對照過（沒發現的不用寫，但不可跳過不查）。
- 範圍鎖——不做：不修改任何檔案、不寫修法、不審 spec 的 Out of Scope
  明示排除的部分、不擴大到本次 diff 以外的既有程式碼。

【回報格式】
- `## Spec` 與 `## Standards` 兩節，各 ≤20 行，各自結尾一行
  「發現 N 項，最嚴重：＜一句話＞」。
- 最後總判定一行：PASS ／ FAIL(Spec) ／ PASS-with-smells。
```
