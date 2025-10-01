---
name: TDD 測試與實作計畫
about: 依據 BDD / SDD 制定 Red-Green-Refactor 循環
title: "[TDD] 模組／測試套件名稱 - 簡述"
labels: tdd
assignees: ''
---

## 來源與範圍
- BDD Issue 連結：
- SDD Issue 連結：
- 涉及子系統（複選）：
  - [ ] API / 後端服務
  - [ ] 前端 / UI
  - [ ] 資料處理 / ETL / Pipeline
  - [ ] AI / ML 模型
  - [ ] 排程 / Lambda / Infra
  - [ ] 資料庫 / 倉儲
  - [ ] 觀測 / 監控
  - [ ] 其他（請說明）：
- 信賴等級（🔵 已確認／🟡 推測／🔴 待確認）：

## 測試矩陣
| Test ID | Scenario ID (BDD-###) | 需求 # | 測試類型 | 資料 / Mock | 狀態（Red/Green/Refactor） | 優先順序 | 備註 | 來源 / 狀態 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
|  |  |  |  |  | Red | P0 |  | （#123 🔵） |
|  |  |  |  |  | Red | P1 |  | （口頭說明 🔴） |

- 每次完成 Red / Green / Refactor 循環後更新此表，於備註欄附上測試輸出或 PR 連結。

## 迭代進度（完成後請勾選）
- [ ] Requirements：已執行 `tdd-requirements` 並更新「來源與範圍」
- [ ] Testcases：已執行 `tdd-testcases` 並更新測試矩陣
- [ ] Red：已執行 `tdd-red` 並提交失敗測試
- [ ] Green：已執行 `tdd-green` 並提交轉綠實作
- [ ] Refactor：已執行 `tdd-refactor` 並提交重構成果
- [ ] Verify：已執行 `tdd-verify` 並提交驗證報告

## 開發與驗證流程
- 預計採用的測試框架與指令：
- 測試資料或樣本來源：
- 依賴的外部服務或模擬方式：
- CI/CD 與自動化需求：
- 完成判準（所有測試轉綠、合約測試通過、品質量測完成等）：

## 風險與應對措施
- 已知風險（效能、成本、資料品質、模型表現、部署限制等）：
- 針對上述風險的預防或緩解做法（監控、警示、回滾計畫等）：

## 後續交付
- 預計送出的 PR / Merge Request：
- 需更新的文件或 README：
- 其他待建立的 Issue：

## 待辦追蹤
| 項目 | 負責人 | 截止日期 | 狀態 | 備註 |
| --- | --- | --- | --- | --- |
|  |  |  |  |  |

## 開放問題
1. 
2. 
3. 

> 建議在每次 Red → Green → Refactor 循環結束後於評論更新測試結果與主要變更，保持透明度與可追蹤性。
