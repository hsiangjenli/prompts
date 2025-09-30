---
mode: agent
description: 入口指令；盤點現況、找出缺口並推薦下一步 Prompt
inputs:
  summary: 需先掌握使用者當前問題與現有產出
  required:
    - 目前的卡關點、目標與時程限制
    - 現有文件與路徑（例如 docs/spec/, docs/prompts/, CHANGELOG.md）
    - 相關 GitHub Issue 編號與重點留言（可透過 MCP 取得）
    - 近期已執行的指令／Prompt 及其主要輸出
    - 語言或格式需求（預設繁體中文 + Markdown）
outputs:
  summary: 提供決策所需的全貌、推薦步驟與後續動作
  include:
    - 專案現況摘要（含資料來源與信賴等級：🔵 確定／🟡 推測／🔴 假設）
    - 近期變更重點（CHANGELOG、Git 提交或 Issue 摘要）
    - 推薦 Prompt 清單（建議 ≤3 項），說明目的、必要輸入、預期產出與 EARS/GWT 覆蓋
    - 建議動作：需建立或更新的 Issue／PR、需補文件、待確認事項（可用 MCP 直接建立）
    - 開放問題：列出尚待使用者提供的資訊
---

# index

## 目的

此指令作為流程入口，負責總覽專案現況、辨識需求缺口，並推薦接下來應執行的 Prompt。關鍵任務包含：
- 判斷 EARS 需求與 GWT 驗收是否齊備。
- 盤點是否已有 BDD / SDD / TDD 輸出及其完整度。
- 修正 Prompt 使用順序，避免重工。

## 流程

在進入 Phase 0 前，請確認：
- 可透過 MCP 或 API 存取 GitHub Issue／留言；若權限不足需在輸出中註明並向使用者索取資訊。
- 回覆語言設定為繁體中文。

### Phase 0：自動蒐集
1. 讀取 `README.md`、`CHANGELOG.md`（或等效檔）、`docs/spec/`。若已有 BDD / SDD / TDD Issue，列出其狀態。
2. 透過 MCP 取得最新的 GitHub Issue / Comments，了解需求或變更討論。
3. 將已有輸出與缺漏整理成清單（需求、行為案例、契約、測試、實作等）。

### Phase 1：使用者訪談
1. 根據缺漏提出封閉式問題（EARS、GWT、BDD、SDD、TDD、CI/CD）。
2. 判斷此次是新需求、需求變更，或進入測試/實作階段，方便推薦對應 Prompt。
3. 確認期望交付與時間限制。

### Phase 2：決策與輸出
1. 依狀態對照推薦：
   | 主要缺口 | 優先 Prompt |
   | --- | --- |
   | 缺少正式需求 | `requirements.prompt.md` |
   | 既有需求需調整 | `requirements-change.prompt.md` |
   | 需求已確認需建立行為案例 | `bdd.prompt.md` |
   | 行為案例已定需契約化 | `sdd.prompt.md` |
   | 需要規劃 / 執行 TDD 迭代 | `tdd.prompt.md` |
2. 組成輸出：
   - 現況摘要（含來源與信賴等級）。
   - CHANGELOG／Issue 重點。
   - 推薦 Prompt（目的、必要輸入、預期產出、EARS/GWT 覆蓋情況）。
   - 建議動作（必要時透過 MCP 建立 Issue／PR、留言或更新標籤）。
   - 開放問題與待補資訊。

## 注意事項

- 先確認資訊是否完整；不足時應向使用者追問。
- 回覆需保持結構化，方便貼入 GitHub Issue。
- 若偵測輸出重複，提醒使用者檢視是否可沿用既有成果。
- 可使用自動化工具時，優先主動建立或更新 Issue 並附連結。
