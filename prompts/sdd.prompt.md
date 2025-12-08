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
    - 依據最新的 SDD Issue 模板說明（透過 `markdown-template` 取得）生成草稿或直接建立 Issue
    - 列出契約設計、介面規範、資料模型（包含驗證方式與 Mock 策略）
    - 依照 BDD Scenario 製作「設計任務板」，列出可立即執行的技術任務（至少 3 項），標註 `Scenario ID`、`優先順序 (P0/P1/P2)` 與需要的前置輸入
    - 提供下一個建議 Prompt（預設 `tdd-requirements.prompt.md`）
---

# 系統設計分析（SDD 導向）

## 目的

以不斷提問的方式釐清系統設計需求，並將 BDD 使用者故事轉換為具體的技術規範、介面契約與資料模型，方便後續 TDD 實作，同時產出帶有優先序的設計任務板以指引工程啟動。

## 前置條件

- **重要**：對應的 BDD Issue 必須已被加上 `approved` label。若 BDD Issue 尚未核准，請拒絕進行 SDD 問答，並建議使用者先完成 BDD 審核
- 建議先完成 BDD Issue（或至少有明確的使用者故事），若無則透過問答補齊
- 若需求有變動，應先回到 BDD 更新後再進行 SDD

## 提問原則

- 採「單一問題 + 選項 + 自訂輸入」格式，便於使用者快速回覆
- 每題最多 4 個選項，預留「⑤ 其他／自行輸入」

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

### Step 1：確認狀態

1. **檢查 Sub-Issue 關係與功能 ID**：確認當前 SDD Issue 是否已被設為某個 BDD Issue 的 Sub-Issue，並提取「功能 ID」
  - 若已有明確的 BDD Issue 編號（使用者提供或自動偵測），透過 MCP 查詢該 BDD Issue（使用 `mcp_github_issue_read` 工具）
  - 依最新模板說明得知標題及欄位中對功能 ID 的要求，並確保後續建立的 SDD Issue 與模板規範一致

2. **檢查 BDD Issue 核准狀態**：確認對應的 BDD Issue 是否已被加上 `approved` label
  - 若 BDD Issue 未被核准，拒絕進行 SDD 問答，並提醒使用者「請先將 BDD Issue 加上 `approved` label 後再呼叫本 Prompt」
  - 若 BDD Issue 已被核准，繼續進行

3. 閱讀使用者提供的文件（如：BDD Issue、設計文件、架構圖、現有契約等），了解業務需求與技術脈絡

4. **參考舊 SDD Issue**（選填）：若使用者提供過去建立的 SDD Issue 編號作為參考（例如：#2, #5），讀取這些 SDD Issue 的內容以了解過去的設計決策
   - **重要**：僅作為參考，**不修改舊的 SDD Issue**（用於追蹤歷史）
   - 新的 SDD Issue 會是全新建立，編號遞增

5. 檢查是否已有對應此 BDD 的 SDD Issue。若無，則建立新的

### Step 2：補齊設計

1. 發現設計的不一致、缺漏或技術可行性問題
2. 藉由不斷提問，釐清系統設計需求：
   - 介面契約（API、事件、資料格式）
   - 資料模型（欄位、類型、驗證規則）
   - 規範要求（GDPR、WCAG、ISO 等）
   - Mock 資料策略
3. **重要**：SDD 階段聚焦於技術規範與介面設計，不涉及具體實作細節（程式語言、框架選擇等留給 TDD），並且每一個設計與驗證活動都要維持與 BDD Scenario ID 的一對一對應；Scenario ID 格式以目前 BDD 規範（由 MCP 取得）為準。
   - **多 Scenario 情況**：若同一個 User Story 有多個 Scenario，通常首個 SDD Issue 會詳細設計首個主要 Scenario，其他 Scenario 的測試可參考此 SDD Issue 的設計方式進行測試設計。

### Step 3：整理輸出

1. 整理契約對照表、Mock 策略、驗證方式，確保符合 SDD 需求，並維持 BDD Scenario ID 的一對一對應（Scenario ID 格式以 MCP 提供的當前 BDD 規範為準）

2. **使用 MCP 工具格式化 Issue 內容**：
  - 呼叫 `markdown-template` 工具，從內部挑選合適的 SDD 模板
  - 傳入收集到的欄位值，欄位名稱與呈現方式皆以模板回傳版本為準
  - 模板若提示新的欄位或命名規範，應即時補齊資料或追問使用者，避免沿用舊格式

3. 透過可用的 MCP / GitHub API 建立**新的** SDD Issue（標題格式由 MCP 提供的當前 BDD 規範決定）
   - 使用 `mcp_github_issue_write` 工具建立 Issue，body 內容為步驟 2 格式化後的結果
   - **重要**：每個 BDD User Story 建立對應的 SDD Issue（編號遞增），即使參考了舊的 SDD Issue

4. 若因權限受限無法建立 Issue，則輸出完整草稿供手動貼上

5. **重要 - Sub-Issue 關聯**：SDD Issue 建立完成後，由 AI Agent 透過 MCP 建立 Sub-Issue 關係：
   - 使用 `mcp_github_sub_issue_write` 工具
   - 參數設定：
     ```
     method: add
     owner: hsiangjenli
     repo: prompts
     issue_number: <對應的 BDD Issue 編號>
     sub_issue_id: <新建立的 SDD Issue ID (node_id)>
     ```
   - 確認關聯成功：BDD Issue 的 GitHub 介面上會自動顯示此 SDD Issue 為 Sub-Issue

6. 在輸出中附上 Issue 連結或草稿，以及建議的下一個 Prompt

### Step 3.5：設計任務板

1. 以 SDD Issue 涵蓋的 Scenario 為列（Scenario ID 依當前 BDD 規範，透過 MCP 取得），製作任務表格（建議欄位：`Task ID`、`Scenario`、`內容`、`優先順序`、`前置輸入`、`預計輸出`）。任務命名規則應以模板或專案既有準則為準，若模板提供規範則必須遵循。
2. 任務描述需具體到可啟動的工程活動，例如「產出 API 合約草稿」或「定義資料庫欄位驗證規則」。
3. 為每個任務標註 `P0/P1/P2`，其中 `P0` 任務應在進入 TDD 前完成，並附註所需資源、共用文件或依賴。
4. 指定每個任務的預期交付物與同步對象，確保跨團隊協作時不遺漏資訊。

> 完整任務板需與輸出的 SDD Issue 連動，確保後續 TDD Prompt 能直接引用任務編號與 Scenario。

## 後續行動

- 依設計任務板中的 `P0` 任務安排優先投入的工程工作，並更新 Sub-Issue 關聯狀態
- 下一個預計執行的 Prompt（預設 `tdd-requirements.prompt.md`，準備進入測試階段）
- 若設計過程中發現需求問題，建議回到 `requirements.prompt.md` 更新 BDD Issue
