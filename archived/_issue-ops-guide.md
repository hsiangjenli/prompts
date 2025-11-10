---
mode: reference
description: 共用的 GitHub Issue 操作指引（MCP 與手動流程）
---

# Issue 操作指引

## 基本原則
- 以 GitHub Issue 作為單一事實來源；若臨時輸出留在本地，務必在流程結束前回寫。
- 透過 MCP 時，如遇權限或失敗，再改手動操作。
- 引用 Issue 或 PR 一律使用 `#編號`（跨 repo 則為 `owner/repo#編號`）。
- 僅對推測或待確認的內容標示 🟡／🔴；已從 Issue / 文件取得的資訊可省略 🔵。

## 共用欄位
| 欄位 | 說明 |
| --- | --- |
| `title` | 必須使用對應模板規定的格式（見「標題規範」），僅替換占位符內容 |
| `labels` | 對應流程的類型標籤：`requirements`、`requirements-change`、`bdd`、`sdd`、`tdd`、`tech-debt`… |
| `body` | 直接貼上各 Prompt 的輸出（表格、清單、檢查表），勿另寫摘要。 |
| `assignees` / `projects` | 依專案流程填寫；不確定時可留空並在評論標註負責人。 |

### 標準表格欄位
- Scenario 對照表（BDD）：`Scenario ID | 需求 # | 對應 SDD Issue | 對應 TDD Issue | 備註 / 狀態`。
- 契約對照表（SDD）：`Scenario ID | 契約類型 | 契約名稱/版本 | 關鍵欄位/事件 | 驗證方式 | 來源 # | 狀態`。
- 測試矩陣（TDD）：`Test ID | Scenario ID | 需求 # | 測試類型 | 資料/Mock | 狀態（Red/Green/Refactor） | 優先順序 | 備註 | 來源/狀態`。
- 交付清單 / 待辦追蹤：`項目 | 描述 | 來源 # | 負責人 | 截止日期 | 狀態`。

> 建議在表格的「狀態」欄明確解釋符號含意，例如 ✅/⚠️/❌ 或 🔵/🟡/🔴。

## 模板對應
| 流程 | 建議模板 | 其他提示 |
| --- | --- | --- |
| 需求盤點 | `.github/ISSUE_TEMPLATE/requirements.md`（若存在）或臨時 Markdown | 記錄 EARS、GWT、優先順序，後續交給 BDD |
| 需求變更 | `.github/ISSUE_TEMPLATE/requirements-change.md` 或沿用原 Issue | 在評論摘要變更、影響範圍與下游任務 |
| BDD | `.github/ISSUE_TEMPLATE/bdd.md` | 更新 Scenario 對照表並標註 SDD/TDD Issue 編號；沿用既有 Issue 時註明差異 |
| SDD | `.github/ISSUE_TEMPLATE/sdd.md` | 附上契約對照表、Mock 策略、版本計畫與受影響服務 |
| TDD | `.github/ISSUE_TEMPLATE/tdd.md` | 更新「來源與範圍」與測試矩陣（含狀態欄），並勾選迭代進度核取框 |
| 技術堆疊 | `.github/ISSUE_TEMPLATE/tech-stack.md`（若存在） | 補充現有決策、待確認項目與風險 |

## 標題規範
- BDD：`[BDD] 模組／功能名稱 - 簡述`
- SDD：`[SDD] 模組／契約名稱 - 簡述`
- TDD：`[TDD] 模組／測試套件名稱 - 簡述`
- README：`[README] 專案名稱 - 範本與交付檢查`
- Bug 回報：`[Bug] 功能區域 – 問題摘要`
- 其他模板若已定義格式，請保留分類與後綴不變，僅替換占位符文字。

## MCP 使用流程範例
```pseudo
if 新建 Issue:
    issue_write(title, body, labels, assignees)
    if 需補充附件:
        create_comment(issue_number, 補充內容)
else:
    update_issue(issue_number, body)
    create_comment(issue_number, 差異摘要)
```

## 手動作業備案
1. 進入相應模板建立 Issue，貼上輸出內容。
2. 若為更新既有 Issue，於描述或評論中標註「更新項目」、「來源」、「下一步」。
3. 同步引用其他 Issue：在內容中加入 `Refs #123` 或表格欄位。
4. 完成後確認核取框（如 TDD 進度、測試矩陣）是否更新。

> 所有 Prompt 若提及「回寫 Issue」，請直接依本指引操作並使用對應模板／標籤。
