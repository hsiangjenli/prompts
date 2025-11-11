---
mode: agent
description: 根據 TDD Issue 中的測試矩陣，撰寫「一定會失敗的測試」並記錄失敗原因與阻塞
inputs:
  summary: 以本 Prompt 開始 Red 階段，撰寫失敗測試
  required:
    - TDD Issue 編號與測試矩陣（由 tdd-requirements.prompt.md 提供）
    - 對應的 BDD Scenario / SDD 契約細節
    - 測試框架與執行指令（例如 `pnpm test --filter ...`）
    - 測試資料、Mock 或外部依賴的準備情況
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 撰寫失敗測試並更新 TDD Issue 的 Red 階段狀態，準備進入 Green
  include:
    - 新增測試檔案的摘要（檔名、情境、預期失敗訊息）
    - 測試執行結果與錯誤詳情
    - 失敗原因分析與阻塞清單
    - 下一步指向 `tdd-green.prompt.md`
---

# tdd-red

## 目的

依照測試矩陣撰寫會失敗的測試，清楚記錄失敗原因、阻塞與需要補強的資料，為 Green 階段做準備。

## 流程

### Step 1：確認測試矩陣與準備

1. 檢查 TDD Issue 中的測試矩陣，確認「測試框架」「Mock 策略」「資料準備」等都已就位
2. 從測試矩陣選定此次要處理的 Test ID（通常按優先順序 P0 → P1 → P2）
3. 確認測試框架與執行指令可用（例如 `pnpm test --filter ...`）

### Step 2：撰寫一定會失敗的測試

1. 根據 Scenario ID（`US<序號>-S<序號>`）和 SDD 契約細節，新增測試檔案
2. **重要**：設計測試使其「一定失敗」，測試預期行為但實現還不存在
   - 使用註解說明「預期行為是什麼」
   - 斷言應指向尚未實現的功能
3. 執行測試並記錄：
   - 失敗的錯誤訊息（完整堆疊）
   - 預期結果 vs 實際結果
   - 需要的依賴或資料

### Step 3：整理輸出與更新狀態

1. 記錄此次 Red 階段的成果：
   - 新增的測試檔案名稱與路徑
   - 失敗情境摘要
   - 完整的錯誤訊息
   - 阻塞清單（如有）

2. **為該 Test ID 建立或追加 Comment**：
   
   **首次執行 tdd-red 時（建立新 Comment）**：
   - 在 TDD Issue 中建立新 Comment，標題為「`## Test: [Test ID] - [Scenario ID]`」
   - 記錄以下 6 項必要資訊：
     - Test ID + Scenario ID（例如 T-101 + US1-S1）
     - 測試檔案路徑
     - Red 階段時戳與失敗狀態（🔴）
     - 失敗原因簡述（錯誤訊息前 120 字）
     - 完整的錯誤訊息與堆疊
     - CI/CD 執行連結或本地輸出
   - Comment 格式參考 Issue #5 的「初始 Comment 格式」
   
   **重試同一 Test 時（追加到既有 Comment）**：
   - 在同一 Comment 中追加新的執行紀錄，包括時戳、重試次數、新的失敗訊息
   - 使用「### Red 階段 - [時戳]」格式，保持紀錄清晰
   - 累計重試次數，方便後續判斷是否需要人工介入

3. **更新 TDD Issue 測試矩陣的 Red 欄位**：
   - 找到對應的 Test ID 行
   - 將 Red 欄位從 `⏳` (未開始) 更新為 `🔴 [查看](#comment-XXXX)` (已失敗 + Comment 連結)
   - 連結應指向剛建立或更新的 Comment

4. 指出下一步執行 `tdd-green.prompt.md`

## 產出格式建議

- **測試摘要表**：Test ID、測試檔路徑、Scenario ID、失敗原因摘要（限 120 字）、狀態
- **錯誤詳情**：完整的錯誤訊息、堆疊、預期結果對比
- **阻塞清單**：若有資料/Mock/權限缺口，列出負責人與完成時間

## 後續建議

- 完成 Red 後立即執行 `tdd-green.prompt.md` 進行實作
- 若測試無法失敗（說明實作已存在），需調整測試或檢查是否有需求差異
