---
mode: agent
description: 總覽 TDD 迭代，決定是否啟動 `tdd-requirements` → `tdd-testcases` → `tdd-red` → `tdd-green` → `tdd-refactor` → `tdd-verify` 的完整流程
inputs:
  summary: 透過本 Prompt 評估當前 TDD 迭代狀態並排定下一個子 Prompt
  required:
    - 目標 TDD Issue 編號與摘要
    - 最新的 BDD / SDD Issue 連結與狀態
    - 既有的 TDD 子階段成果（若有）
    - 阻塞、品質要求、時程或外部依賴
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 明確列出目前所處階段、缺口與下一個要執行的 TDD 子 Prompt
  include:
    - 子流程進度表（需求、測試、實作、重構、驗證的完成度與最後更新來源）
    - 推薦的下一步子 Prompt（含理由與所需輸入清單）
    - 若需多個步驟，排成建議順序並標示責任人
    - 阻塞與風險摘要（含連續錯誤統計與 MCP 處理需求），以及與 TDD Issue 的同步提醒
---

# tdd

## 目的

作為 TDD 迭代的「看板」，快速盤點需求、測試與實作的進度，並決定下一個要執行的子 Prompt（`tdd-requirements`、`tdd-testcases`、`tdd-red`、`tdd-green`、`tdd-refactor`、`tdd-verify`）。

## 流程

### Phase 0：狀態盤點
1. 讀取目標 TDD Issue 與對應的 BDD、SDD Issue，確認各自的最新留言與狀態。
2. 蒐集既有輸出：若已跑過子 Prompt，彙整重點與待辦，建立「子流程進度表」（建議欄位：階段、完成度、最後更新時間、來源連結）。
3. 標示任務幅度矩陣與外部依賴，確認是否有阻塞或等待同步的 PR／部署。

### Phase 1：完成度評估
1. **需求脈絡**：是否已透過 `tdd-requirements` 整理完成？若否，排入待辦並列缺口。
2. **測試矩陣**：是否涵蓋 BDD Scenario 與 SDD 契約？若不足或資料未備，排入 `tdd-testcases`。
3. **Red**：是否已有失敗測試並符合預期訊號？若缺少，排入 `tdd-red`。
4. **Green**：測試是否已轉綠？若仍失敗或阻塞，紀錄原因與連續錯誤次數。
5. **Refactor**：是否完成重構、文件更新與技術債清單？若無，排入 `tdd-refactor`。
6. **Verify**：是否執行總驗證與品質檢查？若尚未，列入 `tdd-verify`。
7. **回圈判斷**：若任何階段缺口源於需求/契約變動，提醒回到 `requirements-change` 或 `sdd`。

### Phase 2：行動建議
1. 依優先順序列出接下來要執行的子 Prompt：指明觸發條件、需要的輸入、期望輸出與負責人。
2. 如需批次執行，提醒使用 `scripts/tdd-cycle.sh` 並確認錯誤處理機制（MCP 留言、標籤更新）。
3. 若有阻塞或重複錯誤，提供「錯誤分類」（測試失敗、建置失敗、依賴缺失等）與紀錄規則，並提醒遵守連續錯誤 3 / 5 次的 MCP 處理。
4. 在輸出末尾整理需同步到 TDD Issue 的重點，包含：進度表更新、需留言事項、待建立的追加 Issue。

## 產出格式建議

- **現況摘要表**：需求、測試、實作、重構、驗證的完成度（✅/⚠️/❌）。
- **下一步清單**：序號、推薦子 Prompt、需要的輸入、預期產出。
- **阻塞與風險**：列出問題、影響、建議處置。
- **Issue 同步提醒**：哪些欄位需更新、是否要建立留言或新 Issue。

## 後續建議

- 若全流程皆需執行，可直接使用 `scripts/tdd-cycle.sh` 自動跑完各子 Prompt。
- 每完成一個子 Prompt，建議回到本指令再次檢視，確保沒有遺漏。
- 在輸出末尾提醒確認 TDD Issue 的測試矩陣與紀錄同步最新資訊。
