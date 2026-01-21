# 文件完成度檢查表

## 概述

本文件追蹤 h8 專案架構文件的完成狀態。

---

## 文件清單

### ✅ 已完成

| 文件 | 說明 | 閱讀順序 | 狀態 |
|-----|------|---------|------|
| [spec.md](spec.md) | 專案規格書 | 1 | ✅ 完成 |
| [permissions.md](permissions.md) | 權限機制設計 | 2 | ✅ 完成 |
| [data.md](data.md) | 資料存取層設計 | 3 | ✅ 完成 |
| [writing-guide.md](writing-guide.md) | 文件撰寫指南 | - | ✅ 完成 |
| [checklist.md](checklist.md) | 文件完成度檢查表 | - | ✅ 完成 |

---

### 🔴 核心文件（必要）

| 文件 | 說明 | 閱讀順序 | 狀態 | 優先順序 |
|-----|------|---------|------|---------|
| [core-plugin-guide.md](core-plugin-guide.md) | Core Plugin 開發指南（平台方） | 4 | 🚧 進行中 | P0 |
| `plugin-dev-guide.md` | Plugin 開發指南（第三方廠商） | 5 | ⬜ 未開始 | P0 |
| `plugin-lifecycle.md` | Plugin 上架流程 | 6 | ⬜ 未開始 | P0 |
| `api-spec.md` | API 規範 | 7 | ⬜ 未開始 | P1 |
| `database-spec.md` | 資料庫規範 | 8 | ⬜ 未開始 | P1 |

#### core-plugin-guide.md 應包含

- [ ] Core Plugin 架構概覽
- [ ] 認證模組實作（Token 驗證、current_user 提供）
- [ ] 權限模組實作（Rolify 整合、RoleRegistry API）
- [ ] DAL 實作（DataAccess::API 介面設計）
- [ ] 對外介面規格定義
- [ ] 與業務 Plugin 的邊界

#### plugin-dev-guide.md 應包含

- [ ] Plugin 目錄結構
- [ ] 命名規範（Module、Table、Route）
- [ ] Engine 設定範例
- [ ] 如何使用 `current_user`
- [ ] 如何宣告角色需求
- [ ] 如何撰寫 Pundit Policy
- [ ] 如何存取自有 Table
- [ ] 如何透過 DAL 存取共用資料
- [ ] 如何與其他 Plugin 交換資料
- [ ] 完整範例 Plugin

#### plugin-lifecycle.md 應包含

- [ ] Plugin 註冊流程
- [ ] 審核標準
- [ ] 版本管理規範
- [ ] 更新流程
- [ ] 下架條件與流程
- [ ] 違規處置辦法

#### api-spec.md 應包含

- [ ] RESTful 設計規範
- [ ] URL 命名規則
- [ ] Request/Response 格式
- [ ] 錯誤回應格式（統一錯誤碼）
- [ ] API 版本控制策略
- [ ] 分頁規範
- [ ] 認證 Header 格式

#### database-spec.md 應包含

- [ ] Table 命名規範（prefix 規則）
- [ ] Migration 撰寫規範
- [ ] 共用 Schema 說明
- [ ] Index 建立指南
- [ ] 外鍵使用規範

---

### 🟡 重要文件（建議有）

| 文件 | 說明 | 閱讀順序 | 狀態 | 優先順序 |
|-----|------|---------|------|---------|
| `frontend-spec.md` | 前端架構規範 | - | ⬜ 未開始 | P2 |
| `deployment.md` | 部署架構 | - | ⬜ 未開始 | P2 |
| `testing-guide.md` | 測試策略 | - | ⬜ 未開始 | P2 |
| [logging.md](core-plugin/logging/logging.md) | Logging 模組設計 | - | ✅ 完成 | P2 |

#### frontend-spec.md 應包含

- [ ] Hotwire (Turbo + Stimulus) 使用規範
- [ ] 共用 UI 元件
- [ ] 樣式指南 (CSS/Tailwind)
- [ ] JavaScript 撰寫規範
- [ ] 與後端 Plugin 的整合方式

#### deployment.md 應包含

- [ ] Docker 映像檔規範
- [ ] Kamal 部署設定
- [ ] 環境變數管理
- [ ] 水平擴展策略
- [ ] 資料庫備份與還原

#### testing-guide.md 應包含

- [ ] 單元測試規範
- [ ] 整合測試規範
- [ ] E2E 測試規範
- [ ] 測試覆蓋率要求
- [ ] Mock/Stub 使用指南

#### logging.md 應包含

- [x] Log 格式規範
- [x] Log 等級定義
- [x] 監控與告警設定
- [x] 稽核日誌規範
- [x] Request Tracing
- [x] 安全性考量（敏感資料過濾）
- [x] Plugin 整合方式

---

### 🟢 可選文件（視需求）

| 文件 | 說明 | 閱讀順序 | 狀態 | 優先順序 |
|-----|------|---------|------|--------|
| `dev-setup.md` | 開發環境建置 | - | ⬜ 未開始 | P3 |
| `cicd.md` | CI/CD 流程 | - | ⬜ 未開始 | P3 |
| `security.md` | 安全規範 | - | ⬜ 未開始 | P3 |
| `performance.md` | 效能指南 | - | ⬜ 未開始 | P3 |

---

## 進度總覽

```
已完成：██████████░░░░░░░░░░ 6/19 (32%)

核心文件：░░░░░░░░░░░░░░░░░░░░ 0/5
重要文件：█████░░░░░░░░░░░░░░░ 1/4
可選文件：░░░░░░░░░░░░░░░░░░░░ 0/5
```

---

## spec.md 待確認事項

對應 `spec.md` 底部的待確認清單：

| 項目 | 對應文件 | 狀態 |
|-----|---------|------|
| 資料存取層實作細節 | `data.md` | ✅ 已完成 |
| Plugin 註冊與發布流程 | `plugin-lifecycle.md` | ⬜ 未開始 |
| 開發環境建置指南 | `dev-setup.md` | ⬜ 未開始 |
| CI/CD 流程 | `cicd.md` | ⬜ 未開始 |

---

## 下一步行動

1. **立即**：撰寫 `core-plugin-guide.md`（定義 Core Plugin 介面）
2. **接續**：撰寫 `plugin-dev-guide.md`（基於 Core Plugin 介面）
3. **本週**：完成 `plugin-lifecycle.md`、`api-spec.md`
4. **下週**：完成 `database-spec.md`、開始 P2 文件

---

*最後更新：2026-01-20*
