---
name: TDD 測試驅動
about: 用於提交測試驅動開發的測試計畫，包含測試矩陣與場景驗證。
title: 'T-<feature_id>-US<us_number>'
labels: 'type: testing', 'domain: tdd'
assignees: ''
---

## TDD 測試計畫表單
請填寫以下資訊，建立完整的測試矩陣與驗證策略

### 測試矩陣:
<!--
建立測試矩陣表格，每行代表一個測試案例，含 Test ID、Scenario ID (BDD)、測試類型、優先順序、資料準備方式、預期結果及狀態欄位。
**Test ID 命名格式**：`{功能ID}-T-{序號}`（例如 `REQ-001-T-101`），這樣可以確保每個功能的測試編號唯一，避免跨功能重複。
狀態欄位初始為 `⏳`，執行對應 Prompt 時更新狀態並附上 Comment 連結。
使用 checkbox (- [ ]) 來標記實現進度。

Example:
| Test ID | Scenario ID (BDD) | 測試類型 | 優先順序 | 資料/Mock | 預期結果 | 狀態 | 備註 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| REQ-001-T-101 | US1-S1 | E2E | P0 | 真實環境 | 系統成功響應 | ⏳ | 成功路徑 |
| REQ-001-T-102 | US1-S1 | E2E | P0 | Mock 失敗 | 系統返回錯誤訊息 | 🔴 [查看](#comment-1234) | 失敗路徑 |
| REQ-001-T-103 | US1-S1 | Unit | P1 | 單元測試資料 | 驗證邏輯正確 | ⏳ | 邊界情況 |

**測試狀態說明**：
- `⏳` = 未開始
- `🔴` = Red（測試失敗）- 首次執行 tdd-red 時建立對應 Test 的 Comment，後續重試追加到同一 Comment
- `🟢` = Green（測試通過）- 執行 tdd-green 時在同一 Comment 中追加結果
- `♻️` = Refactor（已優化）- 執行 tdd-refactor 時在同一 Comment 中追加優化說明

**Comment 連結格式**：`狀態符號 [查看](#comment-XXXX)`，點擊連結可查看該 Test 的完整生命週期紀錄
-->
<test_matrix>

### 測試場景:
<!--
列出各個測試場景的詳細說明，包括場景名稱、輸入、預期結果。每項使用 checkbox (- [ ]) 標記完成進度。

Example:
- [ ] **T-101: 成功建立 Issue**
  - Scenario ID: US1-S1
  - 輸入：完整表單資料
  - 預期：Issue 建立成功，返回編號

- [ ] **T-102: 欄位驗證失敗**
  - Scenario ID: US1-S1
  - 輸入：缺漏必填欄位
  - 預期：系統提示補充，驗證通過後建立
-->
<test_scenarios>

### 測試資料準備:
<!--
說明測試資料策略、Mock 資料來源、真實資料準備方式、邊界資料等。

Example:
- **Mock 資料**：使用 Jest Mock fixtures 模擬 API 回應
- **真實資料**：使用測試資料庫中的預設資料集
- **邊界資料**：空值、極限值、特殊字元等
- **資料準備**：於 setup.js 中初始化，teardown.js 中清理
-->
<test_data>

### 預期結果與通過條件:
<!--
定義各測試場景的通過條件，使用 checkbox (- [ ]) 標記驗收進度。

Example:
- [ ] **T-101 通過條件**：Issue 建立成功，標籤與欄位正確
- [ ] **T-102 通過條件**：驗證邏輯正確，提示訊息清楚
- [ ] **整體通過條件**：所有測試場景通過，覆蓋率 ≥ 80%
-->
<expected_results>

### 測試執行任務板:
<!--
以本 TDD Issue 為單位，規劃 Red / Green / Refactor 三階段的執行任務。
- Task ID 命名格式：`T-{功能ID}-US{序號}-TASK-{流水號}`。
- 每個 TDD Issue 對應 3 個 Task（R → G → Rf），不需逐一列出每個 Test ID。
- 用於指引 `tdd-red` / `tdd-green` / `tdd-refactor` 的工作順序。

Example:
| Task ID | 階段 | 優先順序 | 前置條件 | 協作角色 | 預計完成時程 |
| --- | --- | --- | --- | --- | --- |
| T-REQ-001-US1-TASK-01 | R | P0 | Mock Service 部署完成 | 後端工程師 | 2025-12-10 |
| T-REQ-001-US1-TASK-02 | G | P0 | TASK-01 完成 | 後端工程師 | 2025-12-11 |
| T-REQ-001-US1-TASK-03 | Rf | P1 | 測試保持綠燈 | 後端工程師、QA | 2025-12-12 |
-->
<execution_task_board>
