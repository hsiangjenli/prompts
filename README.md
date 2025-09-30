# GitHub Prompt 導覽

本目錄存放專案使用的各類 Prompt 與 Issue Template，協助 AI 與人類協作者在不同階段執行適當任務。說明預設採用繁體中文，並以 GitHub Issue 作為主要交付載體。

## Prompt 清單

| 檔名 | 目的 | 主要產出 | 推薦情境 |
| --- | --- | --- | --- |
| `tech-stack.prompt.md` | 盤點技術堆疊並補齊缺項，統一實作依據 | 技術清單（已定案 / 暫用 / 待決定）、後續待辦 | 專案初始、需要確認或更新技術決策時 |
| `index.prompt.md` | 入口導覽，依專案現況建議下一步 Prompt | 專案現況摘要、指令清單、EARS/GWT 缺口檢視 | 不確定該用哪個指令、需要流程總覽時 |
| `requirements.prompt.md` | 釐清需求來源並整理 EARS / GWT 雛形 | 需求摘要、情境清單、後續 BDD 問題集 | 需求尚未完整或需重新盤點時 |
| `requirements-change.prompt.md` | 針對既有需求變更，更新 EARS / GWT | 變更摘要、更新後的需求與驗收描述 | 已有文件但臨時調整或新增需求時 |
| `bdd.prompt.md` | 將需求轉為 Gherkin 驗收案例並建議後續 Issue | BDD Issue 內容（Scenario、驗收訊號、對應 SDD/TDD 編號） | 需求確認後，需要行為測試範例時 |
| `sdd.prompt.md` | 從 BDD 案例萃取介面 / 資料契約與合約測試 | SDD Issue 內容（契約對照表、Mock、版本策略） | 行為案例已定，需要規範契約或資料流時 |
| `tdd.prompt.md` | TDD 迭代總覽，決定下一個子 Prompt | 現況評估、子流程建議、阻塞清單 | 需要掌握 TDD 進度或排程下一步時 |

### TDD 子流程 Prompt

| 檔名 | 目的 | 主要內容 | 推薦情境 |
| --- | --- | --- | --- |
| `tdd-requirements.prompt.md` | 以 TDD 角度整理需求背景與邊界 | 功能概要、輸入輸出、限制條件、待補事項 | 初次建立 TDD Issue 或需求在迭代中有重大調整時 |
| `tdd-testcases.prompt.md` | 擴充測試矩陣，規劃 Red 階段測試 | 測試項目清單、資料/Mock 要求、優先順序 | 進入 Red 前需要確認測試覆蓋與資料準備 |
| `tdd-red.prompt.md` | 撰寫會失敗的測試並記錄阻塞 | 新增測試摘要、錯誤紀錄、MCP 留言/改標籤規則 | 需要建立失敗測試或更新測試矩陣中的 Red 項目時 |
| `tdd-green.prompt.md` | 最小實作讓測試轉綠並維持紀錄 | 修改摘要、測試結果、MCP 留言/改標籤規則 | Red 測試已備妥，準備進行實作轉綠 |
| `tdd-refactor.prompt.md` | 在綠燈狀態下重構與更新文件 | 重構內容、品質檢查、技術債追蹤 | Green 完成後需整理結構、補文件或還技術債時 |
| `tdd-verify.prompt.md` | 總驗證 TDD 迭代成果 | 測試/品質檢查結果、契約同步、結束或回圈判定 | 準備提交成果或判定是否需再迭代時 |

> Red / Green 階段若遇到同一錯誤連續 3 次，須透過 MCP 在 TDD Issue 留言；連續 5 次則需透過 MCP 將 Issue 標籤調整為 `human_required` 並說明原因。

## Issue Template 清單

| 檔名 | 用途 | 重點欄位 |
| --- | --- | --- |
| `bdd.md` | 建立 BDD 驗收場景 Issue | Gherkin 情境、驗收訊號、對應 SDD / TDD Issue 編號 |
| `sdd.md` | 記錄契約設計與合約測試計畫 | 契約對照表、Mock/樣本策略、版本與部署規劃 |
| `tdd.md` | 追蹤 TDD 測試與實作進度 | 測試矩陣、流程紀錄、風險應對、後續交付 |
| `bug_report.md` | 標準化缺陷回報，含嚴重度與相關 Scenario | 復現步驟、預期/實際行為、環境與風險、後續建議 |

## 執行流程概覽

```mermaid
flowchart TB
    PI[index
入口導覽] -->|新需求| PRQ[requirements
需求盤點]
    PI -->|需求已存在| PRC[requirements-change
需求變更]
    PRQ -->|需確認或大幅調整技術| TS[tech-stack
技術堆疊盤點]
    PRC -->|重大技術變更| TS
    PRQ --> PBDD[bdd
行為案例]
    TS --> PBDD
    PRC --> PBDD
    PBDD --> PSDD[sdd
契約設計]
    PSDD --> PTDD[tdd
迭代總覽]
    subgraph "TDD 子流程"
        PTDD --> TRQ[tdd-requirements]
        TRQ --> TTC[tdd-testcases]
        TTC --> TRD[tdd-red]
        TRD --> TGR[tdd-green]
        TGR --> TRF[tdd-refactor]
        TRF --> TVF[tdd-verify]
    end
    TVF -->|全部通過| GH[GitHub Issue / PR
交付]
    TVF -->|需補強| PTDD
    TVF -->|需求偏移| PRC
```

1. 先執行 `index` 盤點現況與缺口。若發現技術尚未定義或變更幅度大，再啟動 `tech-stack`。  
2. `requirements` 釐清新需求，若目標是調整既有需求則改用 `requirements-change`。  
3. BDD → SDD → TDD 逐步深化：先定義行為情境，再契約化介面/資料，最後規劃測試與實作。  
4. TDD 透過總覽 Prompt 與六個子流程反覆迭代；`tdd-verify` 若未通過會回到 `tdd`/`requirements-change` 依缺口補強。  
5. 驗證通過後，接續任務拆解或實作流程（建立 PR、同步程式碼）並更新 GitHub Issue / PR。

## 自動化腳本

| 腳本 | 說明 |
| --- | --- |
| `scripts/tdd-cycle.sh` | 依序觸發 `tdd-requirements → tdd-testcases → tdd-red → tdd-green → tdd-refactor → tdd-verify`，支援指定 Issue 編號、測試名稱與略過前置階段。請在執行過程遵守 MCP 留言與標籤調整規則。 |

### 異常情境處理

- `requirements` ↔ `requirements-change`：若 `docs/spec/` 或已存在的需求 Issue/PR 中有對應章節，視為「既有需求」。調整既有需求時改用 `requirements-change`，並於輸出註明引用的既有文件。
- BDD / SDD 迭代：若在 SDD 或 TDD 發現情境缺漏，先回到對應的 BDD Issue 更新，再同步影響的 SDD / TDD Issue。
- TDD 回圈：`tdd-verify` 未通過時，依檢查清單判定要回到 `tdd-red`（測試不足）、`tdd-green`（實作未完成）或 `tdd-refactor`（品質問題）。若根本需求改變，回到 `requirements-change`。
- 技術堆疊：發生重大技術調整、調整部署目標或跨專案共用時，再啟動 `tech-stack` 重新盤點並更新 README。

### Issue 關聯規範

- 所有 BDD / SDD / TDD Issue 需在描述欄內標示來源需求（Issue 編號或文件路徑）與信賴等級符號（🔵／🟡／🔴）。
- BDD Issue 的「關聯工作」欄必須填寫對應 SDD / TDD Issue 編號，格式建議 `#123 (SDD)`、`#124 (TDD)`；若尚未建立，請標記 `待建立` 並在輸出提供建議標題。
- SDD Issue 應列出所依賴的 BDD Scenario 標籤（例如 `BDD-001`）與預計更新的契約檔案路徑。
- TDD Issue 中的測試矩陣需同步列出對應 BDD Scenario 與 SDD 契約 ID，方便追蹤來源。
- 建議透過 MCP 操作 GitHub 時，一併更新彼此的 Issue 交互連結與狀態（例如使用 `linked pull requests`、`/link` 指令）。

### TDD 自動化腳本注意事項

- `scripts/tdd-cycle.sh` 執行失敗時，請查看 CLI 回傳碼：非零代表某個 Prompt 未完成，需手動檢查輸出並至少更新 TDD Issue 的阻塞欄位。
- 若 MCP 操作（留言、改標籤）失敗，腳本不會自動重試；請人工處理並在 Issue 中註明。
- 使用 `--skip-*` 參數時，請確保已手動完成對應階段並在 TDD Issue 中標記為 ✅，避免跳過必要步驟。
- 每輪執行結束後，確認 CLI 輸出含有「✅ TDD 全流程執行完畢」字樣；若無，視為未完成需重新檢查。

## 使用建議

- 每個 Prompt 末段皆會建議下一步操作，形成清楚的導覽。
- 新增 Prompt 或 Issue Template 時，請同步更新本 README、Mermaid 圖與 `index.prompt.md` 的推薦邏輯。
- 儘量使用 GitHub Issue 留存成果；若需輸出本地 Markdown，請在 Prompt 中明確指示檔案路徑。
- 使用 MCP 操作 GitHub（留言、變更標籤、建立 Issue 等）時，務必附上理由與對應的錯誤紀錄，維持追蹤性。
- 實作或撰寫腳本時，請回顧 `tech-stack.prompt.md`、`README.md`、`AGENTS.md` 技術堆疊章節，確保決策一致並即時更新。
