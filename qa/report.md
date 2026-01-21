# 文件檢查報告

日期：2026-01-21
狀態：待修正

本報告針對專案中已完成的章節進行檢查，發現以下疏漏或潛在錯誤。請確認後再進行修正。

## 1. 連結路徑錯誤 (`docs/checklist.md`)

在 `docs/checklist.md` 中，部分文件連結因目錄結構調整而失效。

- **錯誤連結**：`[permissions.md](permissions.md)`
  - **實際位置**：`docs/core-plugin/permissions/permissions.md`
  - **建議修正**：更改為 `[permissions.md](core-plugin/permissions/permissions.md)`

- **錯誤連結**：`[data.md](data.md)`
  - **實際位置**：`docs/core-plugin/data/data.md`
  - **建議修正**：更改為 `[data.md](core-plugin/data/data.md)`

## 2. Mermaid 語法錯誤 (`docs/spec.md`)

（經再次確認，原文件中的 `subgraph` 已正確閉合，此項目為檢查誤報，無需修正。）


## 3. 文件狀態不一致 (`docs/checklist.md`)

`docs/checklist.md` 的狀態標記與實際檔案內容/列表有出入。

- **完成度標示不明**：
  - `core-plugin-guide.md` 標記為 `🚧 進行中`，但檔案已有完整架構與內容 (263 lines)，建議確認是否已可標記為完成。
  - `configuration.md`, `caching.md`, `event-bus.md` 已存在且有完整內容 (Draft 0.1)，但在 `checklist.md` 中未列出或未標記狀態。

## 4. 命名與結構一致性建議

- **檔名一致性**：
  - 目前大多數模組文件使用模組名稱命名 (如 `logging.md`, `permissions.md`)。
  - 認證模組使用 `docs/core-plugin/auth/README.md`。建議考量是否統一為 `auth.md`，或在目錄結構中保持 `README.md` Pattern (若該目錄下還有其他文件)。

- **術語一致性**：
  - `spec.md` 明確指定使用 Keycloak。
  - `auth/README.md` 在圖表中使用 generic term "OAuth2 Provider"，但在文字描述中提及 Keycloak。雖無大礙，但建議確認是否需統一強調 Keycloak 即為我們的 OAuth2 Provider 實作。

## 5. 其他潛在問題

- **docs/core-plugin/logging/logging.md**：
  - 內容提到 "Trace ID 規格" 中的 Header 為 `X-Trace-ID`。需確認這是否與 Kong 或 OpenTelemetry 標準 (如 `traceparent`) 相符，或僅為自訂標準。

## 6. 孤立文件 (`docs/core-plugin/logging/args.md`)

- **問題描述**：`docs/core-plugin/logging/args.md` 存在於目錄中，但在 `README.md` 或 `logging.md` 中似乎缺乏明顯的引用連結。
- **建議**：
  - 若為 `logging.md` 的補充文件，建議在 `logging.md` 中增加連結 (例如在「配置」章節)。
  - 或者將其內容整併入 `configuration.md` 或是 `logging.md` 中，避免碎片化。
