# 50 — 版本與命名規範：一個工作單位只掛一個編號

> 來歷：GameGen Service 曾同時流通三套編號——Phase 編號、backlog 編號（B-x）、
> feature 拆解編號（F1/F2）——commit 標題疊成「Phase 59 — B-2 F1」，事後
> 無法對照哪個版本出了什麼、也無法從標題判斷單位大小。該專案事後立了
> 「一個識別碼」規則並用 git hook 強制才止血（見其 CLAUDE.md「Versioning」節）。
> 本檔把那套規則搬進模板，開工第一天就生效，不等紊亂發生。

## 1. 版本格式：`vMAJOR.MINOR.PATCH`

- **MAJOR**：1.0 前固定為 `0`；進 1.0 的時機由使用者宣布，不自行升。
- **MINOR**：每個**可出貨的功能**+1，同時 PATCH 歸零。例：`v0.1.0 → v0.2.0`。
- **PATCH**：對現行功能的**修復或小跟進**+1。例：`v0.2.0 → v0.2.1`。
- 本專案第一個出貨單位＝ `v0.1.0`。

**什麼算「可出貨單位」**：pipeline 走完 S1–S6 的一個功能＝ MINOR；
`/pipeline fix` 或單點小修＝ PATCH。純文件／制度檔改動**不掛版本**
（commit 用 `docs:`／`chore:` 開頭，見第 3 節）。

## 2. 唯一編號規則（防紊亂的核心，無例外）

1. 每個可出貨單位對外只有**一個**標籤：版本號。
2. 其他編號各留原地，**永不進 commit 標題或 DEVLOG 條目首句**：
   - tasks.md 的片編號（T1/T2…）→ 只活在該 tasks.md。
   - 規格目錄 slug（`docs/specs/<YYYYMMDD-slug>/`）→ 只活在路徑。
   - ADR 編號（`NNNN`）→ 只活在 `docs/adr/`。
   - 要交叉引用，寫在 commit **body** 或 DEVLOG 條目的內文，不進標題。
3. commit 標題與 DEVLOG 該則的「做了什麼」首句**共用同一字串**：
   `v0.2.0: <一句白話描述>`——一處查得到，處處對得上。
4. **禁止平行開新編號系統**（Phase、Sprint、里程碑代號、代字…）。
   需要分組敘事時，用日期或版本區間（如「v0.2.x 系列」）。
5. **禁止事後重編**：版本號打錯就在下一個號往前修，不回頭改已出貨的
   號與歷史紀錄。

## 3. Commit 標題格式（git hook 強制）

每個 commit 標題必須是下列之一：

- `v<major>.<minor>.<patch>: <desc>` — 可出貨的功能／修復（版本開頭）。
- `docs|chore|refactor|test|build|ci|perf|style(可選scope): <desc>` — 不出貨。
- git 自動產生的 `Merge …`／`Revert …` 放行。

Hook 已放在 `.githooks/commit-msg`（自 GameGen Service 移植）。
本專案目前**尚未 git init**——在那之前本節格式靠自律遵守，hook 不會攔。
**git init 之後執行一次**：`git config core.hooksPath .githooks`，
並把這步寫進當時的 DEVLOG。改格式規則＝同時改本檔與 hook，兩處同步。

## 4. 各檔案怎麼配合

| 檔案 | 規則 |
|---|---|
| `docs/DEVLOG.md` | 條目標題仍是 `## YYYY-MM-DD`（session 制不變）；有出貨時「做了什麼」首句用 `vX.Y.Z: <desc>` 開頭，一則出多版就逐版列 |
| `docs/STATUS.md` | 「目前目標」旁標註最新已出貨版本一個，僅此一處 |
| `docs/specs/` 目錄名 | 維持 `<YYYYMMDD-slug>`，**不含**版本號（規格先於版本存在） |
| commit | 見第 3 節 |

## 建制時的假設（日後可依實況修正）

- 假設專案尚未 `git init`；hook 先就位，git 化當日啟用。
- 假設無發佈管線；若日後有 tag／release 流程，tag 名＝版本號本身
  （`v0.2.0`），不另創格式。
