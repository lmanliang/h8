# Logging 設定參數說明

> 這些參數將註冊在 Configuration 模組中，供系統與使用者調整。

## 參數列表

| Key | 預設值 | 類型 | 說明 | 建議來源 |
|-----|-------|------|------|---------|
| `logging.retention.app` | 90 | Integer | 應用程式日誌保留天數 | DB / File |
| `logging.retention.audit` | 365 | Integer | 稽核日誌 (Audit Log) 保留天數 | DB / File |
| `logging.level` | `info` | String | 日誌層級 (debug/info/warn/error) | ENV / DB |
| `logging.format` | `json` | String | 輸出格式 (json/text) | ENV / File |
| `logging.output` | `stdout` | String | 輸出目標 (stdout/file) | ENV |

## 設定範例 (config/h8.yml)

```yaml
logging:
  retention:
    app: 90
    audit: 365
  level: "info"
  format: "json"
```
