---
mode: agent
description: 在 TDD 開發過程中（Red/Green/Refactor/Verify 後），於 `tdd-*` 分支上為當前階段變更產生部分提交並輸出 Angular 風格的 commit message
inputs:
  summary: 蒐集本次提交的分支、變更與待辦資訊
  required:
    - 目前 git 分支名稱與狀態（`git status -sb` 結果）
    - 本次提交要涵蓋的檔案與變更摘要
    - 應保留未提交或另行處理的檔案（若有）
    - 預期的變更類型（feat / fix / chore / test / docs / refactor / build / ci 等）與可選的 scope
    - 關聯的需求或 Issue 編號（例如 `#123` 或 `owner/repo#123`）
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 提供分支與部分暫存建議，並產生 Angular 風格的 commit message
  include:
    - 分支確認或建立指令（須符合 `tdd-*` 命名）
    - 建議暫存的檔案與 `git add -p`／`git restore --staged` 操作提醒
    - Angular 風格 commit message（含摘要、可選 body 與 footer）
    - 需同步更新的 Issue／PR／Changelog 項目
    - 後續動作：檢查 `git status`、`git commit`、`git push` 或後續流程提示
---

# commit-message

## 目的

在每個 TDD 子階段（Red → Green → Refactor → Verify）結束時，協助開發者僅暫存與該階段相關的檔案，並產出符合 Angular 規範的 commit 訊息。提交前會確認分支命名（`tdd-*`）、跨檔案一致性與關聯 Issue，避免將不相干的修改打包在同一次 commit。於 `tdd-verify` 完成後，再由使用者視需要整理歷史（例如 squash）後合併。

## 執行前確認

- 目前開發必須位於 `tdd-*` 開頭的分支；若仍在其他分支，先行切換或建立新分支。
- `git status -sb` 已掌握工作樹狀態，並能區分與本次提交無關的檔案。
- 已完成所有必要的測試／驗證（例如 `tdd-verify.prompt.md` 建議的檢查）。
- 需求或 Issue 編號已知，便於在 commit footer 及後續 changelog 中引用。

## 流程

### Phase 0：準備（檢查分支與變更）
1. **分支確認**：若當前分支非 `tdd-*`，產生 `git checkout -b tdd-<keyword>` 建議並提醒使用者切換。
2. **階段判定**：請使用者明確指出本次提交對應的 TDD 階段（Red / Green / Refactor / Verify）；可提示「Red 建議一個測試一個 commit、Green 以轉綠為界、Refactor 依重構主題分批、Verify 彙整最終驗證」。
3. **盤點變更**：列出所有已修改檔案，標示哪一些應納入此次提交、哪一些需保留未 stage。必要時可建議使用 `git diff --stat` 快速檢視。
4. **風險提醒**：若偵測到測試或 CI 相關檔案，提示檢查是否需要同時更新對應的 pipeline／文件。

### Phase 1：執行（部分暫存與訊息擬稿）
1. **部分暫存**：
   - 建議使用 `git add -p <file>` 對相關檔案逐段暫存。
   - 若已有誤加的檔案，提示 `git restore --staged <file>` 或 `git reset HEAD <file>` 撤回。
   - 需要一次暫存多個測試檔或指定型別時，可依下列範例調整：
     ```bash
     # 僅暫存測試與型別宣告檔
     git add tests/**/*.test.ts src/**/*.d.ts

     # 先取消全部暫存，再挑選需要的檔案
     git reset
     git add src/service/user.ts
     git add -p src/service/user.test.ts
     ```
2. **分類摘要**：根據輸入的變更類型與 scope，整理每個檔案的修改目的，為 commit message body 做準備。
3. **撰寫訊息**：
   - 依 Angular 規範組合 `type(scope): summary`。
   - Body 以條列方式描述主要變更，引用資料附 `#編號`，對推測或待確認的內容標註 🟡／🔴。
   - Footer 加入 `Refs #123` 或 `Closes owner/repo#456` 等資訊，方便 changelog 工具解析。

### Phase 2：記錄與交接（提交前檢查）
1. **`git status` 驗證**：提示使用者確認只有預期的檔案被 stage。
2. **指令建議**：提供 `git commit`、`git push`、更新 changelog 或開 PR 的後續步驟。
3. **同步提醒**：若提交會影響文件、測試或 CI，列出需同步更新的 Issue／PR／文件並附 `#編號`；對尚未確認的事項標註 🟡／🔴。

## 產出格式建議

- **分支建議**：`git checkout -b tdd-<keyword>` 或確認訊息。
- **建議暫存命令**：條列關鍵檔案搭配 `git add -p`／`git restore --staged`。
- **Commit 訊息草稿**：
  ```
  type(scope): summary

  - change 1…（來源 #123 🔵）
  - change 2…（來源 docs/design/... 🟡）

  Refs #123
  ```
- **後續動作**：例如 `git status` → `git commit` → `git push -u origin tdd-<keyword>` → 開 PR。

## 後續建議

- 若提交後仍有尚未處理的檔案，記錄待辦或重新執行本 Prompt 分批提交。
- 提醒使用者在對應 Issue 留言記錄此次 commit，或使用 MCP 自動更新。
- 若需更新 changelog，列出使用的指令或工具（例如 `pnpm changelog`）。
