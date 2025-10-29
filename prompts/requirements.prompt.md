---
mode: agent
description: 先理解需求並整理成 EARS 列表，為後續 BDD --> SDD --> TDD Prompt 提供完整輸入
inputs:
  summary: 使用 Prompt 釐清使用者需求
  required:
    - 直接對話進行釐清
    - 提供會議記錄、文件、`README.md`、GitHub Issue Number 等相關資訊
outputs:
  summary: 以使用者情境為主，整理可追蹤的需求摘要，準備交由 BDD 進一步展開（非技術實作細節）
  include:
    - 輸出 EARS 清單
    - 建議接手的 Prompt（預設 `bdd.prompt.md`）；若出現技術或 SDD 議題，請標註並轉給 `tech-stack.prompt.md` / `sdd.prompt.md`
---

# 需求分析

## 目的

以不斷提問方式去釐清需求，並使用 EARS 語法整理需求，方便後續對齊 BDD --> SDD --> TDD 的實作流程。

## 提問原則

- 採「單一問題 + 選項 + 自訂輸入」格式，便於使用者快速回覆。
- 每題最多 4 個選項，預留「⑤ 其他／自行輸入」。
- 必要時加入範例或預設值（例如：「預設語言：繁體中文」）。

**問題範例**

```
是否為全新的一份專案？
① 是
② 否
```

```
目前主要聚焦的領域是？
① API / 後端服務
② 前端或 UI 互動
③ 資料處理 / ETL / ML 訓練
④ CI/CD、Infra
⑤ 其他（請說明）
```

## 操作流程

### Step 1：盤點基準

1. 閱讀使用者提供的文件（如：`README.md`、`CHANGELOG.md`、GitHub Issue 等），了解近期變更與業務邏輯
2. 檢查是否已有 EARS 需求 Issue；若有，請根據原本的 Issue 進行修改

### Step 2：補齊需求

1. 發現需求的不一致、缺漏
1. 藉由不斷提問，釐清使用者的需求

### Step 3：整理輸出

1. 根據對話以及文件，建立 EARS Issue，並根據類型排列 EARS 列表

## 後續行動

- 在輸出末尾再次提醒下一個預計執行的 Prompt（預設 `bdd.prompt.md`）
