---
name: BDD 驗收情境
about: 將需求轉為 Gherkin 驗收案例，串接 SDD 與 TDD 流程
title: "[BDD] 模組／功能名稱 - 簡述"
labels: bdd
assignees: ''
---

> 使用前請先完成需求彙整，並於填寫時引用對應的 `#Issue` 或文件連結；推測或待確認事項以 🟡／🔴 標註。

## 1. 任務背景
- 核心目標與不可變限制：
- 需求來源與既有輸出（含最新 EARS／GWT 摘要、需求 Issue）：
- 相關 BDD Issue / Scenario 狀態（若為首度建立請註記）：
- 重要時程 / Milestone / 責任人：
- 涉及子系統（請勾選保留）：
  - [ ] API
  - [ ] 前端 / Client
  - [ ] 後端 / Service
  - [ ] 資料 / ETL
  - [ ] AI / 模型
  - [ ] Infra / DevOps
  - [ ] 其他：

## 2. 參考資料
- 設計稿 / 文件：
- 會議記錄 / 對話：
- 其他關聯 Issue / PR：

## 3. Gherkin 驗收案例
> 至少列出一個成功情境與一個例外情境；每個 Scenario 請給定唯一 `BDD-###`。對於待確認的行為以 🟡／🔴 標註並附來源。

```gherkin
Feature: [以一句話描述功能目標]
  Background:
    Given [共用前置條件，若無可刪除]

  Scenario: BDD-001 Happy Path — [成功情境摘要]
    Given 
    When 
    Then 

  Scenario: BDD-002 Exception — [例外情境摘要]
    Given 
    When 
    Then 

  # 如有其他情境請續增 Scenario，並依序給定 BDD-003...
```

## 4. 驗收訊號與測試資料
- 成功訊號（UI/事件/回應碼/指標）：
- 失敗訊號與監控需求：
- 所需測試資料或模型（責任人 / 來源 / 處理狀態）：

## 5. Scenario 對照表
| Scenario ID | 需求 # | 對應 SDD Issue | 對應 TDD Issue | 備註 / 狀態 |
| --- | --- | --- | --- | --- |
| BDD-001 |  |  |  |  |
| BDD-002 |  |  |  |  |
|  |  |  |  |  |

## 6. 任務幅度矩陣
| 子系統 / 範圍 | 主要元件或流程 | 影響面 | 責任人 | 備註 |
| --- | --- | --- | --- | --- |
|  |  |  |  |  |
|  |  |  |  |  |

## 7. 開放問題與下一步
- 待確認項目 / 風險（含 🟡／🔴 標記）：
- 建議下一步（預設進入 `sdd.prompt.md`、建立對應 Issue 等）：

---
- [ ] 已依 `_issue-ops-guide.md` 建立或更新 BDD Issue，並同步關聯的 SDD / TDD Issue（互相引用）。
- [ ] 已通知相關負責人檢視本次更新。
