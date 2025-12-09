---
mode: agent
description: 透過問答方式釐清測試需求背景，將 SDD 規範轉為具體的測試計畫與驗證策略  
inputs:
  summary: 使用 Prompt 透過互動式問答釐清測試需求  
  required:
    - 直接對話進行釐清  
    - 提供 BDD Issue 編號、SDD Issue 編號（必填）、測試文件、現有測試案例等相關資訊  
outputs:
  summary: 以測試驗證為主，建立完整測試矩陣與 TDD Issue，準備進入 Red-Green-Refactor 循環  
  include:
    - 依據 `markdown-template` 取得的 TDD Issue 模板生成草稿或直接建立 Issue  
    - 列出測試場景、驗證方式、測試資料準備方式  
    - 提供下一個建議 Prompt（預設 `tdd-red.prompt.md`）  
---

# 測試需求分析（TDD 導向）

## 目的

以不斷提問的方式釐清測試需求，並將 SDD 技術規範轉換為具體的測試計畫、驗證策略與測試資料，方便後續 TDD 實作。

## 重要原則

- **模板優先**：所有 ID 格式、欄位名稱、命名規則皆以 `markdown-template` 工具回傳的模板為準，本 Prompt 不定義具體格式
- **測試導向**：TDD 階段聚焦於測試驗證策略，具體測試案例由後續 Red/Green/Refactor Prompt 撰寫
- **可追蹤性**：每個測試場景必須對應到 BDD Scenario ID，維持追蹤鏈完整

## 前置條件

- **必要**：對應的 BDD Issue 必須已被加上 `approved` label，且 SDD Issue 必須已建立完成
- 若任何一個條件未滿足，拒絕進行 TDD 問答，並提醒使用者先完成相關流程
- 若需求或設計有變動，應先回到 BDD/SDD 更新後再進行 TDD

## 提問原則

- 採「單一問題 + 選項 + 自訂輸入」格式，便於使用者快速回覆
- 每題最多 4 個選項，預留「其他／自行輸入」選項
- 一次只問一個問題，避免資訊過載

### 常用問題題庫

```
- 這個功能的主要測試場景有哪些？
  （例如：成功路徑、失敗路徑、邊界情況、異常情況）

- 每個測試場景需要哪些輸入資料？
  （請列出參數、類型、有效值範圍、邊界值）

- 每個測試場景預期的輸出或結果是什麼？
  （請描述成功/失敗時的結果）

- 測試需要什麼類型的測試資料？
  ① 單位測試資料  ② 整合測試資料  ③ 端到端測試資料  ④ 其他（請說明）

- 測試資料是否可以用 Mock 或需要真實資料？
  ① Mock 資料即可  ② 需要真實資料  ③ 混合使用  ④ 其他（請說明）

- 測試需要什麼驗證方式？
  ① 自動化單元測試  ② 整合測試  ③ E2E 測試  ④ 其他（請說明）

- 測試環境有什麼限制或依賴？
  （例如：資料庫、外部 API、第三方服務）

- 測試是否需要檢查效能或安全相關的非功能需求？
  ① 效能（回應時間、吞吐量）  ② 安全（權限、資料加密）  ③ 相容性  ④ 其他（請說明）

- 已有相關的測試工具或框架嗎？
  ① Jest  ② Pytest  ③ Cypress  ④ 其他（請說明）

- 測試預計的涵蓋率目標是多少？
  ① 80%+  ② 60-80%  ③ 沒有特定目標  ④ 其他（請說明）
```

## 操作流程

### Step 1：確認現有狀態

1. **檢查 BDD 與 SDD 完成狀態**
   - 使用 `mcp_github_issue_read` 查詢 BDD 以及 SDD Issue，確認是否已被加上 `approved` label
   - 若任一條件未滿足，拒絕進行 TDD 問答，提醒使用者「請先完成 BDD、SDD 核准後再呼叫本 Prompt」

2. **閱讀背景資料**
   - BDD Issue、SDD Issue、測試文件、現有測試案例等
   - 了解業務需求與規格設計規範

3. **檢查是否已有對應的 TDD Issue**
   - 若有，確認是「新增」還是「修改」
   - 若為修改既有測試計畫，提醒使用者考慮是否應改用 `requirements-change.prompt.md`

### Step 2：測試需求釐清與補齊

1. **識別測試需求缺口**
   - 根據 Step 1 的資料，列出需要釐清的問題
   - 使用「常用問題題庫」逐一提問

2. **釐清測試需求**
   - 測試場景（成功路徑、失敗路徑、邊界情況）
   - 測試資料與 Mock 策略
   - 驗證方式與工具
   - 非功能需求（效能、安全、相容性等）

3. **維持 BDD Scenario 對應**
   - 每個測試場景必須對應到 BDD Scenario ID
   - Scenario ID 格式以 `markdown-template` 取得的模板為準

4. **確認完整性**
   - 與使用者確認所有測試場景是否完整
   - 標記任何待確認項目為「待確認」

### Step 3：取得模板並建立 Issue

1. **取得 TDD Issue 模板**
   - 呼叫 `markdown-template` 工具取得當前的 TDD Issue 模板
   - 確認模板要求的所有欄位（標題格式、Test ID 格式、測試矩陣欄位等）

2. **建立測試矩陣**
   - 依模板要求的欄位與格式建立測試矩陣
   - 若模板定義了狀態欄位或標記方式，依其規範初始化
   - 確認測試資料、Mock 或外部依賴準備清單

3. **建立 TDD Issue**
   - 使用 `mcp_github_issue_write` 建立 Issue
   - 確保標籤包含模板指定的預設標籤
   - 若因權限受限無法建立，輸出完整 Markdown 草稿供手動貼上

4. **建立 Sub-Issue 關聯**
   - 使用 `mcp_github_sub_issue_write` 將 TDD Issue 設為 SDD Issue 的 Sub-Issue
   - 確認關聯成功：SDD Issue 的 GitHub 介面上應顯示此 TDD Issue 為 Sub-Issue
   - 驗證完整層級結構：BDD Issue -> SDD Issue -> TDD Issue

### Step 4：輸出摘要與後續行動

1. **輸出內容**
   - TDD Issue 連結（或草稿）
   - 測試矩陣摘要
   - 測試資料準備清單
   - 待確認項目清單（若有）

2. **後續行動指引**
   - 下一個預計執行的 Prompt：`tdd-red.prompt.md`
   - 若測試規劃過程中發現設計問題，建議使用 `requirements-change.prompt.md` 評估變更影響

## 測試狀態追蹤說明

TDD Issue 中的測試矩陣應包含「狀態」欄位，配合 Comment 管理完整的測試生命週期：

- **初始狀態**：依模板定義的預設值初始化
- **Red 階段**：執行 `tdd-red.prompt.md` 時，為該 Test ID 建立獨立 Comment，記錄失敗訊息，更新狀態欄位
- **Green 階段**：執行 `tdd-green.prompt.md` 時，在同一 Comment 中追加結果，更新狀態欄位
- **Refactor 階段**（可選）：執行 `tdd-refactor.prompt.md` 時，在同一 Comment 中追加結果，更新狀態欄位

每個 Test ID 維持一個獨立 Comment，記錄完整的測試生命週期。

## 注意事項

- **禁止實作細節**：本階段不討論具體測試程式碼，專注於測試規劃
- **格式依模板**：所有 ID、欄位名稱、表格結構皆以 `markdown-template` 回傳為準
- **待確認標註**：對於使用者未明確回答的項目，標註「待確認」並列入待確認清單
- **Sub-Issue 必建**：TDD Issue 建立後必須設為 SDD Issue 的 Sub-Issue，維持追蹤鏈完整

## 錯誤處理

| 情況 | 處理方式 |
| --- | --- |
| BDD Issue 未建立、核准 | 拒絕進行 TDD 問答，提醒使用者先完成 BDD 建立或審核 |
| SDD Issue 未建立、核准 | 拒絕進行 TDD 問答，提醒使用者先完成 SDD 建立或審核 |
| `markdown-template` 無法取得模板 | 提示使用者確認 MCP 工具是否正常運作，暫停流程 |
| 使用者提供的資訊不足以建立測試矩陣 | 持續提問直到至少有一個完整的測試場景 |
| GitHub API 權限不足 | 輸出完整 Markdown 草稿，指導使用者手動建立 |
| Sub-Issue 關聯建立失敗 | 提示使用者手動在 SDD Issue 中加入 Sub-Issue 關聯 |