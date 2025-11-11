---
mode: agent
description: 依據 Red 階段的失敗測試進行最小實作，讓測試轉為綠燈
inputs:
  summary: 以本 Prompt 指導 Green 階段的實作與驗證
  required:
    - 目標 TDD Issue 與測試矩陣（來自 `tdd-red` 的失敗測試清單）
    - 失敗訊息、程式位置與阻塞清單（來自 `tdd-red` 產出）
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 記錄最小實作、測試轉綠狀態與遇到的阻塞
  include:
    - 實作修改摘要（檔案、修改重點、測試命令、成功結果）
    - 品質檢查執行紀錄（Lint、Type Check 等執行結果）
    - 未解決的阻塞與後續建議
---

# tdd-green

## 目的

根據 Red 階段製作的失敗測試，實作最小可行程式碼讓測試轉為綠燈，同時維持程式品質並記錄遇到的議題。

## 流程

### Step 1：確認目標
- 回顧 Red 階段的失敗測試清單與錯誤訊息
- 確認要轉綠的測試項目（Test ID 格式為 `{功能ID}-T-{序號}`）與預期實作位置
- **重要**：確認 Test ID 的功能 ID 前綴，確保實作與測試的對應關係正確

### Step 2：最小實作
- 一次專注一個失敗測試，依最小可行原則修改程式碼
- 執行測試確認轉為 Green，記錄修改的檔案與改動重點
- 在所有標記為綠燈的測試都通過前，不進入下一個測試

### Step 3：驗證品質與更新狀態

- 執行 Lint、型別檢查等必要的守門檢查
- 整理實作修改摘要、測試結果與檢查結果

**在 tdd-red 建立的同一 Comment 中追加 Green 階段結果**：
- 找到該 Test ID 對應的 Comment（由 tdd-red 首次建立，例如 `## Test: REQ-001-T-101 - US1-S1`）
- 在 Comment 中追加新的區段，格式為「### Green 階段 - [時戳]」
- 記錄以下內容：
  - Green 階段時戳與通過狀態（🟢）
  - 實作修改重點（檔案、改動摘要）
  - 測試通過的完整證據（測試輸出或截圖）
  - 品質檢查結果（Lint、Type Check 等）
  - 功能 ID 與 Test ID 的對應確認（例如 `功能 ID: REQ-001, Test ID: REQ-001-T-101`）
- 參考 Issue #5 的「追加更新格式」

**更新 TDD Issue 測試矩陣的狀態欄位**：
- 找到對應的 Test ID 行（例如 REQ-001-T-101）
- 將狀態欄位從 `⏳` (未開始) 或 `🔴` (已失敗) 更新為 `🟢 [查看](#comment-XXXX)` (已通過 + Comment 連結)
- 連結應指向同一個 Comment（由 tdd-red 建立）

## Comment Markdown 格式規範

更新 Comment 時，請遵循 `.github/ISSUE_TEMPLATE/comment-template.md` 中定義的 Markdown 格式標準。

## 產出格式

- **修改摘要表**：檔案名稱 | 修改重點 | 測試命令 | 結果
- **測試結果**：成功測試的輸出或截圖
- **質量檢查結果**：Lint、Type Check 等的執行結果

## 後續行動

完成 Green 階段後，如需程式碼優化或技術債處理，執行 `tdd-refactor.prompt.md`（可選，僅大型重構或品質改善時使用）。
