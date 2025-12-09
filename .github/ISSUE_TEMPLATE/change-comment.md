---
name: 變更追蹤 Comment
about: 用於記錄需求變更的審計軌跡，建立於 BDD Issue
---

## 變更追蹤紀錄

### 變更來源:
<!--
請說明此變更是從哪個層級發現的。
① BDD（需求本身不清）  ② SDD（設計有誤）  ③ TDD（測試無法進行）
-->
<change_source>

### 變更原因:
<!--
請說明觸發此變更的原因，包含背景脈絡與期望成果。

Example:
在 SDD 階段進行 API 設計時，發現原始 BDD Scenario US1-S2 未考慮到使用者未登入的情況，需補充失敗路徑。
-->
<change_reason>

### 影響範圍:
<!--
請列出此變更影響的項目，包含 BDD Scenario、SDD 規格、TDD 測試。

Example:
| 層級 | 受影響項目 | 變更類型 | 說明 |
| --- | --- | --- | --- |
| BDD | US1-S2 | 修改 | 補充未登入失敗路徑 |
| BDD | US1-S3 | 新增 | 新增 Token 過期情境 |
| SDD | S-REQ-001-US1 | 修改 | 更新錯誤碼定義 |
| TDD | REQ-001-T-102 | 修改 | 調整預期錯誤訊息 |
| TDD | REQ-001-T-105 | 新增 | 新增 Token 過期測試 |
-->
<impact_scope>

### 關聯 Issue:
<!--
請列出所有受此變更影響的 Issue 編號。

Example:
- SDD Issue: #12, #15
- TDD Issue: #23, #24
-->
<related_issues>