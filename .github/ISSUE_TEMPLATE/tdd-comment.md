---
name: TDD Test Comment  
about: 用於追蹤單一 Test 的 Red → Green → Refactor 生命週期，建立於 TDD Issue  
---

## Test: <test_id> - <scenario_id>

### 測試檔案路徑:
<!--
請填入測試檔案的完整路徑。

Example:
tests/unit/auth/login.test.ts
-->
<test_file_path>

### Red 階段 - <timestamp>
<!--
請記錄測試失敗的詳細資訊。

Example:
- **狀態**: 🔴 失敗
- **失敗原因**: Expected status 200 but received 404. Function `authenticateUser` not implemented.
- **重試次數**: 0
-->
<red_phase>

### Green 階段 - <timestamp>
<!--
請記錄測試通過的詳細資訊（由 tdd-green 追加）。

Example:
- **狀態**: 🟢 通過
- **實作摘要**: 新增 `authenticateUser` 函數於 `src/auth/service.ts`
- **重試次數**: 1
-->
<green_phase>

### Refactor 階段 - <timestamp>
<!--
請記錄重構的詳細資訊（由 tdd-refactor 追加，可選）。

Example:
- **狀態**: ♻️ 已優化
- **優化項目**: 抽取驗證邏輯至 `src/auth/validator.ts`，提升可測試性
- **重試次數**: 0
-->
<refactor_phase>