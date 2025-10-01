---
mode: agent
description: 依照 BDD 驗收場景模板整理 Gherkin 案例，銜接 requirements → BDD → SDD/TDD 流程
inputs:
  summary: 需先掌握需求要點與既有輸出，避免重複定義
  required:
    - 最新的需求彙整或 Issue（含需求編號、EARS 條列）
    - 既有 BDD Issue / Scenario ID 與狀態（若無請註記「首度建立」）
    - 相關文件或討論連結（設計稿、會議記錄、對話）
    - 目標時程、Milestone 與責任人
    - 已知的子系統範圍與限制（API、前端、AI、資料等）
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 產出符合 `.github/ISSUE_TEMPLATE/bdd.md` 的內容並安排下一步
  include:
    - 至少一個成功情境與一個例外情境的 Gherkin 案例（引用來源附 `#編號`，對待確認的情境標註 🟡／🔴）
    - 成功／失敗訊號、監控需求與所需測試資料
    - 任務幅度矩陣與相關子系統說明
    - Scenario ↔ SDD / TDD Issue 對照表（格式建議：`Scenario ID | SDD Issue | TDD Issue`，若尚未建立請填寫 `待建立` 並提供建議的 Issue 標題與標籤）
    - 提醒將結果透過 MCP 建立或更新 BDD Issue，並在欄位填寫對應的 SDD / TDD Issue 編號
---

# bdd

## 目的

在 `requirements.prompt.md` 理解需求後，協助快速定義可驗證的行為案例，以 BDD（Gherkin）格式輸出，為 SDD 與 TDD 做好前置準備。

## 執行前確認

- 取得最新的需求摘要（EARS + GWT）與任務幅度矩陣（可直接沿用 `requirements.prompt.md` 輸出）。
- 若專案已存在相關 BDD Issue，先彙整既有 Scenario ID、對應的 SDD / TDD Issue（使用 `#編號` 格式）與需更新的欄位，再帶著差異點進行訪談，避免重複定義。
- 回覆語言維持繁體中文。

## 提問準則

- 採單題 + 選項（最多 4 項）+「⑤ 其他（自由輸入）」的格式。
- 針對不同子系統（API、前端、資料、AI、Infra）分別確認情境與輸入輸出。
- 若資訊不足，應直接追問，不得自行杜撰。

## 流程

### Phase 0：準備（情境盤點）
1. **對齊需求重點**：摘要需求編號、核心目標、不可變限制，引用來源附 `#編號`，對尚未確認的資訊標註 🟡／🔴。
2. **盤點子系統**：列出涉及的模組，勾選 API、前端、資料、AI 等，若多項請分開處理。
3. **蒐集參考資料**：記錄可引用的設計稿、流程圖、舊 Issue（含既有 BDD Issue 如需更新）。

### Phase 1：執行（行為訪談）
1. **先定義 Happy Path**：針對主要使用者或服務路徑提問，確認輸入條件、觸發事件、預期結果。
2. **補齊例外 / 邊界情境**：詢問錯誤狀況、資料不齊、外部系統失敗等，確認補救流程。
3. **確認成功與失敗訊號**：包含監控指標、日誌、UI 狀態、回應碼。
4. **盤點測試資料需求**：若需樣本資料或模型，請列出來源與準備方式。

### Phase 2：記錄與交接（整理輸出）
1. **撰寫 Gherkin**：依 `.github/ISSUE_TEMPLATE/bdd.md` 填寫 Feature / Background / Scenario，至少一成功一失敗，必要時加入 Scenario Outline。每個 Scenario 生成唯一 ID（建議格式 `BDD-###`），供後續流程引用。
2. **標註來源與狀態**：每個 Scenario 附上來源（Issue `#編號` 或文件路徑）；對仍待確認的情境標註 🟡／🔴。
3. **整理驗收訊號與資料需求**：彙整成功／失敗訊號、監控指標與測試資料項目，包含誰提供、何時就緒。
4. **建立「Scenario 對照與交付清單」**：以單一表格列出 `Scenario ID | 需求 # | 對應 SDD Issue | 對應 TDD Issue | 備註 / 狀態`，並於備註欄標記「沿用」「更新」「待建立」。若需新增 Issue，於此表直接提供建議標題與標籤。
5. **列出待確認事項與後續行動**：以項目清單列出開放問題、負責人與預計完成時間，同時提醒依 `_issue-ops-guide.md` 建立或更新 BDD Issue、回填 Scenario 對照表與相關連結。

### 交付給 SDD / TDD 的重點
- 「Scenario 對照與交付清單」可直接提供 SDD、TDD 參考，避免重複列示。
- 每個情境的觸發條件、輸入輸出與驗收訊號應完整列在上述表格或相鄰段落。
- 對於標註 🟡／🔴 的情境或資料，確認責任人與下一步後再通知下游。

## 產出要求

- Markdown 區塊應可直接貼入 Issue，不得遺漏表格或勾選項目。
- Scenario 與需求編號需建立對照表。
- 若更新既有 Scenario，需標註原 Scenario ID 與對應 Issue `#編號`，並描述差異摘要。
- 若有跨子系統情境，需清楚標示影響範圍與責任人。
- 在回覆末段提醒使用者檢視並確認新建或更新的 BDD Issue，以及對應的 SDD / TDD Issue 編號與狀態。

## 後續建議

- 完成 BDD Issue 後，立即啟動 `sdd.prompt.md` 將案例轉為契約。
- 若需求再次變動，先更新 BDD Issue 再通知 SDD / TDD 負責人。
- 將所有關鍵連結（需求、設計、BDD、SDD、TDD Issue）整理於輸出摘要，方便後續追蹤。

## Issue 操作提醒

依 `_issue-ops-guide.md` 的「BDD」指引建立或更新 Issue：貼上 Gherkin、Scenario 對照表與開放問題，更新關聯的 SDD / TDD Issue（互相引用），並於評論標註待確認情境（🟡／🔴）。
