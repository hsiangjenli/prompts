---
name: README 模板盤點
about: 建立或補齊 README.md 的標準章節與草稿內容
title: "[README] 專案名稱 - 範本與交付檢查"
labels: documentation, readme
assignees: ''
---

## 任務背景
- 需求來源（Issue / PR / 會議記錄）：
- 目標交付日期或里程碑：
- README 適用範圍（新專案／子系統／重大改版）：
- 相關文件或設計稿連結：

## 必備章節清單
> 勾選已完成且確認內容的章節（🔵 已確認／🟡 需補充／🔴 待確認）。
- [ ] 🔵／🟡／🔴 專案名稱與徽章（標題、CI/測試狀態、NPM 版本等）
- [ ] 🔵／🟡／🔴 一句話摘要與專案定位
- [ ] 🔵／🟡／🔴 Overview／背景說明
- [ ] 🔵／🟡／🔴 核心功能／Use Cases（條列重點價值）
- [ ] 🔵／🟡／🔴 Tech Stack 與架構示意（若有圖請附檔或連結）
- [ ] 🔵／🟡／🔴 Getting Started（Prerequisites、Installation、Configuration、Running）
- [ ] 🔵／🟡／🔴 Usage 範例（CLI、API、UI 截圖、範例指令）
- [ ] 🔵／🟡／🔴 Project Structure 主要目錄導覽
- [ ] 🔵／🟡／🔴 Testing／品質保證流程
- [ ] 🔵／🟡／🔴 Deployment／CI/CD 注意事項
- [ ] 🔵／🟡／🔴 Contributing 指南與對應 Issue 模板
- [ ] 🔵／🟡／🔴 License（若開源請列出授權條款）
- [ ] 🔵／🟡／🔴 Contact／Maintainers（聯絡窗口與支援渠道）
- [ ] 🔵／🟡／🔴 Acknowledgements／參考資源

## README.md 草稿模板
> 直接複製以下 Markdown，依序填入內容，若章節不適用請註明原因並刪除占位符。

````markdown
# <PROJECT_NAME>
[![Status Badge](<BADGE_URL>)](<BADGE_LINK>) <!-- TODO: 可移除或替換 -->

> <ONE_SENTENCE_SUMMARY>

## Overview
- <WHAT_PROBLEM_IT_SOLVES>
- <TARGET_USERS>
- <CURRENT_STATUS>

## Core Features
1. <FEATURE_OR_USE_CASE_1>
2. <FEATURE_OR_USE_CASE_2>
3. <FEATURE_OR_USE_CASE_3>

## Tech Stack
| Layer | Technology | Purpose | Status |
| --- | --- | --- | --- |
| Frontend | <FRAMEWORK_OR_LIB> | <WHY_USED> | 🔵/🟡/🔴 |
| Backend | <LANG_OR_FRAMEWORK> | <WHY_USED> | 🔵/🟡/🔴 |
| Data / Storage | <DB_OR_WAREHOUSE> | <WHY_USED> | 🔵/🟡/🔴 |
| Tooling | <CI/CD_OR_LINT> | <WHY_USED> | 🔵/🟡/🔴 |

## Architecture
- 圖片：`docs/architecture.png`（若無可省略）
- 描述：<SYSTEM_OVERVIEW_PARAGRAPH>

## Getting Started
### Prerequisites
- <REQUIRED_RUNTIME>
- <PACKAGE_MANAGER>
- <ENVIRONMENT_VARIABLES>

### Installation
```bash
<INSTALL_COMMANDS>
```

### Configuration
- `.env.sample`：<ENV_VARS_DESCRIPTION>
- 其他設定：<CONFIG_NOTES>

### Running
```bash
<START_COMMAND>
```

## Usage
- CLI：`<CLI_EXAMPLE>`
- API：`POST /v1/example`
- UI：<SCREENSHOT_LINK_OR_DESCRIPTION>

## Project Structure
```
<PROJECT_TREE>
```

## Testing
```bash
<TEST_COMMANDS>
```
- 覆蓋率或品質門檻：<THRESHOLD>
- 其他測試資源：<LINKS>

## Deployment
- Pipeline / Workflow：<CI_CD_LINK>
- 部署環境：<ENVIRONMENTS>
- 回滾策略：<ROLLBACK_PLAN>

## Contributing
- 請先閱讀 `CONTRIBUTING.md`
- Issue 模板：BDD / SDD / TDD / README
- 開發腳本：`scripts/<TOOL>.sh`

## License
- <LICENSE_TYPE>（連結到 `LICENSE`）

## Contact
- Maintainers：[@username](mailto:maintainer@example.com)

## Acknowledgements
- <THANK_PEOPLE_OR_PROJECTS>
````

## 開放問題
1. 需要其他章節嗎？（例如 Roadmap、Security、Localization）
2. 是否需提供 README 的英文版／多語版本？
3. 其他待確認事項：

> 完成後請將 README.md 草稿、相關圖片或附檔上傳至 PR，並於合併前完成所有 🔵 項目。
