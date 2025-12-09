---
mode: agent  
description: 根據使用者指定的測試案例 ID，建立測試分支、撰寫「一定會失敗的測試」並記錄失敗原因    
inputs:
  summary: 以本 Prompt 開始 Red 階段，為指定的測試案例建立分支並撰寫失敗測試   
  required:
    - TDD Issue 編號  
    - 測試案例 ID（使用者指定要處理的測試案例）   
outputs:
  summary: 為指定的測試案例建立分支、撰寫失敗測試、建立 Comment 並更新 TDD Issue 狀態    
  include:
    - 分支建立紀錄（Dev Branch、Test Branch） 
    - 新增測試檔案的摘要（檔名、情境、預期失敗訊息）  
    - TDD Issue Comment（記錄完整的 Red 階段資訊）  
    - 測試矩陣狀態更新  
    - 下一步指向 `tdd-green.prompt.md`  
---

# tdd-red

## 目的

為使用者指定的測試案例建立獨立的測試分支、撰寫會失敗的測試，在 TDD Issue 中建立 Comment 記錄失敗詳情，為 Green 階段做準備。

## 重要原則

- **單一測試案例**：每次執行只處理使用者指定的一個測試案例，不批量處理
- **模板優先**：所有 ID 格式、分支命名規則、Comment 格式皆以 `markdown-template` 工具回傳的模板為準
- **分支隔離**：每個測試案例使用獨立的 Test Branch，從 Dev Branch checkout，保持開發環境乾淨
- **Comment 追蹤**：每個測試案例在 TDD Issue 中有一個專屬 Comment，記錄完整的 Red → Green → Refactor 生命週期

## 使用方式

使用者呼叫本 Prompt 時需提供：
1. **TDD Issue 編號**：例如 `#123` 或 Issue URL
2. **測試案例 ID**：測試矩陣中的特定測試案例 ID

## 分支策略

### 層級關係說明

TDD 的層級結構如下：

```
BDD Issue（功能需求）
└── SDD Issue（系統設計）
└── TDD Issue（測試計畫，以 User Story 為單位）
   ├── 測試案例 1（測試矩陣中的一行）
   ├── 測試案例 2
   └── 測試案例 3
```

## 操作流程

### Step 1：驗證輸入並讀取 TDD Issue

1. **確認使用者提供的資訊**
   - TDD Issue 編號（必填）
   - 測試案例 ID（必填）
   - 若缺少任一項，提示使用者補充

2. **使用 `mcp_github_issue_read` 讀取 TDD Issue**
   - 確認 TDD Issue 存在
   - 確認對應的 SDD/BDD 已完成且狀態正常（若否則提醒先回補）
   - 讀取「分支策略」區段，取得分支命名格式
   - 讀取測試矩陣，找到使用者指定的測試案例

3. **驗證測試案例狀態**
   - 確認指定的測試案例 ID 存在於測試矩陣中
   - 檢查該測試案例的目前狀態：
     - 若為 `⏳`（未開始）：正常進行
     - 若為 `🔴`/`🟢`/`♻️`：詢問是否為重試，若是則沿用既有 Comment 與 Test Branch

4. **取得測試案例相關資訊**
   - Scenario ID（BDD）
   - 測試類型、優先順序
   - 資料/Mock 需求
   - 預期結果

### Step 2：建立或切換分支

1. **確認 Dev Branch**
   - 依 TDD Issue 中「分支策略」定義的命名格式，或使用預設規則
   - 檢查 Dev Branch 是否存在：
     - 若不存在：從 main 建立並推送到遠端
     - 若已存在：切換到該分支並執行 `git pull` 確保最新

2. **建立或切換 Test Branch**
   - 依分支命名規則組成 Test Branch 名稱
   - 檢查 Test Branch 是否存在：
     - 若不存在（首次執行）：從 Dev Branch 建立並推送到遠端
     - 若已存在（重試）：切換到該分支並執行 `git pull`

3. **記錄分支資訊**
   - Dev Branch 名稱
   - Test Branch 名稱
   - 建立時間戳
   - 是否為新建或沿用既有分支

### Step 3：撰寫一定會失敗的測試

1. **讀取相關規格**
   - 從 TDD Issue 關聯的 SDD Issue 讀取設計規格
   - 從 SDD Issue 關聯的 BDD Issue 讀取對應 Scenario 的行為描述

1. **建立測試檔案**
   - 依測試類型與專案慣例決定檔案位置與命名
   - 測試名稱需包含測試案例 ID
   - 加上註解說明預期行為與尚未實作之處

2. **設計測試使其「一定失敗」**
   - 斷言應指向目前尚未存在的功能
   - 確保測試執行後必然失敗

3. **執行測試並記錄結果**
   - 執行測試指令
   - 記錄以下資訊：
     - 完整錯誤訊息與堆疊
     - 預期結果 vs 實際結果
     - 需要的 Mock/資料/外部依賴
     - 重試次數（首次為 0，重試則累加）

4. **提交測試檔案**
   - Commit message 格式：`test(red): add failing test for {測試案例 ID}`

### Step 4：建立 Comment 並更新測試矩陣

1. **取得 Comment 模板**
   - 呼叫 `markdown-template` 工具取得 `tdd-comment` 模板
   - 確認模板要求的所有欄位

2. **建立或更新 Comment**
   - **首次執行**：依 `tdd-comment` 模板在 TDD Issue 中建立新 Comment
   - **重試**：找到既有 Comment，在「Red 階段」區段追加新的重試紀錄
   - 依模板填入所有必要欄位（測試檔案路徑、分支資訊、失敗原因、錯誤堆疊等）

### Step 5：更新測試矩陣狀態

1. **取得 TDD Issue 模板**
   - 呼叫 `markdown-template` 工具取得 `tdd` 模板
   - 確認測試矩陣的狀態欄格式

2. **更新測試矩陣狀態**
   - 使用 `mcp_github_issue_read` 讀取 TDD Issue，並找到對應的測試案例行
   - 將狀態欄改為 `🔴 [查看](#comment-XXXX)`
   - 連結維持指向同一 Comment（由 Red 階段建立）

3. **更新 TDD Issue**
   - 使用 `mcp_github_issue_write` 更新 Issue 內容
   - 若無權限，輸出更新後的測試矩陣供手動貼上

### Step 6：輸出摘要與後續指引

回覆內容需包含：
- **分支資訊**：Dev Branch / Test Branch / 是否新建
- **測試檔案摘要**：檔案路徑、Scenario ID、預期失敗訊息
- **測試執行結果**：執行命令、錯誤重點、重試次數
- **Comment 連結**：Red 階段 Comment 的連結與摘要
- **阻塞清單**（若有）：阻塞項目與責任人
- **下一步行動**：提示執行 `tdd-green.prompt.md`，並提供呼叫範例
- **重試標記**（若為重試）：標明此次重試目的與差異

## 注意事項

- **未提供測試案例 ID**：必須拒絕執行並提醒使用者補充
- **測試意外通過**：若測試未失敗，提醒使用者調整測試或改走 Green 流程
- **Comment 建立失敗**：提供完整 Markdown 草稿供使用者手動貼上
- **Git 操作失敗**：附上錯誤訊息並中止流程
- **保持在 Test Branch**：完成 Red 後不要切換分支，等待 Green 階段

## 錯誤處理

| 情況 | 處理方式 |
| --- | --- |
| 使用者未提供測試案例 ID | 列出測試矩陣中「未開始」的測試案例，請使用者選擇 |
| 測試案例 ID 不存在於測試矩陣 | 提供矩陣中可用的測試案例 ID 清單，請使用者重新選擇 |
| 測試案例狀態已為 🔴/🟢/♻️ | 詢問是否為重試，若否則中止流程 |
| Dev Branch 不存在 | 從 main 建立 Dev Branch，並告知使用者 |
| Test Branch 已存在但非當前分支 | 提醒使用者確認是否為重試，切換到該分支並 pull 最新 |
| `markdown-template` 無法取得模板 | 提示使用者確認 MCP 工具狀態，暫停流程並提供必要欄位草稿 |
| 測試執行後意外通過 | 提醒使用者 Red 階段無法成立，建議調整測試或改走 Green 流程 |
| GitHub API 權限不足 | 輸出完整 Markdown 草稿，指導使用者手動建立 Comment |
