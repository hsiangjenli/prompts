---
mode: agent
description: 總驗證 TDD 迭代成果，確認測試、契約、品質檢查皆達標並回報結果
inputs:
  summary: 使用本 Prompt 進行 Verify / Complete 階段的檢查與回報
  required:
    - 目標 TDD Issue 與最新測試矩陣
    - Red / Green / Refactor 階段的輸出摘要與待辦
    - 必須通過的測試指令、CI/CD 流程與品質守門清單
    - 需要同步驗證的契約、文件、監控設定
    - 語言與輸出格式偏好（預設繁體中文 + Markdown）
outputs:
  summary: 出具驗證結果報告，判定是否結束 TDD 迭代或需重新循環
  include:
    - 驗證檢查清單（測試、品質、契約、文件、監控）的通過狀態、來源 `#編號` 與證據；對待確認項目標註 🟡／🔴
    - 契約 / 文件同步情況與缺口（含負責人、完成時程、來源，必要時標註 🟡／🔴）
    - 回圈判定決策樹：依檢查結果列出需返回的階段（Red / Green / Refactor / requirements-change / sdd），並說明觸發條件（測試失敗類型、品質缺口、契約偏差、需求偏移）
    - 建議後續動作：提交 PR、更新 Issue、建立新任務等（附來源與責任人）
---

# tdd-verify

## 目的

在完成 Red→Green→Refactor 後，執行最終驗證，確認所有測試與品質檢查通過、契約與文件同步更新，並決定是否結束本次 TDD 迭代或回到先前階段。

> 前述子流程（Red / Green / Refactor / 檢查點）僅需紀錄阻塞與疑慮，真正的回圈判定與需求調整決策在本階段一次完成，避免多處邏輯分散。

## 流程

### Phase 0：準備（檢查清單建立）
1. 根據 TDD Issue 測試矩陣列出所有需驗證的測試項目與指令。
2. 列出品質守門檢查：Lint、型別、Coverage、靜態分析、資安掃描等。
3. 確認契約、文件、監控設定是否需要更新或驗證。

### Phase 1：執行（逐項驗證）
1. 逐項執行測試與檢查，記錄輸出與結果。
2. 若有未通過項目，詳細描述錯誤訊息與影響範圍，並以決策樹判定回到哪個階段處理。
3. 確認契約檔案或文件是否與 SDD 一致，若需補件，記錄路徑與負責人。

### Phase 2：記錄與交接（判定與回報）
1. 將檢查清單結果統一為 ✅（通過）、⚠️（需跟進）、❌（未通過），並計算合格率與剩餘待辦；每項附來源 `#編號`，對待確認項目標註 🟡／🔴。
2. 若所有檢查項目均為 ✅，整理驗證摘要、耗時、關鍵指標，標示「合格」並確認是否可提交 PR 或部署，列出必要的後續行動（合併、部署、更新 changelog 等）。
3. 若任一項為 ❌／⚠️，套用下列決策樹：
   - 測試失敗且測試案例不足 → 回到 `tdd-testcases` 或 `tdd-red`。
   - 測試失敗但案例完整 → 回到 `tdd-green` 修正實作。
   - 品質檢查未過、技術債未解 → 回到 `tdd-refactor`，註明須額外檢查項目。
   - 契約 / 文件未同步 → 回到 `sdd`（契約更新）或 `tdd-refactor`（文件修正），附路徑。
   - 需求偏移或跨子系統影響 → 建議啟動 `requirements-change`，並列出影響的需求與 Scenario。
   ```pseudo
   if 測試未通過:
       if 案例缺漏: return "tdd-testcases" → "tdd-red"
       else: return "tdd-green"
   elif 品質檢查未過 or 技術債未收斂:
       return "tdd-refactor"
   elif 契約/文件不同步:
       return "sdd" or "tdd-refactor"  # 視是否需調整契約內容
   elif 需求偏移:
       return "requirements-change"
   else:
       return "tdd-verify"  # 重新驗證直到全數通過
   ```
4. 提醒在 TDD Issue 更新測試矩陣與狀態，並附上驗證輸出或 CI 連結；若有 ⚠️／❌ 項目，記錄對應的回圈動作。
5. 建議後續動作（建立 PR、通知 reviewer、更新 changelog、安排上線），並列出責任人與時程；若需再次迭代，列出預計開始時間與負責人。同時在 TDD Issue 迭代進度中勾選 `Verify` 核取框，若決策為回圈則記錄需清除或暫時保留的勾選狀態。

## 產出格式建議

- **驗證結果表**：項目、指令 / 工具、結果、來源 `#編號`、狀態（✅/⚠️/❌）、備註。
- **契約 / 文件同步清單**：是否已更新、連結、待補事項、責任人、完成期限。
- **決策摘要**：明確標示狀態（`Ready for PR` / `Re-run Red` / `Re-run Green` / `Refactor Needed` / `Return to requirements-change` / `Update SDD`）與下一步。

## 後續建議

- 若需再次迭代，建議立即回到指定階段並更新 TDD Issue。
- 合格後，可啟動實作 PR 流程或進入更高層級測試。
- 在輸出末尾提醒使用者確認 TDD Issue 已留存完整驗證記錄，並附上使用的證據來源；對需跟進的項目標註 ⚠️（含 🟡／🔴 說明）。

## Issue 操作提醒

依 `_issue-ops-guide.md` 的「TDD」指引：貼上驗證結果表、勾選 `Verify` 核取框，並於評論紀錄決策摘要與後續行動；若需回圈或新增任務，建立對應 Issue 後互相引用。
