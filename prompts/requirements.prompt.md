---
mode: agent
description: 先理解需求並整理成 BDD 使用者故事與 Gherkin 情境，為後續 SDD -> TDD Prompt 提供完整輸入
inputs:
  summary: 使用 Prompt 釐清使用者需求
  required:
    - 直接對話進行釐清
    - 提供會議記錄、文件、`README.md`、GitHub Issue Number 等相關資訊
outputs:
  summary: 以使用者情境為主，整理可追蹤的需求摘要，準備交由 SDD/TDD 進一步展開（非技術實作細節）
  include:
    - 依據 `markdown-template` 取得的 BDD Issue 模板生成草稿或直接建立 Issue
    - 列出使用者故事與 Gherkin 情境（僅含行為與情境，禁止加入技術、API、資料模型或實作細節）
    - SDD 對接路線圖，標示各 Scenario 的優先順序與設計焦點
---

# 需求分析（BDD 導向）

## 目的

以不斷提問的方式釐清需求，並使用 BDD 使用者故事（User Story）與 Gherkin 語法整理需求，方便後續對齊 SDD → TDD 的實作流程。

## 重要原則

- **模板優先**：所有 ID 格式、欄位名稱、命名規則皆以 `markdown-template` 工具回傳的模板為準，本 Prompt 不定義具體格式
- **行為導向**：BDD 階段僅聚焦於使用者行為與業務需求，禁止包含任何技術細節（API、資料模型、架構設計等）
- **可追蹤性**：每個 User Story 與 Scenario 必須有唯一識別碼，便於後續 SDD/TDD 關聯

## 提問原則

- 採「單一問題 + 選項 + 自訂輸入」格式，便於使用者快速回覆
- 每題最多 4 個選項，預留「其他／自行輸入」選項
- 一次只問一個問題，避免資訊過載

### 常用問題題庫

```
- 這個功能的主要使用者是誰？
  ① 一般使用者  ② 管理員  ③ 系統管理員  ④ 其他（請說明）
- 使用者想要達成什麼目標？
  （請具體描述使用者的需求或痛點）
- 使用者在什麼情境下會使用這個功能？
  （例如：登入後、查看資料時、收到通知時）
- 使用者期望看到什麼結果？
  （請描述成功的狀態或畫面）
- 如果操作失敗，使用者應該看到什麼？
  （例如：錯誤訊息、提示、替代方案）
- 這個功能有沒有前置條件？
  （例如：需要先登入、需要先完成某個步驟）
- 使用者可能會遇到哪些異常情況？
  ① 輸入格式錯誤  ② 權限不足  ③ 資料不存在  ④ 其他（請說明）
```

## 操作流程

### Step 1：確認現有狀態

1. **檢查是否已有相關 BDD Issue**
   - 詢問使用者是否有既有的 BDD Issue 編號
   - 若有，使用 `mcp_github_issue_read` 讀取現有內容，確認是「新增」還是「修改」需求
   - 若為修改既有需求，提醒使用者考慮是否應改用 `requirements-change.prompt.md`

2. **閱讀使用者提供的背景資料**
   - 會議記錄、文件、`README.md`、相關 Issue 等
   - 整理已知資訊，標記需要釐清的部分

### Step 2：需求釐清與補齊

1. **識別需求缺口**
   - 根據 Step 1 的資料，列出需要釐清的問題
   - 使用「常用問題題庫」逐一提問

2. **整理 User Story**
   - 使用「作為...，我想要...，以便...」格式
   - 每個 User Story 聚焦於單一使用者的使用情境

3. **發展 Gherkin Scenario**
   - 為每個 User Story 撰寫至少一個 Scenario
   - 識別並補充失敗路徑、邊界情況的 Scenario
   - 確保 Given-When-Then 描述的是「行為」而非「實作」

4. **確認完整性**
   - 與使用者確認所有 User Story 和 Scenario 是否完整
   - 標記任何待確認項目為「待確認」

### Step 3：取得模板並建立 Issue

1. **取得 BDD Issue 模板**
   - 呼叫 `markdown-template` 工具取得當前的 BDD Issue 模板
   - 確認模板要求的所有欄位（功能 ID 格式、Scenario ID 格式、必填欄位等）

2. **確保 ID 唯一性**
   - 使用 `mcp_github_issue_read` 查詢現有 BDD Issue
   - 依照模板的 ID 命名規則，產生不重複的功能 ID

3. **填入模板欄位**
   - 依模板要求填入所有欄位
   - User Story 與 Scenario 的 ID 格式以模板為準

4. **建立或更新 Issue**
   - 使用 `mcp_github_issue_write` 建立 Issue
   - 確保標籤包含模板指定的預設標籤（如 `type: feature`, `domain: bdd`）
   - **不要**加上 `approved` 標籤（需等待 PM/Reviewer 審核）
   - 若因權限受限無法建立，輸出完整 Markdown 草稿供手動貼上

### Step 4：輸出摘要與後續行動

1. **輸出內容**
   - BDD Issue 連結（或草稿）
   - User Story 與 Scenario 清單摘要
   - SDD 對接路線圖（依模板格式）
   - 待確認項目清單（若有）

2. **後續行動指引**
   - BDD Issue 已建立，但尚未核准
   - 請等待 PM/Reviewer 審核並加上 `approved` 標籤
   - 核准後，執行 `sdd.prompt.md` 進行系統設計

3. **SDD 對接路線圖說明**
   - 列出建議優先進入 SDD 的 Scenario（P0 優先）
   - 說明每個 Scenario 預計討論的設計議題
   - 標註需要參與的角色

## 注意事項

- **禁止技術細節**：本階段不討論 API 設計、資料庫結構、程式架構等技術實作
- **格式依模板**：所有 ID、欄位名稱、表格結構皆以 `markdown-template` 回傳為準
- **待確認標註**：對於使用者未明確回答的項目，標註「待確認」並列入待確認清單
- **不自行核准**：建立的 BDD Issue 不應有 `approved` 標籤，必須等待人工審核

## 錯誤處理

| 情況 | 處理方式 |
| --- | --- |
| `markdown-template` 無法取得模板 | 提示使用者確認 MCP 工具是否正常運作，暫停流程 |
| 使用者提供的資訊不足以建立任何 User Story | 持續提問直到至少有一個完整的 User Story |
| GitHub API 權限不足 | 輸出完整 Markdown 草稿，指導使用者手動建立 |
| 發現與既有 Issue 有衝突 | 提示使用者確認是否為需求變更，建議改用 `requirements-change.prompt.md` |