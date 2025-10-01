---
mode: agent
description: 根據 TDD 需求與 BDD 情境篩選、撰寫測試案例，建立 Red 階段的待辦清單
inputs:
  summary: 以本 Prompt 蒐集並整理測試案例所需的條件與資料
  required:
    - 最新的 TDD Issue 背景（或 `tdd-requirements` 輸出）
    - 對應的 BDD Scenario 與 SDD 契約摘要
    - 可用的測試資料來源、Mock 或樣本檔
    - 既有測試框架與 CI/CD 限制
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 形成 `.github/ISSUE_TEMPLATE/tdd.md`「測試計畫」欄位的初稿
  include:
    - 測試項目清單（欄位固定為：`Test ID`、`Scenario ID (BDD-###)`、`需求 #`、`測試類型`、`資料/Mock`、`狀態`、`優先順序`、`備註`，每列附來源與信賴等級）
    - 必要的測試資料、環境與依賴說明（使用 `#編號` 或檔案路徑標示來源）
    - 評估優先順序與 Red → Green → Refactor 預期路徑
    - 待補資料與轉交 `tdd-red.prompt.md` 的重點，若因需求不完整無法前進，提醒回到 `requirements-change`
    - 提醒同步更新 TDD Issue 及相關 TODO（勾選對應欄位並標示信賴等級）
---

# tdd-testcases

## 目的

結合 BDD 情境與 SDD 契約，挑選並撰寫要在 Red 階段實作的測試案例，讓後續實作有清楚的測試矩陣與資料準備。

## 流程

### Phase 0：準備（情境確認）
1. 快速重點複習 TDD Issue 的「來源與範圍」。
2. 列出需涵蓋的 Gherkin 情境與對應契約，標示信賴等級與來源 `#編號`。
3. 確認測試框架、格式（如 Vitest、Jest、Playwright、pytest 等）。

### Phase 1：執行（測試設計）
1. 針對每個情境詢問：測試類型（單元 / 元件 / 整合 / E2E）、預期驗證點、必要輸入輸出。
2. 盤點測試資料、Mock、外部服務依賴，並標示取得方式。
3. 釐清效能 / 資安 / 模型等特殊測試需求。
4. 為每個測試項目設定初始狀態（預期 Red）。

### Phase 2：記錄與交接（輸出整合）
1. 建立測試矩陣：欄位需包含 `Test ID`、`Scenario ID (BDD-###)`、`需求 #編號`、`測試類型`、`資料/Mock`、`狀態`（預設 Red）、`優先順序`、`備註`。
2. 梳理 Red → Green → Refactor 的實作順序與里程碑，必要時標記 `Blocked` 原因（資料缺、契約待定等）。
3. 列出待補的測試資料或環境設定，指派負責人或下一步。若出現下列情況請回到相應流程：
   - 欄位、介面定義或合約版本不清楚 → 回到 `sdd.prompt.md` 更新契約。
   - 合約完整但缺樣本資料、Mock 或測試環境 → 留在本階段補資料。
   - 需求描述與 BDD / SDD 明顯矛盾 → 參考 README 的「需求變更觸發檢查表」，回到 `requirements-change.prompt.md`。
4. 建議下一步執行 `tdd-red.prompt.md`，並帶入重點測試項目。
5. 提醒更新 TDD Issue、TODO 或任務資料庫，並勾選 Issue「迭代進度」中的 `Testcases` 核取框。

## 產出格式建議

- **測試矩陣表格**：使用標題 `Test ID | Scenario ID (BDD-###) | 需求 # | 測試類型 | 資料/Mock | 狀態 | 優先順序 | 備註 | 信賴等級/來源`，可直接貼入 TDD Issue。
- **資料 / Mock 清單**：標示檔案位置、建立方式、責任人，並依 README 規範附上來源與信賴等級。
- **依賴與風險**：列出外部服務、守門檢查、回滾策略。
- **後續動作**：明確寫出下一步必須執行 `tdd-red.prompt.md`。

## 後續建議

- Red 階段前，確認測試檔案與案例命名規則。
- 若測試案例有爭議，回到 BDD / SDD Issue 取得共識。
- 在輸出末尾提醒使用者或 AI 建立／更新 TDD Issue 的測試區塊，並為每列標註來源與信賴等級。
