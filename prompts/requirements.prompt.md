---
mode: agent
description: 先理解需求並整理 EARS / GWT 雛形，為後續 BDD→SDD→TDD Prompt 提供完整輸入
inputs:
  summary: 使用本 Prompt 釐清需求來源與限制
  required:
    - 使用者提供的需求概要與最終目標（對話、文件或 Issue）
    - 目前可用的專案文件清單與路徑（例如 docs/spec/, README.md, CLAUDE.md）
    - 相關 GitHub Issue 編號與重點留言、必要的 changelog 摘要
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
    - 必須遵循的時程、技術或 CI/CD 限制
outputs:
  summary: 整理可追蹤的需求摘要，並準備執行 BDD Prompt 所需的材料
  include:
    - EARS 記法整理的功能需求（引用既有資料附 `#編號`，對尚未確認的內容標註 🟡／🔴）
    - 初步的 GWT 驗收草稿與 BDD 情境清單（供下一步擴充）
    - 任務幅度與涉及子系統（前端／後端／AI／資料處理等）的界線說明
    - 推薦建立或更新的 GitHub Issue（需求摘要），以及建議後續交給 `bdd.prompt.md` 的重點題目
    - 提醒將輸出留存於 GitHub Issue，並標註下一個將執行的 Prompt（預設為 `bdd.prompt.md`）
---

# requirements

## 目的

以 EARS 記法整理需求並補齊 GWT 驗收描述，同時對齊 BDD → SDD → TDD 的實作循環。適用於 API、前／後端、資料處理、AI/ML、排程、資料庫與混合型任務，協助快速掌握任務幅度與跨子系統的影響。

## 提問原則

- 採「單一問題 + 選項 + 自訂輸入」格式，便於使用者快速回覆。
- 每題最多 4 個選項，預留「⑤ 其他／自行輸入」。
- 必要時加入範例或預設值（例如：「預設語言：繁體中文」）。
- 優先確認是否已有對應 GitHub Issue／模板；若無，詢問是否需要建立。

**問題範例**
```
目前主要聚焦的領域是？
① API / 後端服務
② 前端或 UI 互動
③ 資料處理 / ETL / ML 訓練
④ 排程 / Lambda / 基礎設施
⑤ 其他（請說明）
```

## 操作流程

在任何階段若資訊尚未確認，請以 🟡（推測）或 🔴（待確認）標註；引用既有文件或 Issue 時附上 `#編號` 即可。若發現 `docs/spec/`、GitHub Issue 或 PR 中已有正式需求文件，請評估是否應改用 `requirements-change.prompt.md` 並向使用者確認；若仍選擇使用本 Prompt，需在輸出中記錄「建立新基準」的理由。

### Phase 0：準備（盤點現況 / Discovery）

**目標**：掌握既有資產、任務幅度與限制條件。

1. **掌握上下文**：讀取 `CLAUDE.md`、`AGENTS.md`、`README.md`、`CHANGELOG.md` 或同等文件，列出近期變更。確認是否已有 `*-requirements.md` 或相關 Issue 作為基準，若有需註記來源並提醒後續如涉及變更應改用 `requirements-change`。
2. **收斂規範**：檢視 `docs/rule/`、`docs/rule/kairo/requirements/` 或其他專案規範。
3. **資產清查**：使用 `rg --files` 盤點 `docs/spec/`、`docs/design/`、`commands/`、`notebooks/` 等，標示曾產出的規格、模型、流程圖。
4. **GitHub 連結**：透過 MCP 取得相關 Issue／PR，整理標題、狀態、指派人。
5. **任務幅度矩陣**：紀錄涉及的子系統（✅ 或 ☐）：API、前端、資料管線、模型訓練／推論、排程／Lambda、資料庫／倉儲、基礎設施／觀測、其他。
6. **約束條件**：確認時程、預算、技術棧、CI/CD、法規、資料治理等限制。

若任何項目資料不足，直接提出多選問題要求補充。

### Phase 1：執行（需求訪談 / Interview）

**目標**：釐清缺口、範圍與優先順序。

1. **定位情境**：根據 Phase 0 的矩陣，先確認本次焦點（API / 前端 / Data / AI / Infra 等），使用選項題請使用者選擇，可複選。
2. **EARS 缺口盤點**：以表格列出「已知需求／缺口／狀態」，逐項詢問：
   - 「此功能是否已有確認來源？」（選項：① 已確認、② 推測（標註 🟡）、③ 未提及（標註 🔴）、④ 移除、⑤ 其他）
3. **GWT 情境補強**：針對尚未覆蓋的 Gherkin 場景，詢問觸發條件、關鍵指標與成功／失敗訊號。
4. **非功能／跨領域議題**：對效能、安全、成本、資料品質、模型評估、可維運性等提出選項題確認。
5. **範圍與優先順序**：使用 MoSCoW 選項題或時間盒問題確認 Must/Should/Could/Won't 以及目標里程碑。

所有訪談輸入需即時轉記錄，並於表格中標示來源或狀態（🟡／🔴）。

### Phase 2：記錄與交接（對齊 BDD → SDD → TDD / Alignment）

**目標**：把人話需求對應到可執行的測試、契約與實作計畫。

1. **BDD（行為驅動）**
   - 依主題整理使用者故事（WHO/WHAT/WHY），先列出候選情境與重要步驟。
   - 若涵蓋多子系統，將候選情境分組（例如：API 呼叫、前端互動、批次流程、模型推論、監控警示）。
   - 為每個候選情境標示來源 `#編號` 或狀態（🟡／🔴），未定義者列入開放問題，供 `bdd.prompt.md` 深挖。
2. **SDD（規格驅動）**
   - 根據上述候選情境，紀錄可能涉及的介面或資料契約（OpenAPI／AsyncAPI／GraphQL／gRPC、事件 schema、資料表／檔案格式、特徵與模型版本、UI 元件協定、排程設定、監控 KPI 等）。
   - 為每項契約先草擬需釐清的重點（版本策略、mock／樣本、合約驗證方式），交由 `sdd.prompt.md` 詳化。
   - 若屬「資料／契約優先」工作，可在此階段記下需優先處理的契約，再提醒後續 BDD 需補齊行為。
   - 若出現新的框架、部署或第三方服務，記錄需安排 `tech-stack.prompt.md` 重新盤點的原因與預計時程。
3. **TDD（測試驅動）**
   - 先列出必須驗證的測試類型（單元／元件／資料處理／模型驗證等）與可能的 Red → Green → Refactor 步驟。
   - 紀錄可能需要的環境、資料集與檢查項目（schema 驗證、模型表現、資安掃描），交由 `tdd.prompt.md` 詳細展開。
   - 將候選測試與前述 BDD 情境、SDD 契約建立初步映射，供後續細化。
4. **Issue 輸出與同步**
   - 優先採用 `.github/ISSUE_TEMPLATE/` 下的模板（例如 `bdd`, `sdd`, `tdd`, `userstory`, `spec`, `gwt`, `datascience`, `frontend`）。
   - 若無合適模板，使用暫用格式（標題、任務幅度、EARS、Gherkin、契約需求、TDD 計畫、狀態欄位、下一步），並在輸出中建議新增正式模板。
   - 透過 MCP 建立或更新 Issue，附上檔案連結、mock、資料樣本或模型位置。
5. **本地補充（選擇性）**
   - 若使用者要求保留於 `docs/spec/` 等路徑，可同步更新並標註此次變更，但以 GitHub Issue 為追蹤主體。

## 產出清單

- 需求摘要：任務幅度、EARS 條列、待決議題與狀態（🟡／🔴）。
- BDD 準備：待確認的情境清單、預期問題、需補的資料來源。
- SDD 預期：可能涉及的契約或資料資產（僅列出重點，詳細留給 SDD Prompt）。
- TDD 提醒：後續需要驗證的重點風險與測試類型。
- 後續指令建議：依序建議執行 `bdd.prompt.md` → `sdd.prompt.md` → `tdd-checkpoint.prompt.md`（或 `tdd-requirements.prompt.md` 視情境而定），並視需求再啟動 `requirements-change.prompt.md` 或其他任務拆解 / 實作流程。
- GitHub Issue 更新：提醒由 AI（透過 MCP）或使用者將輸出附加於相關 Issue，以利追蹤。
- 引用 GitHub Issue 時使用 `#編號`，對於推測或待確認的資訊標註 🟡／🔴。

## 後續行動

- 依產出清單建立或更新 GitHub Issue，確認指派與時程。
- 若進入設計或實作階段，建議自行選擇合適的任務拆解或實作模板（例如專案自訂腳本 / Issue 流程）。
- 維護訪談紀錄、狀態（🟡／🔴）、任務幅度矩陣，以便日後需求變更時快速回溯。

## Issue 操作提醒

依 `_issue-ops-guide.md` 的「需求盤點」指引建立或更新 Issue：貼上 EARS／GWT 摘要、紀錄待確認事項（🟡／🔴），並於評論標註下一個將執行的 Prompt（預設 `bdd.prompt.md`）。
