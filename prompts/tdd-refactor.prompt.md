---
mode: agent  
description: 在測試保持綠燈的前提下進行重構以改善程式碼品質  
inputs:
  summary: 本 Prompt 指導可選的 Refactor 階段重構  
  required:
    - 目標 TDD Issue 與測試矩陣
    - Green 階段的待辦事項或技術債清單  
outputs:
  summary: 記錄重構內容、品質檢查結果與驗證證據  
  include:
    - 重構項目與改善效果
    - 未來技術債追蹤  
---

# tdd-refactor

## 目的

【可選】在維持所有測試綠燈的前提下，進行大型重構或品質改善，提升程式碼結構與可維護性。

## 流程

### Step 1：確認基線
- 確認所有相關測試為綠燈（記錄證據）
- **重要**：確認重構涉及的 Test ID 與功能 ID 對應關係正確，並符合模板或專案規範
- 評估是否需要此階段（設計重構、技術債清理等）
- 如無重構需求，可跳過此步驟

### Step 2：執行重構
- 逐項進行重構改善，每次修改後重跑測試確保綠燈
- 記錄修改的檔案與改善目的
- 確保修改不影響其他功能（特別是有相同功能 ID 或相關 Test ID 的測試）

### Step 3：驗收與更新狀態

- 整理重構摘要、測試結果與品質檢查結果

**在 tdd-red 建立的同一 Comment 中追加 Refactor 階段結果**（可選）：
- 找到該 Test ID 對應的 Comment（由 tdd-red 首次建立，標題格式為 `## Test: [Test ID] - [Scenario ID]`，Scenario ID 格式依當前 BDD 規範）
- 在 Comment 中追加新的區段，格式為「### Refactor 階段 - [時戳]」
- 記錄以下內容：
  - Refactor 階段時戳與優化狀態（依模板或專案慣例使用的符號/說明）
  - 重構改善項目（檔案、改善目的）
  - 測試結果（應保持通過狀態，確認無迴歸）
  - 品質檢查結果（Code Review、複雜度分析等）
  - 功能 ID 與 Test ID 的追蹤確認（依模板或專案既有指南呈現）
- 參考 Issue #5 的「追加更新格式」

**更新 TDD Issue 測試矩陣的狀態欄位**：
- 找到對應的 Test ID 行
- 將狀態欄位從原本的狀態更新為對應的重構完成狀態，並依需求加入 Comment 連結
- 連結應指向同一個 Comment（由 tdd-red 建立）

## Comment Markdown 格式規範

更新 Comment 時，請遵循目前 comment 模板中定義的 Markdown 格式標準。

## 產出格式

- **重構摘要**：檔案 | 改善項目 | 理由 | 驗證結果
- **技術債追蹤**：未完成項目與建議處理時程

## 後續行動

完成後更新 TDD Issue 狀態。若重構期間測試失敗，回到 `tdd-green.prompt.md` 修正。
