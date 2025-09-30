---
mode: agent
---
---
description: 作為入口導覽，根據使用者提供或既有資產的專案狀態推薦合適的指令／Prompt 與後續步驟。
---

# index

## 目的

在面對眾多 tsumiki / kairo / 自訂指令時，協助使用者釐清目前所處階段、整合資料來源，並輸出下一步應執行的 Prompt（名稱不必限定含有「kairo」），同時提示應產出的 EARS 需求與 GWT（Given-When-Then）驗收描述。

## Input

- 使用者當前的問題描述、目標與時程要求
- 已完成或既有的文件清單（例如 `docs/spec/`、`docs/design/`、`CHANGELOG.md`）
- 相關 GitHub Issue 編號與重點留言（若無可請使用者補充）
- 既有指令或 Prompt 的執行情況與結果（若已執行過）
- 語言或輸出格式偏好（預設為繁體中文）

## Output

- 專案現況摘要（含資料來源、信賴等級標示）
- 最近 Changelog、GitHub Issue/Comment 的重點整理
- 推薦指令／Prompt 清單與執行順序，說明各自的目的、必要輸入與預期產出
- 對 EARS 與 GWT 覆蓋情況的檢查結果與缺口
- 建議的後續動作：需建立的 Issue／PR／Draft、需閱讀的文件、下一個 Prompt

## 前提

- 可讀取專案根目錄（如 `docs/`、`.github/`、`commands/`、`change` 或 `CHANGELOG.md`）。
- 擁有 GitHub API 權限或可取得 Issue 與 Comments（若無權限，需告知使用者並改為索取摘要）。

## 執行流程

1. **蒐集上下文**
   - 讀取 `README.md`、`docs/spec/`、`docs/design/`、`CHANGELOG.md`（或等效檔）了解版本與近期變更。
   - 透過 GitHub API / CLI 擷取最近的 Issue 與主要留言（摘要即可），特別是與需求、Bug、設計相關的項目。
   - 若無法存取 Issue/Comment，向使用者說明限制並請對方提供要點。
   - 確認語系需求，預設以繁體中文回應。

2. **盤點需求階段（與使用者互動）**
   - 需求是否已經以 EARS 形式撰寫？若沒有，標記為缺口。
   - 是否存在對應的 GWT 驗收描述或測試案例？
   - 是否需要技術設計、資料流程、介面定義？
   - 是否已拆分任務或準備建立 Issue / PR？
   - 是否屬於對既有需求的新增或調整？若是，預設優先建議 `requirements-change`。
   - 是否進入實作或測試（TDD、CI/CD）階段？

3. **彙整輸入資訊**
   - 列出使用者目標、既有文件來源、GitHub Issue 摘要、最近的 Changelog 重點。
   - 標示各階段完成狀態與缺漏（🔵 確定 / 🟡 推測 / 🔴 假設）。

4. **推薦指令 / Prompt**
   - 依據缺漏與目標，列出 3~5 個最適合的指令（不限名稱前綴，可包含 `requirements-change`、`design`、`tdd-*` 等自訂 Prompt）。
   - 指令說明需包含：目的、預期輸入、主要產出（例如 EARS 需求、GWT 驗收、Issue 草稿、CI/CD 任務）。
   - 若需跨多階段，提供執行順序與依賴關係。

5. **輸出格式**
   - 以 Markdown 條列：
     - 專案現況摘要（含來源、信賴等級）
     - 最近 Changelog 與 GitHub Issue/Comment 的重點整理
     - 推薦指令清單（依優先順序）
     - 每個指令的輸入需求、產出（需明確指出 EARS、GWT 是否涵蓋）、注意事項
     - 建議下一步，包括是否需在 GitHub 建立 Issue / PR / Draft

## 注意事項

- 若遇到客製化模板，請將其加入建議清單並描述其用途。
- 資訊不足時必須向使用者追問，而非自行臆測。
- 回應維持繁體中文；若可使用 MCP 或自動化工具，請直接建立／更新 GitHub Issue 留存輸出，僅在無法存取時再請使用者協助。
