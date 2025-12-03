---
mode: agent
description: 將單一 BDD 任務轉換為技術規格，一次只處理一個任務
inputs:
  summary: 針對單一 BDD Issue 進行技術設計
  required:
    - BDD Issue 編號（必填）
    - 任務 ID（例如 TASK-001）
outputs:
  summary: 產出該任務的技術規格與「下一步指令」
  include:
    - 單一 SDD Issue（對應該 BDD Issue）
    - 明確的「下一步指令」
---

# 系統設計（SDD 導向）

## 核心原則

> **一次只為一個 BDD Issue 建立對應的 SDD，並明確告訴使用者「下一步做什麼」**

## 目的

將 BDD 的使用者故事轉換為具體的技術規格，但保持「單一任務」的專注度。

---

## Phase 1：讀取 BDD Issue

### Step 1.1：取得 BDD 內容

1. 透過 MCP 讀取指定的 BDD Issue
2. 確認 BDD Issue 已被 approve（有 `approved` label）
3. 提取關鍵資訊：
   - 任務 ID
   - 使用者故事
   - 驗收條件（Gherkin Scenarios）

### Step 1.2：檢查前置條件

```
⚠️ 若 BDD Issue 未被 approve，停止並提醒：
「請先將 BDD Issue #XX 加上 `approved` label 後再繼續」
```

---

## Phase 2：技術設計（精簡版）

### Step 2.1：快速技術提問

只問**必要**的技術問題（最多 3-5 題）：

```
① 這個功能的主要技術介面是什麼？
   A) REST API
   B) GraphQL
   C) 事件驅動 (Event)
   D) 函數呼叫 (Library)

② 需要持久化資料嗎？
   A) 是，需要資料庫
   B) 否，僅記憶體/快取
   C) 使用現有資料表

③ 有外部依賴嗎？
   A) 無
   B) 有，第三方 API
   C) 有，內部微服務
```

### Step 2.2：產出精簡 SDD

**SDD Issue 格式（精簡版）：**

```markdown
## Issue 標題
`SDD-TASK-001 - [技術設計主題]`

## 對應 BDD Issue
- BDD Issue: #XX
- 任務 ID: TASK-001

## 技術規格

### 介面設計
- 類型：[REST API / GraphQL / Event / Function]
- 端點/方法：[具體描述]

### 輸入/輸出
| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| ... | ... | ... | ... |

### 錯誤處理
| 錯誤碼 | 情境 | 回應 |
|--------|------|------|
| ... | ... | ... |

### 驗證方式
- [ ] 單元測試覆蓋主要邏輯
- [ ] 整合測試覆蓋 API 端點

## 完成定義
- [ ] 介面設計已確認
- [ ] 測試策略已定義
```

---

## Phase 3：建立 SDD Issue 與關聯

### Step 3.1：建立 Issue

1. 透過 MCP / GitHub API 建立 SDD Issue
2. 將 SDD Issue 設為 BDD Issue 的 Sub-Issue

### Step 3.2：更新任務狀態

更新任務清單中該任務的狀態：

| 任務 ID | BDD Issue | SDD Issue | 狀態 |
|---------|-----------|-----------|------|
| TASK-001 | #XX | #YY | 🚀 SDD 完成，進入 TDD |

---

## Phase 4：明確的下一步指令

### 輸出模板

每次回覆結尾**必須**包含：

```markdown
---

## ✅ 本次完成
- 建立了 SDD Issue: `SDD-TASK-001 - [主題]` (#Issue編號)
- 已關聯至 BDD Issue #XX

## 🎯 下一步
執行 `@workspace /tdd-requirements.prompt.md` 並提供：
- BDD Issue 編號：#XX
- SDD Issue 編號：#YY
- 任務 ID：TASK-001

## 📋 目前任務狀態
| 任務 ID | BDD | SDD | TDD | 狀態 |
|---------|-----|-----|-----|------|
| TASK-001 | #XX | #YY | - | 🚀 準備進入 TDD |
| TASK-002 | - | - | - | ⏸️ 等待 TASK-001 |

---
```

---

## 禁止事項

❌ **禁止**一次處理多個 BDD Issue
❌ **禁止**在沒有 approved BDD Issue 的情況下開始設計
❌ **禁止**沒有明確「下一步」就結束回覆
❌ **禁止**產出過於複雜的技術規格（保持精簡）

---

## 狀態追蹤

完成 SDD 後，任務狀態應更新為：

| 階段 | 狀態符號 | 說明 |
|------|----------|------|
| BDD 完成 | ✅ BDD | BDD Issue 已建立並 approve |
| SDD 進行中 | 🚀 SDD | 正在進行技術設計 |
| SDD 完成 | ✅ SDD | SDD Issue 已建立 |
