---
mode: agent
description: '【可選】在測試保持綠燈的前提下進行重構以改善程式碼品質（僅於大型重構或品質改善時執行）'
inputs:
  summary: 本 Prompt 指導可選的 Refactor 階段重構
  required:
    - 目標 TDD Issue 與測試矩陣（已轉綠的項目）
    - Green 階段的待辦事項或技術債清單
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 記錄重構內容、品質檢查結果與驗證證據
  include:
    - 重構項目與改善效果（含測試與品質檢查結果）
    - 未來技術債追蹤
---

# tdd-refactor

## 目的

【可選】在維持所有測試綠燈的前提下，進行大型重構或品質改善，提升程式碼結構與可維護性。

## 流程

### Step 1：確認基線
- 確認所有相關測試為綠燈（記錄證據）
- **重要**：確認重構涉及的 Test ID 格式為 `{功能ID}-T-{序號}`，並驗證功能 ID 對應關係正確
- 評估是否需要此階段（設計重構、技術債清理等）
- 如無重構需求，可跳過此步驟

### Step 2：執行重構
- 逐項進行重構改善，每次修改後重跑測試確保綠燈
- 記錄修改的檔案與改善目的
- 確保修改不影響其他功能（特別是有相同功能 ID 或相關 Test ID 的測試）

### Step 3：驗收與更新狀態

- 整理重構摘要、測試結果與品質檢查結果

**在 tdd-red 建立的同一 Comment 中追加 Refactor 階段結果**（可選）：
- 找到該 Test ID 對應的 Comment（由 tdd-red 首次建立，例如 `## Test: REQ-001-T-101 - US1-S1`）
- 在 Comment 中追加新的區段，格式為「### Refactor 階段 - [時戳]」
- 記錄以下內容：
  - Refactor 階段時戳與優化狀態（♻️）
  - 重構改善項目（檔案、改善目的）
  - 測試結果（應保持 🟢，確認無迴歸）
  - 品質檢查結果（Code Review、複雜度分析等）
  - 功能 ID 與 Test ID 的追蹤確認（例如 `功能 ID: REQ-001, Test ID: REQ-001-T-101`）
- 參考 Issue #5 的「追加更新格式」

**更新 TDD Issue 測試矩陣的狀態欄位**：
- 找到對應的 Test ID 行（例如 REQ-001-T-101）
- 將狀態欄位從 `⏳` (未開始) 或 `🟢` (已通過) 更新為 `♻️ [查看](#comment-XXXX)` (已優化 + Comment 連結)
- 連結應指向同一個 Comment（由 tdd-red 建立）

## Comment Markdown 格式規範

更新 Comment 時，請遵循 `.github/ISSUE_TEMPLATE/comment-template.md` 中定義的 Markdown 格式標準。

## 產出格式

- **重構摘要**：檔案 | 改善項目 | 理由 | 驗證結果
- **技術債追蹤**：未完成項目與建議處理時程

## 後續行動

完成後更新 TDD Issue 狀態。若重構期間測試失敗，回到 `tdd-green.prompt.md` 修正。
