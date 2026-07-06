---
name: calibrate
description: 分析使用者過往的 Claude session 紀錄，萃取協作模式並校準制度與記憶。用法：/calibrate [專案名，留空則自動挑有新語料的]
disable-model-invocation: true
---

# Calibrate：用歷史互動證據校準制度

從本機 session 紀錄挖出使用者的真實工作模式，蒸餾成記憶與制度規則。
原則：**證據優先於猜測**——沒有原話佐證的結論不落地。

## 步驟

1. **盤點語料**：掃 `C:\Users\User\.claude\projects\` 各專案目錄的 `.jsonl`
   數量／大小／最新時間。對照 `docs/notes/user-patterns-*.md` 既有報告的
   日期，找出「上次校準後有新語料」的專案。`$ARGUMENTS` 有指名就只分析
   指名的。完成判準：列出本次要分析的專案清單與理由，給使用者過目。
2. **派工分析**：每個大專案派一個 sonnet general-purpose subagent
   （背景、平行），prompt 照本目錄 `dispatch-template.md` 填。
   語料超過 30MB 的專案務必提醒 subagent 抽樣。
3. **交叉比對（指揮官自做）**：報告回來後，只有滿足其一才算耐久模式——
   (a) 出現在兩個以上專案；(b) 同專案內重複出現多次。孤例只記錄不落地
   （性質嚴重的孤例——如擅自加功能被罵——可落地但要標明是孤例）。
4. **落地**（依耐久度分層）：
   - 協作偏好、驗收紅線 → 記憶目錄（type: user / feedback，附原話與
     報告路徑）；已有同主題記憶檔就更新，不開新檔。
   - 該變成規則的 → 照 `docs/harness/30-maintenance.md` 改制度檔
     （改前確認已 commit、收緊可自行、放寬先問）。
   - 落地後派 fresh-context read-back 驗收（硬規則 1）。
5. **誠實回報**：改了什麼（檔案路徑）／哪些問題找不到證據／樣本量警語
   （訊息數少於 100 條的專案，結論標「低信心」）。

## 何時跑

- 大專案收尾時、或距上次校準累積了明顯新語料時。
- 注意：session 紀錄預設約 30 天自動清理（`cleanupPeriodDays`），
  想分析的語料要在消失前跑。

## 硬規則

- 主對話不直接讀 `.jsonl`（單檔動輒數 MB，會淹死 context）——一律派工。
- 每個落地的結論必須能指回報告裡的使用者原話。
- 引用中的 API key／密碼／個資一律遮蔽。
- 放寬既有制度規則的建議：先問使用者，不自行落地。
