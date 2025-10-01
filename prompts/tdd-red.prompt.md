---
mode: agent
description: 依據測試矩陣實作 Red 階段，撰寫會失敗的測試並記錄阻塞事項
inputs:
  summary: 以本 Prompt 產生 Red 階段的測試與紀錄
  required:
    - 目標 TDD Issue 編號與測試矩陣（含優先順序）
    - 對應的 BDD Scenario / SDD 契約重點
    - 測試框架與指令（例如 `pnpm test --filter ...`）
    - Mock、測試資料或外部依賴準備情況
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 建立失敗測試並更新 TDD Issue 的 Red 階段狀態
  include:
    - 新增或調整測試檔案的摘要（檔名、情境、預期失敗訊息）
    - 執行測試結果與錯誤截圖／重點輸出
    - 連續錯誤追蹤表（欄位：`Test ID`、`測試檔/名稱`、`錯誤摘要(前120字)`、`錯誤分類`、`連續次數`、`MCP 行動`、`來源/信賴等級`）
    - 阻塞清單與後續處置（含需要回填 BDD/SDD 的項目與對應 Issue `#編號`）
    - 提醒連續 3 次相同錯誤需透過 MCP 在 TDD Issue 留言（附留言範本）；若達 5 次，需透過 MCP 將標籤改為 `human_required` 並說明原因
    - 建議切換至 `tdd-green.prompt.md` 的條件
---

# tdd-red

## 目的

依照測試矩陣撰寫會失敗的測試，清楚記錄失敗原因、阻塞與需要補強的資料，為 Green 階段做準備。

## 流程

### Phase 0：準備（任務對齊）
1. 從 TDD Issue 取得本次要處理的測試項目與優先順序。
2. 確認測試框架與指令，必要時同步安裝或更新依賴。
3. 盤點已準備的 Mock / 資料，缺漏部分列入待辦；若缺口源於需求或契約不一致，立即註記需回到 `requirements-change` 或 `sdd`。

### Phase 1：執行（撰寫測試）
1. 針對選定項目新增或調整測試檔案，確保描述符合 Gherkin 情境。
2. 測試應刻意失敗（Red），並在註解說明期待的行為。
3. 執行測試並收集錯誤訊息、堆疊、快照差異等資訊。
4. 若測試未失敗，分析原因（可能漏寫斷言 / 已有實作），記錄於輸出並重新調整；若因需求落差造成測試無法建立或判斷失準，建議回到 `requirements-change`。

### Phase 2：記錄與交接（阻塞回報）
1. 將執行結果、失敗訊息、阻塞項目整理成段落或表格。
2. 若遇到資料或合約缺口，提醒回寫至 BDD / SDD Issue。
3. 為每個錯誤標記分類（`Test Failure`、`Build/Setup Failure`、`External Dependency`、`Spec Mismatch`），並依 README〈MCP 錯誤處理與回圈規則〉累計次數：相同測試錯誤連續 3 次 → 透過 MCP 留言；連續 5 次 → 將標籤改為 `human_required` 並說明是否需回到 `requirements-change` 或 `sdd`，留言需引用來源 Issue 與信賴等級。
4. 提醒更新 TDD Issue 中測試矩陣的狀態（保持 Red）。
5. 建議下一步進行 `tdd-green.prompt.md`，並帶上此輪測試的重點與阻塞。提醒在 TDD Issue 迭代進度中勾選 `Red` 核取框。

## 產出格式建議

- **測試摘要表**：檔案、情境、預期結果、實際錯誤、來源與信賴等級（遵循 README 規範）。
- **阻塞清單**：需補的資料 / 契約 / 權限、負責人、預計完成時間、對應 Issue `#編號`。
- **Issue 留言建議**：若觸發 3 次同錯誤，附上留言範本（必要欄位：錯誤摘要、嘗試步驟、來源、信賴等級、下一步）。

## 後續建議

- 完成 Red 後，立即執行 `tdd-green.prompt.md` 進行實作。
- 若阻塞涉及跨團隊，於輸出末尾提醒建立後續 Issue 或請求支援。
- 定期將測試檔路徑與情境摘要同步到 TDD Issue，並標註來源與信賴等級。
