---
mode: agent
description: 透過問答方式釐清測試需求背景，將 SDD 規範轉為具體的測試計畫與驗證策略
inputs:
  summary: 使用 Prompt 透過互動式問答釐清測試需求
  required:
    - 直接對話進行釐清
    - 提供 BDD Issue 編號、SDD Issue 編號（選填，若已有）、測試文件、現有測試案例等相關資訊
outputs:
  summary: 以測試驗證為主，建立完整測試矩陣與 TDD Issue，準備進入 Red-Green-Refactor 循環
  include:
    - 建立「測試矩陣」（Test ID、Scenario ID、測試類型、優先順序、資料準備等），其中 Test ID 格式為 `{功能ID}-T-{序號}`（例如 `REQ-001-T-101`）
    - 直接建立 TDD Issue（`.github/ISSUE_TEMPLATE/tdd.yaml`），標題採 `T-<功能ID>-US<序號>` 格式
    - 更新 BDD Issue 的「相關 TDD Issue」表格，建立雙向關聯
    - 列出測試場景、驗證方式、測試資料準備方式
    - 提供下一個建議 Prompt（預設 `tdd-red.prompt.md`）
---

# 測試需求分析（TDD 導向）

## 目的

以不斷提問的方式釐清測試需求，並將 SDD 技術規範轉換為具體的測試計畫、驗證策略與測試資料，方便後續 TDD 實作。

## 前置條件

- **重要**：對應的 BDD Issue 必須已被加上 `approved` label，且 SDD Issue 必須已建立完成。若任何一個條件未滿足，請拒絕進行 TDD 問答，並建議使用者先完成相關流程
- 建議先完成 BDD Issue 與 SDD Issue（或至少有明確的設計規範），若無則透過問答補齊
- 若需求或設計有變動，應先回到 BDD/SDD 更新後再進行 TDD

## 提問原則

- 採「單一問題 + 選項 + 自訂輸入」格式，便於使用者快速回覆
- 每題最多 4 個選項，預留「⑤ 其他／自行輸入」

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

### Step 1：確認狀態

1. **檢查 BDD 與 SDD 完成狀態與功能 ID**：確認對應的 BDD Issue 是否已被加上 `approved` label，且 SDD Issue 是否已建立
   - 若 BDD Issue 未被批准或 SDD Issue 未建立，拒絕進行 TDD 問答，並提醒使用者「請先完成 BDD 批准與 SDD 建立後再呼叫本 Prompt」
   - 若兩者都已完成，讀取 BDD Issue 與 SDD Issue 中的「功能 ID」欄位值（例如 REQ-001）
   - **重要**：後續建立的 TDD Issue 必須在 Title 和「功能 ID」欄位中使用相同的功能 ID
   - 若兩者都已完成，繼續進行
2. 閱讀使用者提供的文件（如：BDD Issue、SDD Issue、測試文件、現有測試案例等），了解業務需求與設計規範
3. 檢查是否已有 TDD Issue。若有，請根據原本的 Issue 進行修改

### Step 2：補齊需求

1. 發現測試需求的不一致、缺漏或技術可行性問題
2. 藉由不斷提問，釐清測試需求：
   - 測試場景（成功路徑、失敗路徑、邊界情況）
  - 每個測試場景都必須對應到 BDD 的 `US<序號>-S<序號>` Scenario ID，確保可追蹤性
   - 測試資料與 Mock 策略
   - 驗證方式與工具
   - 非功能需求（效能、安全、相容性等）
3. **重要**：TDD 階段聚焦於測試驗證策略，具體測試案例由後續 Prompt 撰寫

### Step 3：建立測試矩陣與 TDD Issue

#### Phase 1：建立測試矩陣

1. 根據 Step 2 的對話結果與 BDD Issue 中的功能 ID，為每個測試場景建立「Test ID」
   - 格式：`{功能ID}-T-{序號}`（例如 REQ-001-T-101、REQ-001-T-102）
   - 這樣可以確保每個功能的測試編號唯一，避免跨功能重複
   - 對應 BDD 的 `US<序號>-S<序號>` Scenario ID

2. 整理成「測試矩陣」表格，欄位包含：
   - `Test ID`：測試唯一識別碼（格式：`{功能ID}-T-{序號}`）
   - `Scenario ID (BDD-###)`：對應的 BDD Scenario
   - `測試類型`：單元 / 整合 / E2E
   - `優先順序`：P0 / P1 / P2（根據對話決定）
   - `資料/Mock`：測試資料準備方式
   - `預期結果`：測試通過條件
   - `狀態`：單一狀態欄位追蹤測試進度（見下方狀態說明）
   - `備註`：額外說明

3. 確認測試資料、Mock 或外部依賴準備清單，標記負責人與完成時間

#### Phase 2：建立 TDD Issue

1. 立即透過 MCP / GitHub API 建立 **TDD Issue**
   - 標題格式：`T-<功能ID>-US<序號>` （例如 `T-REQ-001-US1`）
   - 使用 `.github/ISSUE_TEMPLATE/tdd.yaml` 模板

2. 在 TDD Issue 的各欄位填入：
   - **功能 ID**：填入與 BDD Issue 相同的功能 ID（例如 `REQ-001`）
   - **對應 BDD Issue**：記錄此 TDD 依據的 BDD Issue 編號（例如 `#1`）
   - **對應 SDD Issue**：記錄此 TDD 參考的 SDD Issue 編號（例如 `#3`）
   - **測試矩陣**：貼入完整的測試矩陣表格，Test ID 使用 `{功能ID}-T-{序號}` 格式
   - **測試場景**：列出所有測試場景與驗證方式，每行帶上 `US<序號>-S<序號>` Scenario ID
   - **測試資料**：說明資料準備方式、Mock 策略、環境要求
   - **預期結果**：描述各場景的預期通過條件

3. 若因權限受限無法建立 Issue，則輸出完整 Markdown 草稿供手動貼上

#### 測試狀態追蹤說明

建立的 TDD Issue 中，測試矩陣應包含單一「狀態」欄位，並配合 Comment 管理完整的測試生命週期：

- **初始狀態**：所有測試項目初始設為 `⏳` (未開始)
- **Red 階段**：
  - 首次執行 `tdd-red.prompt.md` 時，為該 Test ID（例如 REQ-001-T-101）建立獨立 Comment，記錄失敗訊息
  - 將測試矩陣狀態欄位改為 `🔴 [查看](#comment-XXXX)` (測試失敗 + Comment 連結)
  - 若重試同一 Test，則在同一 Comment 中追加新的執行紀錄與重試次數
- **Green 階段**：
  - 執行 `tdd-green.prompt.md` 時，在 tdd-red 建立的同一 Comment 中追加 Green 階段結果
  - 將測試矩陣狀態欄位改為 `🟢 [查看](#comment-XXXX)` (測試通過 + 連結至同一 Comment)
  - 記錄實作修改摘要、測試通過證據、品質檢查結果
- **Refactor 階段**（可選）：
  - 執行 `tdd-refactor.prompt.md` 時，在同一 Comment 中追加 Refactor 階段結果
  - 將測試矩陣狀態欄位改為 `♻️ [查看](#comment-XXXX)` (已優化 + 連結至同一 Comment)
  - 記錄重構改善項目、測試驗證結果、品質檢查

**Comment 結構規範**（詳見 SDD Issue #5）：
- 每個 Test ID 維持一個獨立 Comment Thread（避免 Issue 評論區混亂）
- Comment 記錄 6 項必要資訊：Test ID、Scenario ID、測試檔路徑、各階段時戳與結果、失敗原因、重試累計、CI 連結
- Comment 格式統一，便於追蹤完整的測試生命週期

#### Phase 3：建立 Sub-Issue 關聯

**重要提醒：此步驟是必須的，避免遺漏。**

1. **將新建立的 TDD Issue 加入為 SDD Issue 的 Sub-Issue**
   - 使用 `mcp_github_sub_issue_write` 工具
   - 參數設定：
     ```
     method: add
     owner: hsiangjenli
     repo: prompts
     issue_number: <對應的 SDD Issue 編號>
     sub_issue_id: <新建立的 TDD Issue ID (node_id)>
     ```
   - 確認關聯成功：SDD Issue 的 GitHub 介面上會自動顯示此 TDD Issue 為 Sub-Issue

2. **驗證完整的層級結構**
   - BDD Issue（祖父）→ SDD Issue（父）→ TDD Issue（本 Issue，子）
   - 確保在各層級 Issue 上都可看到 Sub-Issues 列表
   - 三個 Issue 之間形成完整的追蹤鏈

#### Phase 4：輸出整理

1. 在輸出中附上：
   - 建立的 TDD Issue 連結
   - 完整的測試矩陣（供後續 `tdd-red.prompt.md` 使用）
   - 測試資料準備清單與負責人
   - 待補資料或阻塞清單（如有）
   - 測試狀態追蹤說明（Red/Green/Refactor 狀態更新方式）

2. 指出下一步應執行 `tdd-red.prompt.md`，並提醒攜帶測試矩陣以及新 TDD Issue 編號

3. **更新完成清單（必須完成）**：
   - ☐ TDD Issue 已建立並帶有編號
   - ☐ TDD Issue 已透過 `mcp_github_sub_issue_write` 加入為對應 SDD Issue 的 Sub-Issue
   - ☐ 在 SDD Issue 的 GitHub 介面上確認可看到此 TDD Issue 顯示為 Sub-Issue
   - ☐ 完整層級結構已建立：BDD Issue → SDD Issue → TDD Issue
   - ☐ 三個 Issue 之間的 Sub-Issue 鏈接已建立並可相互追蹤

#### TDD Issue 內容範例

## 後續行動

- **在執行 `tdd-red.prompt.md` 前，務必確認 Phase 3 的 Sub-Issue 關聯已建立完成**
- 下一個預計執行的 Prompt：`tdd-red.prompt.md`（開始設計失敗測試）
- 若設計或測試過程中發現需求問題，建議回到 `sdd.prompt.md` 或 `requirements.prompt.md` 更新相應 Issue
- **重要提醒**：BDD ↔ SDD ↔ TDD 三層 Sub-Issue 關聯建立後，才能確保整個工作流的可追蹤性
