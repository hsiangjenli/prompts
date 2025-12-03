---
mode: agent
description: 查看目前任務狀態，決定下一步要執行哪個 prompt
inputs:
  summary: 檢查任務進度，決定下一步
  required:
    - 任務清單（從 requirements.prompt.md 產出）
    - 或提供專案 GitHub repo 來讀取現有 Issues
outputs:
  summary: 顯示任務狀態，並給出明確的「下一步指令」
  include:
    - 任務狀態總覽
    - 下一個要執行的 prompt 指令
---

# 任務進度追蹤

## 核心原則

> **快速檢查進度，明確告訴使用者「現在該執行哪個 prompt」**

## 目的

幫助使用者在 BDD → SDD → TDD 流程中追蹤進度，並決定下一步要做什麼。

---

## 自動檢查流程

### Step 1：讀取現有 Issues

透過 MCP 讀取專案中的 Issues，分類整理：

```
1. 搜尋所有 TASK-XXX 相關的 Issues
2. 根據標題前綴分類：
   - TASK-XXX：BDD Issue
   - SDD-TASK-XXX：SDD Issue
   - TDD-TASK-XXX：TDD Issue
3. 檢查 labels 確認狀態
```

### Step 2：產出狀態總覽

**輸出格式：**

```markdown
## 📋 任務狀態總覽

| 任務 ID | BDD Issue | SDD Issue | TDD Issue | 目前狀態 |
|---------|-----------|-----------|-----------|----------|
| TASK-001 | #1 ✅ | #3 ✅ | #5 🔴 | Red 階段 |
| TASK-002 | #2 ✅ | - | - | 等待 SDD |
| TASK-003 | - | - | - | 等待 BDD |

### 依賴關係
- TASK-002 依賴 TASK-001
- TASK-003 依賴 TASK-001
```

### Step 3：決定下一步

根據狀態決定建議的下一步：

| 狀態 | 建議 Prompt |
|------|-------------|
| 無 BDD Issue | `@workspace /requirements.prompt.md` |
| BDD 完成，無 SDD | `@workspace /sdd.prompt.md` |
| SDD 完成，無 TDD | `@workspace /tdd-requirements.prompt.md` |
| TDD 建立，需要 Red | `@workspace /tdd-red.prompt.md` |
| Red 完成，需要 Green | `@workspace /tdd-green.prompt.md` |
| Green 完成，需要 Refactor | `@workspace /tdd-refactor.prompt.md` |
| 任務完成 | 進入下一個任務 |

---

## 輸出模板

```markdown
---

## 📋 任務狀態總覽

| 任務 ID | 階段 | Issue | 狀態 |
|---------|------|-------|------|
| TASK-001 | TDD | #5 | 🔴 Red 進行中 |
| TASK-002 | BDD | #2 | ⏸️ 等待 TASK-001 |

---

## 🎯 下一步建議

### 選項 A：繼續 TASK-001（推薦）
執行 `@workspace /tdd-green.prompt.md` 並提供：
- TDD Issue 編號：#5
- 任務 ID：TASK-001

### 選項 B：開始 TASK-002
先完成 TASK-001 後再開始（有依賴關係）

---

## 💡 快速指令

複製以下指令繼續：
```
@workspace /tdd-green.prompt.md
TDD Issue: #5
任務 ID: TASK-001
```

---
```

---

## 特殊情境處理

### 情境 1：第一次使用

若專案沒有任何 TASK Issues：

```markdown
## 🆕 開始新專案

專案中尚無任務，建議從需求分析開始：

執行 `@workspace /requirements.prompt.md`

請描述你想要實作的功能。
```

### 情境 2：任務卡住

若某個任務長時間未更新：

```markdown
## ⚠️ 任務狀態檢查

TASK-001 已在 Red 階段停留 3 天，可能的原因：
1. 測試難以通過 → 考慮拆分任務
2. 依賴問題 → 檢查是否有阻塞
3. 需求變更 → 回到 BDD 更新

建議：執行 `@workspace /tdd-red.prompt.md` 繼續，或回到 `@workspace /requirements.prompt.md` 重新檢視
```

### 情境 3：需求變更

若使用者提到需求變更：

```markdown
## 🔄 需求變更流程

1. 回到 `@workspace /requirements-change.prompt.md`
2. 更新受影響的 BDD Issue
3. 重新走 SDD → TDD 流程

⚠️ 已完成的任務可能需要重新測試
```
