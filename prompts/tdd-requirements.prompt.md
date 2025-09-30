---
mode: agent
description: 釐清指定任務的 TDD 需求背景，為後續測試與實作整理完整脈絡
inputs:
  summary: 透過本 Prompt 蒐集需求來源並定義 TDD 工作邊界
  required:
    - 目標 TDD Issue（或草稿）的編號與標題
    - 對應的需求 / BDD / SDD Issue 連結（使用 `#編號` 格式）
    - 可用的設計或程式文件路徑（architecture.md、interfaces.ts 等）
    - 已知的技術限制、CI/CD 約束與時程目標
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 匯整 `tdd.md` 模板「來源與範圍」所需資訊，並列出後續要補的細節
  include:
    - 功能概要、主要使用者與價值（EARS / 使用者故事交叉驗證）
    - 輸入輸出、非功能限制、預期情境與信賴等級
    - BDD Scenario ↔ TDD 驗證項目的映射草稿（Scenario ID、對應測試範圍）
    - 建議寫入 TDD Issue 的欄位（可直接貼上）
    - 待確認事項與建議切換到 `tdd-testcases.prompt.md` 的條件
---

# tdd-requirements

## 目的

在進入測試案例與實作前，先用 TDD 角度整理需求背景、輸入輸出與限制條件，確保後續步驟有完整脈絡可用於 `TDD 測試與實作計畫` Issue。

## 流程

### Phase 0：蒐集基礎資訊
1. 確認目標 TDD Issue 編號（使用 `#編號` 記錄），並讀取其 BDD / SDD 來源；列出涉及的 Scenario ID 與契約版本。
2. 盤點相關文件：需求摘要、設計文件、既有程式或測試、系統規範。
3. 標註任務幅度矩陣：API、前端、資料處理、AI/ML、排程、資料庫、監控等。

### Phase 1：需求與限制盤點
1. 使用選項＋自由輸入詢問：主要使用者、目標場景、關鍵價值。
2. 整理輸入輸出資訊，標註型別、來源、範圍與信賴等級。
3. 盤點非功能限制：效能、安全、相容性、部署、資料品質、模型限制等。
4. 彙整成功／失敗訊號與監控需求（從 BDD 或需求文件交叉檢查），並標記對應 Scenario ID。

### Phase 2：輸出整理
1. 匯整成段落與表格，對應 `.github/ISSUE_TEMPLATE/tdd.md` 的「來源與範圍」欄位，並加入「Scenario ↔ 測試項目」初步對照表。
2. 建立「待補資訊」清單（未確認的欄位、缺少的文件、外部依賴）。
3. 建議下一步執行 `tdd-testcases.prompt.md`，並將重點問題帶入下一階段。
4. 提醒透過 MCP 或人工方式更新 TDD Issue，確保欄位資訊同步。

## 產出格式建議

- **功能概要**：可直接貼到 Issue。
- **輸入 / 輸出對照表**：欄位、型別、來源、信賴等級。
- **限制條件列表**：分別列出效能、安全、資料、部署等。
- **情境摘要**：概述主要情境與邊界情境，供測試案例延伸。
- **Scenario 對照表**：`BDD-###`、預期測試範圍、對應 Issue `#編號`。
- **待確認事項**：列點，方便後續追蹤。

## 後續建議

- 完成需求整理後，切換至 `tdd-testcases.prompt.md` 擴充測試案例。
- 若需求變更，記得回寫到對應的 BDD / SDD Issue 並重跑本 Prompt。
- 若發現某 Scenario 缺少對應測試項目，先回到 `bdd.prompt.md` 或 `requirements-change.prompt.md` 補齊行為後再繼續。
- 在輸出末尾提醒使用者確認 TDD Issue 已更新最新背景資訊。
