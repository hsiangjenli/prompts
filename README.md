# GitHub Prompt 導覽

本目錄存放專案使用的各類 Prompt 與 Issue Template，協助 AI 與人類協作者在不同階段執行適當任務。說明預設採用繁體中文，並以 GitHub Issue 作為主要交付載體。

## BDD？SDD？TDD？

- BDD（Behavior-Driven Development，行為驅動開發）
    - 定義：以業務行為與使用者能觀察到的「行為/情境」為中心，使用 Gherkin 等格式把需求轉成可驗收的情境（Scenario）。
    - 核心關注：驗收標準、使用者意圖、端到端行為。
    - 主要產出：Gherkin Scenario、驗收條件、驗收訊號（acceptance criteria）。
- SDD（Specification/Service Design Document，或稱契約設計）
    - 定義：將 BDD 的行為細化為系統邊界、介面契約、資料格式與 Mock 策略，形成可由開發與測試直接實作與模擬的契約。
    - 核心關注：介面契約（Request/Response）、資料契約、Mock 策略、版本與相容性。
    - 主要產出：契約對照表、Mock 規範、版本策略、樣本資料。
- TDD（Test-Driven Development，測試驅動開發）
    - 定義：由單元或整合測試驅動實作，先寫測試（Red），使測試通過（Green），再重構（Refactor）。TDD 著重程式內部行為的正確性與設計品質。
    - 核心關注：可測試性、最小實作、回歸保護、內部設計。
    - 主要產出：測試矩陣、失敗測試（Red）、最小實作與對應綠燈（Green）、重構提交。

### 使用情境

- 用 BDD 當你需要把需求轉成可驗收的行為並與非技術利害關係人（PM、產品、客服）對齊。適用於：新功能定義、驗收標準不清、跨團隊合約驗證。輸出直接作為 QA / acceptance 測試與 SDD 的輸入。
- 用 SDD 當行為確定但系統間介面尚未契約化時。適用於：API 設計、資料交換、Mock 資料需求、版本管理與相容性風險。輸出是 TDD 與整合測試所依賴的契約與 Mock。
- 用 TDD 當你要保證內部實作的品質與可回歸性，或要在 CI 中持續驗證小範圍的功能。適用於：演算法實作、邊界條件驗證、快速迭代需要高信賴的模組。

### 為何採用 BDD → SDD → TDD 的順序？

1. 從外到內、由行為到實作：BDD 強調「我們要系統做什麼（外部可觀察行為）」，先對使用者可見行為達成共識可避免下游開發做出與期待不符的實作。
2. SDD 界定邊界：在行為已被驗收條件定義後，SDD 把行為轉換為明確的介面與資料契約，減少不同系統或不同團隊間的語意差異，為測試與實作提供明確的依據。
3. 測試驅動的實作更可靠：有了明確行為（BDD）與契約（SDD）後，TDD 可以在更穩定的前提下聚焦於最小實作、邏輯正確性與設計品質；這樣能降低反覆返工與模糊需求造成的測試/實作浪費。

> 總結一句話：BDD 定義「要驗收的行為」，SDD 定義「系統間如何一致地交換/實現這些行為」，TDD 則在此契約下驗證與實作內部邏輯。

### 三個實務注意事項與回圈策略

1. 可追溯性優先：所有 TDD 測試或 SDD 契約都應該追溯回對應的 BDD Scenario（在 Issue 或檔案中以 `#編號` 或 Scenario ID 標示）。當測試頻繁失敗或契約需變更時，優先確認是否為 BDD 層面的需求變動，避免單純修補測試導致規格漂移。
2. 小步快走與最小變更：在 TDD 階段採用最小可行實作（minimum change）原則；若發現需求或行為不合理，先在 `tdd-checkpoint` 或 TDD Issue 記錄疑慮並回到 SDD（契約調整）或 BDD（行為/驗收條件調整），而非直接在實作層面做大改。
3. 明確回圈觸發條件與標籤化流程：定義何時回圈以及由誰決策，例如：
     - 若驗收條件（BDD）改動 → 直接回到 `requirements-change` / `bdd` 啟動變更流；
     - 若介面或資料契約改動（SDD）但行為不變 → 回到 `sdd` 並同步 TDD Issue；
     - 若單元/整合測試連續失敗（例如同一錯誤重複 3 次）→ 在 TDD Issue 留言並標註阻塞；連續 5 次則標籤 `human_required`（依本 Repo 規則）。

## Prompt 對照

### 核心流程

| 檔名 | 前置條件 | 主要輸出 | 常見下一步 |
| --- | --- | --- | --- |
| `tech-stack.prompt.md` | 技術堆疊尚未確認，或需求變更牽涉新技術 | 技術決策清單（已定案／暫用／待決定）與待辦 | 回到 `requirements` 或 `requirements-change` 更新需求背景 |
| `index.prompt.md` | 不確定目前所處階段，需要流程導航 | 專案現況摘要、缺口檢查、推薦 Prompt 清單 | 依決策表執行 `requirements` / `requirements-change` / `bdd` 等 |
| `requirements.prompt.md` | 尚無正式需求基準，或需要重新盤點 | EARS 條列、GWT 草稿、任務幅度矩陣 | 依輸出建議執行 `bdd.prompt.md` |
| `requirements-change.prompt.md` | 已有需求文件，需要新增或調整內容 | 更新後的 EARS / GWT、影響分析、同步清單 | 視影響決定更新既有 Issue 或啟動 `bdd` / `sdd` / `tdd` |
| `bdd.prompt.md` | 需求基準已確認，需轉為行為案例 | Gherkin Scenario、Scenario 對照與驗收訊號 | 將對照表交給 `sdd.prompt.md` |
| `sdd.prompt.md` | Scenario 已確認，需定義契約與 mock | 契約對照表、Mock/樣本策略、版本摘要 | 通知 `tdd-requirements.prompt.md` 接手 |
| `tdd-checkpoint.prompt.md` | 已執行 `tdd-requirements` 或任一 TDD 子流程 | 子流程進度表、阻塞整理、建議下一步子 Prompt | 依建議執行 `tdd-testcases` / `tdd-red` / `tdd-verify` 等 |
| `commit-message.prompt.md` | 目前位於 `tdd-*` 分支，某 TDD 階段已完成 | 部分暫存建議、Angular 風格 commit 草稿、後續提醒 | 推薦回到 `tdd-checkpoint` 更新進度 |

### TDD 子流程

| 檔名 | 前置條件 | 主要輸出 | 常見下一步 |
| --- | --- | --- | --- |
| `tdd-requirements.prompt.md` | BDD / SDD 資訊已備齊，尚未建立 TDD Issue | 功能摘要、輸入輸出、假設與限制、初始風險 | 將輸出附在 TDD Issue，按清單執行 `tdd-testcases` |
| `tdd-testcases.prompt.md` | 已完成 `tdd-requirements`，需要規劃測試 | 標準化測試矩陣、資料/Mock 需求、優先順序 | 將重點測試帶到 `tdd-red.prompt.md` |
| `tdd-red.prompt.md` | 測試矩陣已備妥，開始撰寫失敗測試 | 新增或更新 Red 測試、阻塞與錯誤追蹤 | 依結果決定繼續 Red 或進入 `tdd-green` |
| `tdd-green.prompt.md` | Red 測試已建立，準備最小實作 | 實作摘要、測試結果、連續錯誤記錄 | 若測試轉綠則交給 `tdd-refactor`；否則保持 Red 狀態 |
| `tdd-refactor.prompt.md` | 指定測試已轉綠，需整理品質 | 重構項目、品質檢查結果、技術債清單 | 完成後回報給 `tdd-verify` 或 `tdd-checkpoint` |
| `tdd-verify.prompt.md` | Red/Green/Refactor 輸出完整，需結案判定 | 驗證清單、契約同步、回圈或結案決策 | 若未通過，將決策寫回 `tdd-checkpoint` 或 `requirements-change` |

> **TDD 基本順序**：`tdd-requirements` → `tdd-testcases` → `tdd-red` → `tdd-green` → `tdd-refactor` → `tdd-verify`。僅在上述任一階段完成後，使用 `tdd-checkpoint.prompt.md` 盤點進度或整理阻塞；啟動 TDD 時請直接從 `tdd-requirements` 開始。

> Red / Green 階段若遇到同一錯誤連續 3 次，須透過 MCP 在 TDD Issue 留言；連續 5 次則需透過 MCP 將 Issue 標籤調整為 `human_required` 並說明原因。所有 Issue 連結一律使用 `#編號` 格式（例如 `#123`）。

## Issue Template 清單

| 檔名 | 用途 | 重點欄位 |
| --- | --- | --- |
| `bdd.md` | 建立 BDD 驗收場景 Issue | Gherkin 情境、驗收訊號、對應 SDD / TDD Issue 編號 |
| `sdd.md` | 記錄契約設計與合約測試計畫 | 契約對照表、Mock/樣本策略、版本與部署規劃 |
| `tdd.md` | 追蹤 TDD 測試與實作進度 | 測試矩陣、流程紀錄、風險應對、後續交付 |
| `bug_report.md` | 標準化缺陷回報，含嚴重度與相關 Scenario | 復現步驟、預期/實際行為、環境與風險、後續建議 |

## 執行流程概覽

```mermaid
flowchart LR
    INDEX[index] --> REQUIRE[requirements]
    INDEX --> REQCHANGE[requirements-change]
    INDEX --> TECH[tech-stack]
    REQUIRE --> BDD[bdd]
    REQCHANGE --> BDD
    TECH --> BDD
    BDD --> SDD[sdd]
    SDD --> TDDREQ[tdd-requirements]
    subgraph "TDD 子流程"
        direction LR
        TDDREQ --> TDDCASE[tdd-testcases]
        TDDCASE --> TDDRED[tdd-red]
        TDDRED --> TDDGREEN[tdd-green]
        TDDGREEN --> TDDREF[tdd-refactor]
        TDDREF --> TDDVERIFY[tdd-verify]
        TDDCHECK[tdd-checkpoint] -.進度盤點.-> TDDREQ
        TDDCHECK -.-> TDDCASE
        TDDCHECK -.-> TDDRED
        TDDCHECK -.-> TDDGREEN
        TDDCHECK -.-> TDDREF
        TDDCHECK -.-> TDDVERIFY
    end
    TDDVERIFY --> DELIVER[GitHub Issue / PR]
    TDDVERIFY --> REQCHANGE
```

1. 先執行 `index` 盤點現況與缺口。若發現技術尚未定義或變更幅度大，再啟動 `tech-stack`。  
2. `requirements` 用於建立新需求；`requirements-change` 啟動後需先在「變更影響評估」整理既有 BDD / SDD / TDD Issue 是否更新即可，僅在需要新增行為時才重新進入 BDD。  
3. BDD → SDD → TDD 逐步深化：先定義行為情境，再契約化介面/資料，最後規劃測試與實作。既有 Scenario 或契約若僅需調整，沿用對應 Issue 更新即可。  
4. 進入 TDD 時，先執行 `tdd-requirements` 建立背景，再依 `tdd-testcases` → `tdd-red` → `tdd-green` → `tdd-refactor` → `tdd-verify` 的順序推進；`tdd-checkpoint.prompt.md` 單純在各階段之間做進度檢查與回圈判斷，`tdd-verify` 未通過時依檢查結果回到適當階段或 `requirements-change`。  
5. 驗證通過後，接續任務拆解或實作流程（建立 PR、同步程式碼）並更新 GitHub Issue / PR。

### TDD 入口決策

| 情境 | 採取的 Prompt | 備註 |
| --- | --- | --- |
| 尚未建立 TDD Issue 或第一次進行該需求的測試迭代 | `tdd-requirements.prompt.md` | 建立背景、輸入輸出與限制；產出直接交給 `tdd-testcases` |
| 已完成 `tdd-requirements`，準備設計測試案例 | `tdd-testcases.prompt.md` | 若遇到契約資料缺口，請依表格中的判斷回到 `sdd` 或補資料 |
| 任一 TDD 子流程完成後需要確認下一步或統整阻塞 | `tdd-checkpoint.prompt.md` | 單純盤點進度與缺口，輸出推薦的下一個子 Prompt 或待解阻塞 |
| TDD 子流程執行中斷、遇到需求矛盾或跨子系統影響 | `tdd-checkpoint.prompt.md`（彙整議題）→ 視情況轉 `requirements-change.prompt.md` | `tdd-checkpoint` 僅列阻塞與疑慮；是否回圈由 `tdd-verify` 結案判定 |
| TDD 各階段完成（Red / Green / Refactor / Verify）需提交成果 | `commit-message.prompt.md` | 確認分支為 `tdd-*`、僅暫存該階段檔案並產生 Angular 風格 commit |

### 流程 Pseudocode

以下用 Python 語法示意主要流程：當 `index.prompt.md` 判定技術決策還不穩定時，會先引導執行 `tech-stack.prompt.md`，補齊共用的技術堆疊基準，再往下走需求、BDD、SDD 與 TDD。

```python
class PromptState:
    context: dict | None  # 下一個 Prompt 可直接引用的資訊
    recommendations: list[str]  # 建議後續應執行的 Prompt 名稱

class ChangeState(PromptState):
    only_updates_existing: bool
    next_inputs: dict | None
    targets: list[str]  # 需同步的 Issue 編號，例如 "#123"

class TDDState(PromptState):
    is_verified: bool
    next_prompt: str | None      # 例如 "tdd-red"
    next_inputs: dict | None     # 下一個子 Prompt 所需情境


def run_pipeline(user_inputs: dict) -> None:
    """依 index → requirements → BDD → SDD → TDD 的順序調度 Prompt。"""

    index_state = run("index.prompt.md", user_inputs)

    if "tech-stack" in index_state.recommendations:
        run("tech-stack.prompt.md", index_state.context.get("tech_stack", {}))

    if "requirements-change" in index_state.recommendations:
        change_state: ChangeState = run("requirements-change.prompt.md", index_state.context)
        user_inputs.update({"existing_issues": change_state.targets})
        if change_state.only_updates_existing:
            return
        if change_state.next_inputs:
            user_inputs.update(change_state.next_inputs)

    if "requirements" in index_state.recommendations:
        req_state = run("requirements.prompt.md", user_inputs)
        user_inputs.update(req_state.context or {})

    if "bdd" in index_state.recommendations or user_inputs.get("bdd_seed"):
        bdd_state = run("bdd.prompt.md", user_inputs.get("bdd_seed", {}))
        user_inputs.update(bdd_state.context or {})

    if user_inputs.get("sdd_inputs"):
        sdd_state = run("sdd.prompt.md", user_inputs["sdd_inputs"])
        user_inputs.update(sdd_state.context or {})

    if user_inputs.get("tdd_inputs"):
        tdd_state: TDDState = run("tdd-requirements.prompt.md", user_inputs["tdd_inputs"])
        while not tdd_state.is_verified:
            next_name = tdd_state.next_prompt
            if not next_name:
                break
            next_inputs = tdd_state.next_inputs or {}
            stage_result = run(f"{next_name}.prompt.md", next_inputs)
            tdd_state = run("tdd-checkpoint.prompt.md", stage_result.context or {})
```


## 信賴等級與來源標註規範

- 已能引用的資料（Issue、PR、文件）直接附上 `#編號` 或相對路徑即可，視為高信賴，無須額外標示符號。
- 僅在資訊屬於推測或待確認時補上符號：🟡（推測，待驗證）、🔴（尚無佐證）。
- 若資訊僅來自同步對話或未記錄的口頭說明，請標註 `（來源 使用者說明 🔴）` 並列入待辦補強。

## Issue 引用與連結規範

- 內部 Issue／PR：使用 `#編號`（例如 `#128`）。
- 跨 repo：使用 `owner/repo#編號`（例如 `tsumiki/prompt-repo#17`）。
- 指向特定留言時，於文字描述附上「留言者／時間」，避免依賴長網址。
- 回覆完成後，請提醒使用者或透過 MCP 將輸出內容回寫到對應 Issue，保持單一事實來源。

> 若需詳細操作步驟，請參考 `.github/prompts/_issue-ops-guide.md`，其中整理了 MCP 與手動流程以及各模板建議欄位。

## MCP 錯誤處理與回圈規則

- **連續錯誤累計**：同一測試（測試檔 + 測試名稱 + 錯誤訊息前 120 字）連續 3 次失敗 → 透過 MCP 在 TDD Issue 留言記錄錯誤、已嘗試步驟與下一步；連續 5 次 → MCP 將 Issue 標籤改為 `human_required` 並說明原因與建議回圈（如返回 `requirements-change`）。
- **分類標準**：統一使用 `Test Failure`、`Build/Setup Failure`、`External Dependency`、`Spec Mismatch` 方便彙整與追蹤。
- 各 TDD 子 Prompt 僅需引用本段規則，避免重複描述造成分歧。

> **責任分工**：Red/Green/Refactor 只需記錄錯誤與疑慮（標註 🟡／🔴），最終是否回圈由 `tdd-checkpoint.prompt.md`（進度調度）與 `tdd-verify.prompt.md`（結案判定）統一決策。

## TDD 提交節奏
- **Red**：每個失敗測試至少一個 commit，內容僅含測試檔及必要輔助檔案。
- **Green**：對應測試轉綠後立即提交，確保最小實作可追溯。
- **Refactor**：以重構主題分批提交（命名、抽象、效能調整等），維持測試綠燈。
- **Verify**：總驗證與文件同步後提交一次，若需 squash 由後續合併流程處理。
- 任何提交都應在 `tdd-*` 分支上完成，partial staging 請依 `commit-message.prompt.md` 建議操作。

## 需求變更觸發檢查表（供 `requirements-change.prompt.md` 及下游參考）

| 觸發情境 | 判定流程 | 建議動作 |
| --- | --- | --- |
| 新增或大幅調整使用情境、使用者故事、EARS 條目 | 確認是否已有對應 Scenario／契約 | 執行 `requirements-change.prompt.md` 更新需求基線；若已有 BDD Scenario，標記需調整項目並同步 `bdd` |
| 合約欄位／資料格式缺失，或跨系統介面改動 | 若契約已存在 → 回 SDD；若需求敘述模糊 → 先回需求 | 先透過 `requirements-change` 釐清需求，再執行 `sdd` 更新契約與版本策略 |
| 測試案例與 Scenario 不符、測試資料無法對應 | 比對測試矩陣與 Scenario/契約；屬需求差異即回需求 | 若是資料樣本不足 → `tdd-testcases`／`tdd-red` 補 mock；若 Scenario 失真 → `requirements-change` 修正需求並更新 BDD |
| 監控指標、SLO、部署或 CI/CD 守門條件改變 | 檢查是否影響交付品質或驗收標準 | 視為需求調整，更新 `requirements-change`，並同步提醒 `tdd-verify`、CI/CD 維護者 |


## 自動化腳本

| 腳本 | 說明 |
| --- | --- |
| `scripts/tdd-cycle.sh` | 依序觸發 `tdd-requirements → tdd-testcases → tdd-red → tdd-green → tdd-refactor → tdd-verify`，支援指定 Issue 編號、測試名稱與略過前置階段。請在執行過程遵守 MCP 留言與標籤調整規則；腳本內含錯誤捕捉，失敗時請回到 TDD Issue 更新阻塞。 |

### 異常情境處理

- `requirements` ↔ `requirements-change`：若 `docs/spec/` 或既有 Issue/PR 中已有正式需求，即視為「既有需求」。執行 `requirements-change` 時請先用變更影響評估檢查既有 BDD / SDD / TDD Issue 是否只需更新；僅在新增行為或契約時再走完整流程。
- BDD / SDD 迭代：在 SDD 或 TDD 發現情境缺漏時，優先更新既有 BDD Issue 的 Scenario，並同步註記受影響的 SDD / TDD Issue（使用 `#編號` 格式）。
- TDD 回圈：`tdd-verify` 未通過時，依檢查清單判定要回到 `tdd-red`（測試不足）、`tdd-green`（實作未完成）或 `tdd-refactor`（品質問題）；若產生需求差異則轉回 `requirements-change`。
- 回圈決策：Red / Green / Refactor 僅標記疑慮；實際是否回到上游階段以 `tdd-checkpoint` 或 `tdd-verify` 的結論為準。
- 技術堆疊：發生重大技術調整、部署目標或跨專案共用時，再啟動 `tech-stack` 重新盤點並更新 README 與相關 Issue。

### Issue 關聯規範

- 所有 BDD / SDD / TDD Issue 需在描述欄內標示來源需求（Issue 編號或文件路徑）與信賴等級符號（🔵／🟡／🔴）。
- BDD Issue 的「關聯工作」欄必須填寫對應 SDD / TDD Issue 編號，格式建議 `#123 (SDD)`、`#124 (TDD)`；若尚未建立，請標記 `待建立` 並在輸出提供建議標題。
- SDD Issue 應列出所依賴的 BDD Scenario 標籤（例如 `BDD-001`）與預計更新的契約檔案路徑。
- TDD Issue 中的測試矩陣需同步列出對應 BDD Scenario 與 SDD 契約 ID，方便追蹤來源。
- 建議透過 MCP 操作 GitHub 時，一併更新彼此的 Issue 交互連結與狀態（例如使用 `linked pull requests`、`/link` 指令）。

#### Scenario ↔ Issue 對照範例

```markdown
| Scenario ID | 說明 | 需求 # | SDD Issue | TDD Issue | 狀態 |
| --- | --- | --- | --- | --- | --- |
| BDD-001 | 使用者登入成功 | #101 | #202 | #303 | ✅ |
| BDD-002 | OTP 驗證失敗 | #101 | #202 | #304 | ⚠️ Mock 待補 🟡 |
| BDD-003 | 帳號鎖定提示 | #101 | 待建立 | 待建立 | 🔴 需求待確認 |
```

> BDD 完成後請在 SDD / TDD Issue 內更新此表，並於每次迭代同步狀態。

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

## 各種 CLI 存放 Prompt 位置

- GitHub Copilot：`$HOME/Library/Application Support/Code/User/prompts`
- Codex CLI：`~/.codex/prompts`

```shell
cp -r ~/.codex/prompts/* "$HOME/Library/Application Support/Code/User/prompts/"
cp -r ./prompts/* "$HOME/Library/Application Support/Code/User/prompts/"
```
