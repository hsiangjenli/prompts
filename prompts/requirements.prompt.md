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

若過程中資料不足，請以 🟡（推測）或 🔴（待確認）標註；引用既有文件或 Issue 時附上 `#編號`。發現已存在正式需求基準時，先判斷是否應改用 `requirements-change.prompt.md`，並在輸出說明若仍需重建基準的原因。

### Step 1：盤點基準
1. 閱讀 `README.md`、`CHANGELOG.md`、`docs/spec/` 或等效文件，記錄近期變更與既有交付物。
2. 檢查是否已有 `*-requirements.md`、需求 Issue 或 PR；若有，註記來源與狀態。
3. 建立「任務幅度矩陣」：API、前端、資料處理、AI/ML、排程/Infra 等欄位，標示 ✅（已涉及）或 ☐（未涉及）。
4. 整理已知限制：時程、技術堆疊、CI/CD、法規、資料治理等。

### Step 2：補齊需求
1. 針對幅度矩陣中標記為 ✅ 的領域提出封閉式問題，蒐集 EARS 所需資訊（事件、條件、應達成的行為）。
2. 針對每個需求條目確認資料來源：① 已確認（🔵）、② 推測（🟡）、③ 待確認（🔴）。
3. 為每個重要情境整理初步 GWT（Given/When/Then）草稿：觸發條件、行為、期望結果、失敗指標。
4. 補齊非功能需求（效能、可靠度、資安、可維運性）與跨系統依賴。
5. 紀錄疑似需要 `tech-stack.prompt.md` 的情境（例如：新增雲服務、改用不同框架）。

### Step 3：整理輸出
1. **EARS 清單**：以表格呈現 `編號 | 敘述 | 來源 # | 狀態 (🔵/🟡/🔴)`。
2. **GWT 草稿**：列出主要驗收情境（可採 Markdown 表格或區塊），並註記與 EARS 編號的對應關係。
3. **Scenario 種子**：產出候選 Scenario ID（暫以 `BDD-SEED-###` 或需求編號對應），說明觸發條件與預期驗收訊號，供 `bdd.prompt.md` 深化。
4. **後續連結**：指出每個 Scenario 建議交給哪個 BDD/SDD/TDD Issue，若尚未存在請提供建議標題與標籤。
5. **開放問題 & 風險**：列出尚未確認的需求、資料、技術議題與責任人／預計解決時間。
6. **下一步建議**：預設推薦執行 `bdd.prompt.md`；若需求牽涉新技術或既有需求須同步，提醒使用 `tech-stack.prompt.md` 或 `requirements-change.prompt.md`。
7. **Issue 同步**：提示使用 `_issue-ops-guide.md` 中的需求盤點流程，將輸出貼入對應 Issue 或建立新 Issue。

## 產出清單

- EARS 表格（含來源與狀態標示）。
- GWT 草稿與 Scenario 種子清單。
- 任務幅度矩陣與關鍵約束摘要。
- 建議後續 Prompt 與待建立／更新的 Issue 清單。
- 開放問題與責任人／時程。

## 後續行動

- 依清單建立或更新 GitHub Issue，確認指派與截止時間。
- 若需同步至本地檔案（例如 `docs/spec/`），請註明與 Issue 的對應關係。
- 在輸出末尾再次提醒下一個預計執行的 Prompt（預設 `bdd.prompt.md`），以及需追蹤的 🟡／🔴 項目。

## Issue 操作提醒

依 `_issue-ops-guide.md` 的「需求盤點」指引建立或更新 Issue：貼上 EARS／GWT 摘要、紀錄待確認事項（🟡／🔴），並於評論標註下一個將執行的 Prompt（預設 `bdd.prompt.md`）。
