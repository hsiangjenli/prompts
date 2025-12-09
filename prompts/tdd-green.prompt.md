---
mode: agent  
description: 根據 Red 階段的失敗測試進行最小實作，讓測試轉為綠燈，並於同一 Comment 中記錄進度  
inputs:
  summary: 以本 Prompt 指導 Green 階段的實作與驗證  
  required:
    - TDD Issue 編號  
    - 測試案例 ID（使用者指定要完成的測試案例）  
    - Red 階段已完成（存在對應的失敗測試與 Comment）  
  optional:
    - 預期的實作位置、相關實作指南或設計文件  
outputs:
  summary: 記錄最小實作、測試轉綠狀態、品質檢查結果與遇到的阻塞  
  include:
    - 實作修改摘要（檔案、修改重點、測試命令、成功結果）  
    - 品質檢查執行紀錄（Lint、Type Check 等執行結果）  
    - 在既有 Red 階段 Comment 中追加 Green 階段結果  
    - 測試矩陣狀態更新  
    - 未解決的阻塞與後續建議  
---

# tdd-green

## 目的

根據 Red 階段製作的失敗測試，實作最小可行程式碼讓測試轉為綠燈，同時維持程式品質並記錄遇到的議題。

## 重要原則

- **模板優先**：所有 Comment 格式、欄位名稱、標記方式皆以 `markdown-template` 工具回傳的 `tdd-comment` 模板為準
- **最小實作**：一次只實作讓一個失敗測試通過所需的程式碼，不提前實作其他功能
- **同一 Comment 生命週期**：Red 階段建立的 Comment 在 Green 階段追加結果，保持 Test ID 的完整紀錄
- **品質檢查**：實作後必須執行 Lint、Type Check 等守門檢查，確保程式品質

## 前置條件

- 已有對應的 TDD Issue 且內含失敗測試（由 `tdd-red` 建立）
- 指定的測試案例已進入 Red 階段（狀態為 🔴），有對應的失敗 Comment
- 可存取 Git 分支與測試執行環境
- 理解對應的 BDD Scenario 與 SDD 規格

## 使用方式

使用者呼叫本 Prompt 時需提供：
1. **TDD Issue 編號**：例如 `#123` 或 Issue URL
2. **測試案例 ID**：已進入 Red 階段的測試案例 ID

## 操作流程

### Step 1：驗證輸入並讀取 Red 階段資訊

1. **確認使用者提供的資訊**
   - TDD Issue 編號（必填）
   - 測試案例 ID（必填）
   - 若缺少任一項，提示使用者補充

2. **使用 `mcp_github_issue_read` 讀取 TDD Issue**
   - 確認 TDD Issue 存在
   - 確認對應的 SDD/BDD 已完成且狀態正常
   - 讀取測試矩陣，找到使用者指定的測試案例

3. **驗證測試案例狀態**
   - 確認指定的測試案例 ID 存在於測試矩陣中
   - 檢查該測試案例的目前狀態：
     - 若為 🔴（Red 階段完成）：正常進行
     - 若為 ⏳（未開始）：提示使用者「該測試案例尚未進入 Red 階段，請先執行 `tdd-red.prompt.md`」
     - 若為 🟢/♻️（已完成）：詢問是否為重試或修復失敗情況

4. **定位並讀取 Red 階段 Comment**
   - 在 TDD Issue 的 Comment 中找到對應測試案例的 Comment
   - Comment 標題格式應為：`## Test: [Test ID] - [Scenario ID]`
   - 讀取 Comment 中「Red 階段」區段的失敗訊息、重試次數、阻塞清單
   - 確認測試檔案路徑、Test Branch 資訊

5. **切換並更新 Test Branch**
   - 切換到對應的 Test Branch（來自 Red 階段紀錄）
   - 執行 `git pull` 確保最新程式碼與測試檔案

### Step 2：最小實作

1. **理解失敗原因與預期實作**
   - 從 Red 階段 Comment 中讀取失敗原因、錯誤訊息、堆疊追蹤
   - 查閱對應 BDD Scenario 與 SDD 規格，確認預期行為
   - 列出最小實作所需的改動項目（一次只實作讓當前測試通過）

2. **實作最小可行程式碼**
   - 一次專注一個測試案例，不批量實作其他相關功能
   - 修改必要的程式碼檔案
   - 記錄修改的檔案名稱與改動重點（簡述改動目的，不詳述程式碼邏輯）

3. **執行失敗的測試並驗證轉綠**
   - 在 Test Branch 中執行該測試案例的測試命令
   - 確認測試轉為綠燈（成功狀態）
   - 記錄測試執行命令、成功結果與時戳
   - **重要**：若測試仍未通過，記錄新的失敗訊息、調整實作後重試，將重試次數累加

4. **確保無迴歸**
   - 執行該測試所屬測試套件的完整測試
   - 確認無其他測試被破壞
   - 若有迴歸，調整實作避免破壞既有功能

5. **提交實作程式碼**
   - Commit message 格式：`feat(green): implement {測試案例 ID} to pass test`
   - 推送 Test Branch 到遠端

### Step 3：執行品質檢查

依據專案的 CI/CD 配置執行以下檢查（清單應以專案實際設定為準）：

1. **Lint 檢查**
   - 執行專案的 Linter（例如 ESLint、Pylint）
   - 記錄檢查結果、通過/失敗狀態

2. **型別檢查**（若使用 TypeScript、Python type hints 等）
   - 執行型別檢查工具（例如 tsc、mypy）
   - 記錄檢查結果

3. **其他守門檢查**
   - 程式碼複雜度檢查（如適用）
   - 覆蓋率檢查（若有最低覆蓋率要求）
   - 安全掃描（若有集成）

4. **記錄檢查結果**
   - 所有檢查都應通過或達到可接受的閾值
   - 若檢查失敗，修正後重新執行

### Step 4：取得 Comment 模板並更新 Comment

1. **取得 tdd-comment 模板**
   - 呼叫 `markdown-template` 工具取得當前的 `tdd-comment` 模板
   - 確認模板中 Green 階段區段的欄位要求（時戳格式、狀態符號、必填欄位等）

2. **準備 Green 階段內容**
   - Green 階段時戳（當前執行時間）
   - 實作修改摘要（檔案清單、改動重點）
   - 測試通過的完整證據（測試命令、測試輸出或截圖）
   - 品質檢查結果（Lint、Type Check 等的執行結果）
   - 重試次數（若為首次通過則為 1，之後累加）
   - **功能 ID 與 Test ID 的對應確認**（依模板或專案既有指南呈現）

3. **在既有 Red 階段 Comment 中追加 Green 階段結果**
   - 定位到 Red 階段建立的 Comment（標題為 `## Test: [Test ID] - [Scenario ID]`）
   - 在 Comment 中找到「### Red 階段 - <timestamp>」區段
   - 追加新的區段，格式為「### Green 階段 - <timestamp>」
     （其中 <timestamp> 應填入當前執行時間，格式依 `tdd-comment` 模板為準）
   - 依 `tdd-comment` 模板填入所有必要欄位
   - 使用 GitHub API 或手動編輯 Comment 內容

4. **若無法更新 Comment**
   - 若無 GitHub API 權限，輸出完整 Green 階段 Markdown 內容
   - 指導使用者手動在 Comment 中追加該內容

### Step 5：更新測試矩陣狀態

1. **取得 TDD Issue 模板**
   - 呼叫 `markdown-template` 工具取得 `tdd` 模板
   - 確認測試矩陣的狀態欄格式

2. **更新測試矩陣狀態**
   - 使用 `mcp_github_issue_read` 讀取 TDD Issue，並找到對應的測試案例行
   - 將狀態欄從 `🔴 [查看](#comment-XXXX)` 改為 `🟢 [查看](#comment-XXXX)`
   - 連結維持指向同一 Comment（由 Red 階段建立）

3. **更新 TDD Issue**
   - 使用 `mcp_github_issue_write` 更新 Issue 內容
   - 若無權限，輸出更新後的測試矩陣供手動貼上

### Step 6：輸出摘要與後續指引

回覆內容需包含：

- **測試案例基本資訊**：Test ID、Scenario ID、Test Branch
- **實作摘要表**：
  | 檔案路徑 | 修改重點 | 測試命令 | 成功狀態 |
  | --- | --- | --- | --- |
  | src/auth/service.ts | 實作 login 函數 | npm test -- auth.test.ts | ✓ 通過 |

- **品質檢查結果**：Lint 結果、Type Check 結果、其他檢查結果
- **Comment 更新**：Comment 連結與摘要
- **重試資訊**（若有重試）：重試次數、失敗原因、修正方式
- **阻塞清單**（若有）：未解決的阻塞項目與責任人
- **後續行動**：
  - 若所有測試都通過：提示考慮執行 `tdd-refactor.prompt.md` 進行優化（可選）
  - 若有其他 Red 階段測試待進行：提示執行 `tdd-red.prompt.md` 處理下一個測試
  - 若所有測試完成：提示提交 PR 並關聯 TDD Issue

## 注意事項

- **最小實作原則**：不實作超過該測試所需的功能，避免提前實作導致設計偏差
- **格式依模板**：Comment 格式、欄位名稱、時戳格式皆以 `markdown-template` 回傳為準
- **同一 Comment 生命週期**：每個 Test ID 應只有一個 Comment，Red/Green/Refactor 都追加到該 Comment 中
- **功能 ID 追蹤**：確保 Test ID 格式與 BDD/SDD 的功能 ID 一致，便於自動追蹤
- **品質檢查必執行**：不可跳過 Lint、Type Check 等守門檢查
- **保持在 Test Branch**：Green 階段完成後不要切換分支，等待 Refactor 或 PR 提交

## 常見情況處理

| 情況 | 處理方式 |
| --- | --- |
| 測試案例 ID 不存在於測試矩陣 | 提供矩陣中可用的 🔴 狀態測試案例清單，請使用者重新選擇 |
| 測試案例狀態不是 🔴 | 詢問是否為重試、修復失敗或重新實作，確認意圖後進行 |
| 實作後測試仍失敗 | 記錄新的失敗訊息、調整實作後重試，將重試次數累加，更新 Comment |
| 品質檢查失敗 | 修正程式碼以符合檢查要求，重新執行檢查直到通過 |
| `markdown-template` 無法取得模板 | 提示使用者確認 MCP 工具狀態，暫停流程並提供 Green 階段內容草稿 |
| GitHub API 權限不足 | 輸出完整 Markdown 草稿（測試矩陣更新 + Comment 追加內容），指導使用者手動更新 |
| 無迴歸但有其他測試失敗 | 檢查是否為依賴該功能的其他測試，若是則調整實作或其他測試，記錄為阻塞 |

## 錯誤處理

| 情況 | 處理方式 |
| --- | --- |
| 使用者未提供測試案例 ID | 列出測試矩陣中 🔴 狀態的測試案例，請使用者選擇 |
| Red 階段 Comment 不存在 | 提示「該測試案例的 Red 階段紀錄不存在，請先執行 `tdd-red.prompt.md`」 |
| Test Branch 檢出失敗 | 附上 Git 錯誤訊息，中止流程並提示檢查分支是否存在、網路連線是否正常 |
| 測試檔案不存在或路徑錯誤 | 根據 Red 階段紀錄的測試檔案路徑檢查，若路徑有誤提示使用者確認 |
| 測試執行環境缺漏依賴 | 附上依賴錯誤訊息，提示安裝缺漏的套件或依賴 |
| 測試執行後意外通過但不是預期結果 | 提示「測試通過但請確認是否為真正實作或測試設計有誤」 |
| Comment 追加失敗 | 輸出完整 Markdown 草稿，指導使用者手動編輯 Comment |