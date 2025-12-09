---
mode: agent  
description: 透過問答方式釐清系統設計與契約需求，將 BDD 使用者故事轉為具體的技術規範與介面設計  
inputs:
  summary: 使用 Prompt 透過互動式問答釐清系統設計  
  required:
    - 直接對話進行釐清  
    - 提供 BDD Issue 編號（必填）、設計文件、架構圖、現有契約文件等相關資訊  
  optional:
    - 參考舊 SDD Issue 編號（例如：#2, #5），作為設計參考（不會修改舊 Issue，僅供查閱）  
outputs:
  summary: 以技術規範為主，整理可追蹤的系統設計摘要，準備交由 TDD 進一步展開  
  include:
    - 依據 `markdown-template` 取得的 SDD Issue 模板生成草稿或直接建立 Issue  
    - 列出契約設計、介面規範、資料模型（包含驗證方式與 Mock 策略）  
    - 提供下一個建議 Prompt（預設 `tdd-requirements.prompt.md`）  
---

# 系統設計分析（SDD 導向）

## 目的

以不斷提問的方式釐清系統設計需求，並將 BDD 使用者故事轉換為具體的技術規範、介面契約與資料模型，方便後續 TDD 實作。

## 重要原則

- **模板優先**：所有 ID 格式、欄位名稱、命名規則皆以 `markdown-template` 工具回傳的模板為準，本 Prompt 不定義具體格式
- **技術導向**：SDD 階段聚焦於技術規範與介面設計，包含架構決策、框架選擇、資料模型等
- **可追蹤性**：每個設計項目必須對應到 BDD Scenario ID，維持一對一關聯

## 前置條件

- **必要**：對應的 BDD Issue 必須已被加上 `approved` label
- 若 BDD Issue 尚未核准，拒絕進行 SDD 問答，並提醒使用者先完成 BDD 審核
- 若需求有變動，應先回到 BDD 更新後再進行 SDD

## 提問原則

- 採「單一問題 + 選項 + 自訂輸入」格式，便於使用者快速回覆
- 每題最多 4 個選項，預留「其他／自行輸入」選項
- 一次只問一個問題，避免資訊過載

### 常用問題題庫

```
- 這個功能涉及哪些子系統或元件？
  ① API 介面  ② 資料庫  ③ 前端 UI  ④ 其他（請說明）
  
- 這個功能需要什麼類型的介面契約？
  ① REST API  ② GraphQL  ③ 事件訊息（Event）  ④ 其他（請說明）
  
- API 的輸入參數有哪些？
  （請列出參數名稱、類型、是否必填、驗證規則）
  
- API 的回應格式是什麼？
  （請說明成功回應的欄位與格式，包含狀態碼）
  
- 如果 API 呼叫失敗，應該回傳什麼錯誤訊息？
  ① 標準錯誤碼 + 訊息  ② 自訂錯誤格式  ③ 沿用現有格式  ④ 其他（請說明）
  
- 這個功能需要哪些資料欄位？
  （請列出欄位名稱、類型、長度限制、預設值）
  
- 資料需要什麼驗證規則？
  ① 格式驗證（Email、電話等）  ② 範圍驗證（最小/最大值）  ③ 必填檢查  ④ 其他（請說明）
  
- 是否需要 Mock 資料進行測試？
  ① 需要（請說明 Mock 資料情境）  ② 不需要  ③ 使用現有測試資料  ④ 其他（請說明）
  
- 這個設計需要符合哪些規範或標準？
  ① GDPR  ② WCAG 2.1  ③ ISO 27001  ④ 其他（請說明）
  
- 受影響的元件或功能有哪些？
  （例如：使用者註冊、會員資料儲存、權限檢查）
  
- 如何驗證這個設計是否正確實作？
  ① 自動化測試  ② 手動檢查  ③ 第三方驗證工具  ④ 其他（請說明）
```

## 操作流程

### Step 1：確認現有狀態

1. **檢查 BDD Issue 核准狀態**
   - 若已有明確的 BDD Issue 編號，使用 `mcp_github_issue_read` 查詢該 Issue
   - 確認是否已被加上 `approved` label
   - 若未核准，拒絕進行 SDD 問答，提醒使用者「請先將 BDD Issue 加上 `approved` label 後再呼叫本 Prompt」

2. **閱讀背景資料**
   - BDD Issue 內容、設計文件、架構圖、現有契約等
   - 了解業務需求與技術脈絡

3. **參考舊 SDD Issue**（選填）
   - 若使用者提供過去的 SDD Issue 編號作為參考，讀取內容以了解過去的設計決策
   - **注意**：僅作為參考，不修改舊的 SDD Issue

4. **檢查是否已有對應的 SDD Issue**
   - 若有，確認是「新增」還是「修改」
   - 若為修改既有設計，提醒使用者考慮是否應改用 `requirements-change.prompt.md`

### Step 2：設計釐清與補齊

1. **識別設計缺口**
   - 根據 Step 1 的資料，列出需要釐清的問題
   - 使用「常用問題題庫」逐一提問

2. **釐清系統設計需求**
   - 介面契約（API、事件、資料格式）
   - 資料模型（欄位、類型、驗證規則）
   - 規範要求（GDPR、WCAG、ISO 等）
   - Mock 資料策略

3. **維持 BDD Scenario 對應**
   - 每個設計項目必須對應到 BDD Scenario ID
   - Scenario ID 格式以 `markdown-template` 取得的模板為準
   - 若同一個 User Story 有多個 Scenario，說明各 Scenario 的設計差異

4. **確認完整性**
   - 與使用者確認所有設計項目是否完整
   - 標記任何待確認項目為「待確認」

### Step 3：取得模板並建立 Issue

1. **取得 SDD Issue 模板**
   - 呼叫 `markdown-template` 工具取得當前的 SDD Issue 模板
   - 確認模板要求的所有欄位（標題格式、ID 格式、必填欄位等）

2. **填入模板欄位**
   - 依模板要求填入所有欄位
   - 契約對照表、Mock 策略、驗證方式等皆以模板結構為準
   - 若模板有新欄位或命名規範變更，應即時補齊資料或追問使用者

3. **建立 SDD Issue**
   - 使用 `mcp_github_issue_write` 建立 Issue
   - 確保標籤包含模板指定的預設標籤
   - 若因權限受限無法建立，輸出完整 Markdown 草稿供手動貼上

4. **建立 Sub-Issue 關聯**
   - 使用 `mcp_github_sub_issue_write` 將 SDD Issue 設為 BDD Issue 的 Sub-Issue
   - 確認關聯成功：BDD Issue 的 GitHub 介面上應顯示此 SDD Issue 為 Sub-Issue

### Step 4：輸出摘要與後續行動

1. **輸出內容**
   - SDD Issue 連結（或草稿）
   - 契約設計摘要
   - 待確認項目清單（若有）

2. **後續行動指引**
   - 下一個預計執行的 Prompt：`tdd-requirements.prompt.md`
   - 若設計過程中發現需求問題，建議使用 `requirements-change.prompt.md` 評估變更影響

## 注意事項

- **禁止實作細節**：本階段不討論具體程式碼實作，專注於規格定義
- **格式依模板**：所有 ID、欄位名稱、表格結構皆以 `markdown-template` 回傳為準
- **待確認標註**：對於使用者未明確回答的項目，標註「待確認」並列入待確認清單
- **Sub-Issue 必建**：SDD Issue 建立後必須設為 BDD Issue 的 Sub-Issue，維持追蹤鏈完整

## 錯誤處理

| 情況 | 處理方式 |
| --- | --- |
| BDD Issue 未核准 | 拒絕進行 SDD 問答，提醒使用者先完成 BDD 審核 |
| `markdown-template` 無法取得模板 | 提示使用者確認 MCP 工具是否正常運作，暫停流程 |
| 使用者提供的資訊不足以建立設計規格 | 持續提問直到至少有一個完整的介面契約或資料模型 |
| GitHub API 權限不足 | 輸出完整 Markdown 草稿，指導使用者手動建立 |
| Sub-Issue 關聯建立失敗 | 提示使用者手動在 BDD Issue 中加入 Sub-Issue 關聯 |