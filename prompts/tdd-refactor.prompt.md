---
mode: agent  
description: 在測試保持綠燈的前提下進行重構以改善程式碼品質，並於同一 Comment 中記錄優化結果   
inputs:
  summary: 本 Prompt 指導可選的 Refactor 階段重構  
  required:
    - TDD Issue 編號  
    - 測試案例 ID（使用者指定要進行重構的測試案例）  
    - Green 階段已完成（存在對應的通過測試與 Comment）  
  optional:
    - 重構目標（程式碼結構、可讀性、效能等）  
    - 技術債清單或待優化項目  
outputs:
  summary: 記錄重構內容、品質檢查結果與驗證證據  
  include:
    - 重構項目摘要（檔案、改善目的、驗證結果）  
    - 品質檢查執行紀錄（Lint、Type Check、複雜度分析等）  
    - 在既有 Green 階段 Comment 中追加 Refactor 階段結果  
    - 測試矩陣狀態更新  
    - 未完成的技術債與後續建議  
---

# tdd-refactor

## 目的

在維持所有測試綠燈的前提下，進行程式碼重構或品質改善，提升程式碼結構與可維護性，並記錄優化過程。

## 重要原則

- **模板優先**：所有 Comment 格式、欄位名稱、標記方式皆以 `markdown-template` 工具回傳的 `tdd-comment` 模板為準
- **測試優先**：每次重構修改後必須確保所有測試保持綠燈，不允許破壞既有功能
- **同一 Comment 生命週期**：Red 階段建立的 Comment 在 Refactor 階段追加結果，保持 Test ID 的完整紀錄
- **可選階段**：若無重構需求，可跳過此階段直接進入下一個測試案例或提交 PR

## 前置條件

- 已有對應的 TDD Issue 且指定測試案例已進入 Green 階段（狀態為 🟢）
- 存在對應的 Comment（由 Red 階段建立，Green 階段已追加結果）
- 可存取 Git 分支與測試執行環境
- 有明確的重構目標（程式碼結構、可讀性、效能、技術債清理等）

## 使用方式

使用者呼叫本 Prompt 時需提供：
1. **TDD Issue 編號**：例如 `#123` 或 Issue URL
2. **測試案例 ID**：已進入 Green 階段的測試案例 ID

## 操作流程

### Step 1：驗證輸入並讀取 Green 階段資訊

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
     - 若為 🟢（Green 階段完成）：正常進行
     - 若為 ⏳（未開始）：提示使用者「該測試案例尚未開始，請先執行 `tdd-red.prompt.md`」
     - 若為 🔴（Red 階段）：提示使用者「該測試案例尚未通過，請先執行 `tdd-green.prompt.md`」
     - 若為 ♻️（已完成 Refactor）：詢問是否為再次重構

4. **定位並讀取既有 Comment**
   - 在 TDD Issue 的 Comment 中找到對應測試案例的 Comment
   - Comment 標題格式應為：`## Test: [Test ID] - [Scenario ID]`
   - 讀取 Comment 中「Red 階段」與「Green 階段」區段的資訊
   - 確認測試檔案路徑、Test Branch 資訊

5. **切換並更新 Test Branch**
   - 切換到對應的 Test Branch（來自 Red/Green 階段紀錄）
   - 執行 `git pull` 確保最新程式碼

### Step 2：確認重構基線與目標

1. **確認所有測試為綠燈**
   - 執行該測試套件的完整測試
   - 記錄測試執行結果作為重構前基線
   - **重要**：若測試失敗，中止 Refactor 流程，提示使用者先執行 `tdd-green.prompt.md` 修復

2. **確認重構目標**
   - 詢問使用者重構的具體目標：
     - ① 改善程式碼結構（拆分函數、模組化）
     - ② 提升可讀性（命名、註解、格式）
     - ③ 優化效能（演算法、快取）
     - ④ 清理技術債（移除重複、過時程式碼）
     - ⑤ 其他（請說明）
   - 列出預計修改的檔案與改善項目

3. **評估是否需要重構**
   - 若無明確重構需求，提示使用者可跳過此階段
   - 若使用者確認跳過，結束流程並提供後續行動指引

### Step 3：執行重構

1. **逐項進行重構**
   - 一次專注一個改善項目，避免大範圍同時修改
   - 每次修改後執行測試，確保測試保持綠燈
   - 記錄修改的檔案名稱與改善目的

2. **確保無迴歸**
   - 執行該測試所屬測試套件的完整測試
   - 確認無任何測試被破壞
   - 若有測試失敗，回滾修改並重新評估重構方式

3. **提交重構程式碼**
   - Commit message 格式：`refactor: improve {改善項目} for {測試案例 ID}`
   - 推送 Test Branch 到遠端

### Step 4：執行品質檢查

依據專案的 CI/CD 配置執行以下檢查（清單應以專案實際設定為準）：

1. **Lint 檢查**
   - 執行專案的 Linter（例如 ESLint、Pylint）
   - 記錄檢查結果、通過/失敗狀態

2. **型別檢查**（若使用 TypeScript、Python type hints 等）
   - 執行型別檢查工具（例如 tsc、mypy）
   - 記錄檢查結果

3. **程式碼複雜度檢查**（若適用）
   - 執行複雜度分析工具
   - 比較重構前後的複雜度指標

4. **其他品質檢查**
   - 覆蓋率檢查（確認覆蓋率未下降）
   - 安全掃描（若有集成）

5. **記錄檢查結果**
   - 所有檢查都應通過或達到可接受的閾值
   - 若檢查失敗，修正後重新執行

### Step 5：取得 Comment 模板並更新 Comment

1. **取得 tdd-comment 模板**
   - 呼叫 `markdown-template` 工具取得當前的 `tdd-comment` 模板
   - 確認模板中 Refactor 階段區段的欄位要求（時戳格式、狀態符號、必填欄位等）

2. **準備 Refactor 階段內容**
   - Refactor 階段時戳（當前執行時間）
   - 重構項目摘要（檔案清單、改善目的、改善效果）
   - 測試驗證結果（所有測試保持通過狀態）
   - 品質檢查結果（Lint、Type Check、複雜度分析等）
   - 重構前後比較（若有量化指標）

3. **在既有 Comment 中追加 Refactor 階段結果**
   - 定位到 Red 階段建立的 Comment（標題為 `## Test: [Test ID] - [Scenario ID]`）
   - 在 Comment 中找到「### Green 階段」區段之後
   - 追加新的區段，格式為「### Refactor 階段」
   - 依 `tdd-comment` 模板填入所有必要欄位
   - 使用 GitHub API 或手動編輯 Comment 內容

4. **若無法更新 Comment**
   - 若無 GitHub API 權限，輸出完整 Refactor 階段 Markdown 內容
   - 指導使用者手動在 Comment 中追加該內容

### Step 6：更新測試矩陣狀態

1. **取得 TDD Issue 模板**
   - 呼叫 `markdown-template` 工具取得 `tdd` 模板
   - 確認測試矩陣的狀態欄格式

2. **更新測試矩陣狀態**
   - 使用 `mcp_github_issue_read` 讀取 TDD Issue，並找到對應的測試案例行
   - 將狀態欄從 `🟢 [查看](#comment-XXXX)` 改為 `♻️ [查看](#comment-XXXX)`
   - 連結維持指向同一 Comment（由 Red 階段建立）

3. **更新 TDD Issue**
   - 使用 `mcp_github_issue_write` 更新 Issue 內容
   - 若無權限，輸出更新後的測試矩陣供手動貼上

### Step 7：輸出摘要與後續指引

回覆內容需包含：

- **測試案例基本資訊**：Test ID、Scenario ID、Test Branch
- **重構摘要表**：
  | 檔案路徑 | 改善項目 | 改善效果 | 測試結果 |
  | --- | --- | --- | --- |
  | src/auth/service.ts | 拆分驗證邏輯至 validator.ts | 提升可測試性 | ✓ 通過 |
  | src/auth/validator.ts | 新增獨立驗證模組 | 減少耦合度 | ✓ 通過 |

- **品質檢查結果**：Lint 結果、Type Check 結果、複雜度分析結果
- **Comment 更新**：Comment 連結與摘要
- **技術債追蹤**（若有）：未完成的優化項目、建議處理時程
- **後續行動**：
  - 若有其他測試案例待處理：提示執行 `tdd-red.prompt.md` 處理下一個測試
  - 若所有測試完成且無更多重構需求：提示提交 PR 並關聯 TDD Issue
  - 若重構期間測試失敗：提示回到 `tdd-green.prompt.md` 修正

## 注意事項

- **測試優先**：任何重構都不能破壞既有測試，每次修改後必須驗證測試保持綠燈
- **格式依模板**：Comment 格式、欄位名稱、時戳格式皆以 `markdown-template` 回傳為準
- **同一 Comment 生命週期**：每個 Test ID 應只有一個 Comment，Red/Green/Refactor 都追加到該 Comment 中
- **可選階段**：Refactor 不是必要步驟，若無重構需求可直接跳過
- **保持在 Test Branch**：Refactor 階段完成後不要切換分支，等待 PR 提交

## 常見情況處理

| 情況 | 處理方式 |
| --- | --- |
| 測試案例 ID 不存在於測試矩陣 | 提供矩陣中可用的 🟢 狀態測試案例清單，請使用者重新選擇 |
| 測試案例狀態不是 🟢 | 依狀態提示先執行對應的 Prompt（⏳→tdd-red、🔴→tdd-green） |
| 重構後測試失敗 | 回滾修改，重新評估重構方式，或回到 `tdd-green.prompt.md` 修正 |
| 品質檢查失敗 | 修正程式碼以符合檢查要求，重新執行檢查直到通過 |
| 無明確重構需求 | 提示使用者可跳過此階段，直接進入下一個測試案例或提交 PR |
| `markdown-template` 無法取得模板 | 提示使用者確認 MCP 工具狀態，暫停流程並提供 Refactor 階段內容草稿 |
| GitHub API 權限不足 | 輸出完整 Markdown 草稿（測試矩陣更新 + Comment 追加內容），指導使用者手動更新 |

## 錯誤處理

| 情況 | 處理方式 |
| --- | --- |
| 使用者未提供測試案例 ID | 列出測試矩陣中 🟢 狀態的測試案例，請使用者選擇 |
| Green 階段 Comment 不存在 | 提示「該測試案例的 Green 階段紀錄不存在，請先執行 `tdd-green.prompt.md`」 |
| Test Branch 檢出失敗 | 附上 Git 錯誤訊息，中止流程並提示檢查分支是否存在、網路連線是否正常 |
| 重構前測試就已失敗 | 中止 Refactor 流程，提示使用者先執行 `tdd-green.prompt.md` 修復測試 |
| Comment 追加失敗 | 輸出完整 Markdown 草稿，指導使用者手動編輯 Comment |