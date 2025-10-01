---
name: BDD 驗收場景
about: 定義行為驅動的驗收案例（Given-When-Then）
title: "[BDD] 模組／功能名稱 - 簡述"
labels: bdd
assignees: ''
---

## 背景摘要
- 來源需求（Issue / 文件 / 對話連結）：
- 本次涵蓋的需求編號（EARS 或其他）：
- 目標時程或 Milestone：
- 涉及子系統（複選）：
  - [ ] API / 後端服務
  - [ ] 前端 / UI / 多端裝置
  - [ ] 資料處理 / ETL
  - [ ] AI / ML 訓練或推論
  - [ ] 排程 / Lambda / 基礎設施
  - [ ] 資料庫 / 倉儲 / BI
  - [ ] 其他（請說明）：
- 信賴等級（🔵 已確認／🟡 推測／🔴 待確認）：

## Gherkin 驗收案例
> 至少提供一個成功情境與一個例外情境，必要時可加入 Scenario Outline。

```gherkin
Feature: 
  Background:
    Given 
    And 

  Scenario: 成功情境 🔵
    Given 
    When 
    Then 

  Scenario: 例外或邊界情境 🟡
    Given 
    When 
    Then 
```

- 其他情境（如需）：
- 尚未釐清的情境（列為開放問題）：

## 驗收訊號
- 成功訊號（輸入、輸出、事件、UI 狀態等）：
- 失敗訊號（錯誤代碼、補救流程）：
- 監控或告警需求（若無請填 `不適用`）：
- 需準備的測試／樣本資料：

## Scenario 對照表

| Scenario ID | 需求 # | 對應 SDD Issue | 對應 TDD Issue | 備註 / 狀態 |
| --- | --- | --- | --- | --- |
|  |  |  |  |  |
|  |  |  |  |  |

- 其他相關 Issue / PR：

## 開放問題
1. 
2. 
3. 

> 建議在評論區附上 Scenario 與需求編號的差異說明，並於建立對應的 SDD / TDD Issue 後更新表格與狀態（🔵／🟡／🔴）。
