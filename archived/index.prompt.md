---
mode: agent
description: 入口指令；盤點現況、找出缺口並推薦下一步 Prompt
inputs:
  summary: 需先掌握使用者當前問題與現有產出
  required:
    - 目前的卡關點、目標與時程限制
    - 現有文件與路徑（例如 docs/spec/, docs/prompts/, CHANGELOG.md）
    - 相關 GitHub Issue 編號與重點留言（可透過 MCP 取得）
    - 近期已執行的指令／Prompt 及其主要輸出
    - 語言或格式需求（預設繁體中文 + Markdown）
outputs:
  summary: 提供決策所需的全貌、推薦步驟與後續動作
  include:
    - 專案現況摘要（引用資料附 `#編號`，對推測或待確認的內容標註 🟡／🔴）
    - 近期變更重點（CHANGELOG、Git 提交或 Issue 摘要）
    - 推薦 Prompt 清單（建議 ≤3 項），說明目的、必要輸入、預期產出與 EARS/GWT 覆蓋
    - 建議動作：需建立或更新的 Issue／PR、需補文件、待確認事項（可用 MCP 直接建立）
    - 開放問題：列出尚待使用者提供的資訊
---

# index

## 目的

此指令作為流程入口，負責總覽專案現況、辨識需求缺口，並推薦接下來應執行的 Prompt。關鍵任務包含：
- 判斷 EARS 需求與 GWT 驗收是否齊備。
- 盤點是否已有 BDD / SDD / TDD 輸出及其完整度。
- 修正 Prompt 使用順序，避免重工。

## 操作步驟

在開始前，確認回覆語言為繁體中文，並評估是否可透過 MCP 讀取 GitHub Issue／留言。若權限不足，需在輸出中標註資料來源並向使用者索取關鍵資訊。

### Step 1：快速盤點
1. 檢視 `README.md`、`CHANGELOG.md`、`docs/spec/`、GitHub Issues（如存在）與主要說明文件，記錄最近更新與既有交付物。
2. 擷取與需求、BDD、SDD、TDD 相關的 Issue／PR 編號與最新狀態（`#編號`），組成簡易的「狀態檢查表」（建議欄位：項目、完成度 ✅/⚠️/❌、最新來源）。
3. 判斷是否存在正式需求基準：若需求仍為空白，預設走新需求流程；若已有文件，標記為既有專案並準備影響評估。
4. 紀錄技術堆疊是否與現況不符（例如新框架、部署目標改變）。

### Step 2：快速提問
1. 依檢查表的 ⚠️／❌ 項目的種類，選擇少量封閉式問題追問（EARS、GWT、Scenario、契約、測試、CI/CD、技術堆疊）。
2. 使用「選項 + ⑤ 其他」格式，避免冗長對話；對尚未拿到答案的項目標註 🟡／🔴。
3. 確認本回合的主要目標與時程：是建立新需求、調整既有情境，還是解除 TDD 阻塞。

### Step 3：決策輸出
1. 套用決策矩陣統一建議：
   | 條件 | 推薦 Prompt | 後續提醒 |
   | --- | --- | --- |
   | 尚無正式需求基準 | `requirements.prompt.md` | 產出 EARS / GWT 雛形，預設下一步 `bdd.prompt.md` |
   | 已有需求但需增修 | `requirements-change.prompt.md` | 先列既有 Issue 與交付物，決定更新或新增 |
   | 需求已確認但缺乏情境 | `bdd.prompt.md` | 生成 Scenario 對照表，交給 `sdd.prompt.md` |
   | Scenario 完整，需契約化 | `sdd.prompt.md` | 標記契約與 Mock 待辦，通知 `tdd-requirements.prompt.md` |
   | TDD 尚未建立或需重新盤點 | `tdd-requirements.prompt.md` 或 `tdd-checkpoint.prompt.md` | 首次進入請用 `tdd-requirements`；完成任一子流程後再用 `tdd-checkpoint` |
   | 技術決策不明或變更幅度大 | `tech-stack.prompt.md` | 輸出後記得回填 README / 需求文件 |
   | 某 TDD 階段完成需 commit | `commit-message.prompt.md` | 確認正在 `tdd-*` 分支並僅暫存對應檔案 |
2. 輸出內容至少包含：
   - 狀態檢查表摘要與資料來源（`#編號` + 🔵／🟡／🔴 標示）。
   - 近期變更重點（CHANGELOG、Issue、PR）。
   - 推薦 Prompt 1~3 個，說明目的、必要輸入、預期輸出、EARS/GWT 覆蓋情況。
   - 建議動作（需建立或更新的 Issue／PR、需補文件、MCP 操作）。
   - 開放問題列表，方便下個 Prompt 追問。

## 注意事項

- 先確認資訊是否完整；不足時應向使用者追問。
- 回覆需保持結構化，方便貼入 GitHub Issue。
- 若偵測輸出重複，提醒使用者檢視是否可沿用既有成果。
- 可使用自動化工具時，優先主動建立或更新 Issue 並附連結。
- 所有 Issue 皆以 `#編號` 表示，並在輸出中標示待確認事項（🟡／🔴）。
