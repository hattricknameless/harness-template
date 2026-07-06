---
name: status
description: 快速掌握專案現況：讀 STATUS／DEVLOG／未完成的 specs 並摘要。用法：/status
disable-model-invocation: true
---

# Status：現在做到哪了

唯讀操作，**不改任何檔案**：

1. 讀 `docs/STATUS.md`（不存在就回報「尚未建立，建議收工時打 /wrap」）。
2. 讀 `docs/DEVLOG.md` 最新 3 則。
3. 掃 `docs/specs/*/tasks.md` 有無非 done 的片。

回報 ≤15 行：目前目標／上次 session 做到哪／未完成的 run（有的話附 slug
與剩幾片）／建議下一步——具體到可執行的動作（例如「`/pipeline` 續跑
`<slug>`，從 T3 接」，不要只說「繼續開發」）。
