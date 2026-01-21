# Caching 模組設計

> 版本：0.1 (Draft)
> 更新日期：2026-01-21

---

## 概述

Caching 模組旨在透過快取機制提升 `h8` 生態系的整體效能，降低資料庫負載，並提供 Plugin 開發者統一且安全的快取存取介面。

### 設計目標

| 目標 | 說明 |
|------|------|
| 統一介面 | 封裝底層實作，提供簡單一致的 API (基於 Rails Cache) |
| 命名空間隔離 | 強制隔離 Core 與各 Plugin 的快取，避免 Key 衝突與誤刪 |
| 多樣化策略 | 支援由簡單的 Key-Value 到複雜的 Tag-based 清除策略 |
| 可配置 | 支援切換不同的 Backend (Redis, Memory, File, Null) |
| 監控與統計 | 追蹤 Cache Hit/Miss Rate，識別效能瓶頸 |

---

## 架構設計

系統基於 Rails `ActiveSupport::Cache` 進行擴充，主要增加了**命名空間管理**與**Plugin 輔助方法**。

```mermaid
flowchart TB
    App[Rails 主程式] -->|H8.cache| CacheManager
    PluginA[Plugin A] -->|plugin.cache| CacheManager
    PluginB[Plugin B] -->|plugin.cache| CacheManager
    
    subgraph CacheModule [Caching 模組]
        CacheManager[Cache Manager]
        Namespace[Namespace Handler]
        Serializer[Serializer (Marshal/JSON)]
    end
    
    CacheManager --> Namespace
    Namespace --> Store
    
    subgraph Backend [Cache Store]
        Store{選擇儲存與介面}
        Redis[(Redis)]
        Memcached[(Memcached)]
        Memory[MemoryStore]
    end
    
    Store --> Redis
    Store --> Memcached
    Store --> Memory
```

---

## 命名空間策略 (Namespacing)

為了防止 Plugin 之間的 Key 衝突，所有快取 Key 都會自動加上前綴。

### Key 格式

`h8:<scope>:<plugin_name>:<version>:<custom_key>`

| 區段 | 說明 | 範例 |
|------|------|------|
| `scope` | 範圍識別 | `core` 或 `plugins` |
| `plugin_name` | Plugin 名稱 | `auth`, `ecommerce` |
| `version` | 資料版本 (用於全面失效) | `v1` |
| `custom_key` | 開發者定義的 Key | `user:1:permissions` |

### 實例

*   **Core**: `h8:core:auth:v1:user:123`
*   **Plugin A**: `h8:plugins:plugin_a:v1:products:featured`

---

## API 設計

### 核心介面 (Core / Global)

直接使用 `H8.cache` 存取，主要用於核心功能或跨 Plugin 資料。

```ruby
# 寫入 (預設 TTL 依配置)
H8.cache.write('global_announcement', 'Maintenance at 12:00', expires_in: 1.hour)

# 讀取
H8.cache.read('global_announcement')

# Fetch (讀取，若無則執行區塊並寫入) - **推薦用法**
users = H8.cache.fetch('heavy_query_result', expires_in: 5.minutes) do
  User.includes(:roles).load
end

# 清除
H8.cache.delete('global_announcement')
H8.cache.clear # 清除整個 h8 命名空間下的所有快取 (慎用)
```

### Plugin 專屬介面

Plugin 開發者應使用 `H8::Plugin` 提供的 `cache` 輔助物件，系統會自動處理命名空間。

```ruby
module H8
  module PluginA
    class ProductService
      # 假設在 Plugin A 的 Context 下
      
      def featured_products
        # 實際 Key: h8:plugins:plugin_a:v1:featured_products
        H8.plugin(:plugin_a).cache.fetch('featured_products', expires_in: 30.minutes) do
          Product.where(featured: true).to_a
        end
      end
      
      def clear_cache
        # 僅清除 Plugin A 的快取，不影響 Core 或其他 Plugin
        H8.plugin(:plugin_a).cache.clear_namespace!
      end
    end
  end
end
```

---

## 快取層級與情境

| 層級 | 適用情境 | 實作方式 |
|------|---------|---------|
| **HTTP Caching** | 靜態資源、公開 API 回應 | `ETag`, `Last-Modified` Headers |
| **Page Caching** | 極少變動的公開頁面 (如首頁) | Nginx/CDN 層級 (不進 Rails) |
| **Action Caching** | 需過濾器 (如驗證) 的頁面 | Rails `caches_action` (需引入 gem) |
| **Fragment Caching** | 頁面中的區塊 (如側邊欄、選單) | View 層級 `cache @product do ... end` |
| **Low-Level Caching** | **主要目標**，資料庫查詢結果、API 呼叫結果 | `H8.cache.fetch` |
| **Request Caching** | 同一 Request 內的重複查詢 | Rails `ActiveSupport::CurrentAttributes` 或 Instance Var |

---

## 失效策略 (Invalidation)

快取最困難的是「何時清除」。我們採納以下策略：

### 1. TTL (Time To Live) 優先
所有快取**必須**設定過期時間。
*   預設：1小時
*   短期：1-5分鐘 (高併發計數器)
*   長期：24小時 (設定檔、靜態選單)

### 2. Key-based Expiration (Russian Doll Caching)
利用物件的 `updated_at` 時間戳作為 Key 的一部分。當物件更新，Key 改變，舊快取自然淘汰 (LRU)。

```ruby
# Rails View Helper 自動支援
cache(@product) do 
  # Key 包含 @product.updated_at
end
```

### 3. 事件驅動清除 (Event-based)
當資料變更時，主動清除相關 Key。

```ruby
class Product < ApplicationRecord
  after_commit :clear_cache
  
  def clear_cache
    H8.plugin(:ecommerce).cache.delete("product:#{id}")
    H8.plugin(:ecommerce).cache.delete("featured_products")
  end
end
```

---

## 配置與後端

### 支援的 Backend

透過 `config/h8.yml` 或 `ENV` 配置：

1.  **Redis (Production 推薦)**
    *   效能好，支援集群，支援 `keys` 指令 (用於 namespace 清除)。
    *   Env: `H8_REDIS_URL`
2.  **Memcached**
    *   替代方案，但不支援 namespace 批次清除 (需依賴 Key 前綴技巧)。
3.  **MemoryStore**
    *   Development / Test 環境預設。
4.  **NullStore**
    *   停用快取時使用。

### 參數配置

設定值由 [Configuration 模組](../configuration/configuration.md) 管理。

| Key | 預設值 | 說明 |
|-----|-------|------|
| `caching.adapter` | `redis` (prod) / `memory` (dev) | 快取後端 |
| `caching.default_ttl` | 3600 (1小時) | 預設過期時間 |
| `caching.namespace` | `h8` | 全域 Key 前綴 |
| `caching.redis_url` | `redis://localhost:6379/0` | Redis 連線字串 |

---

## 待解議題

1.  **Tag-based Caching**: Rails 預設不支援 Tag，但有些 Gem (如 `identity_cache`) 支援。是否需要引入？
    *   *目前決策：暫不引入，先以 Key-based 為主，保持簡單。*
2.  **Race Condition (Cache Stampede)**: 當高流量 Key 失效瞬間，大量請求穿透 DB。
    *   *對策：使用 `ActiveSupport::Cache` 的 `race_condition_ttl` 選項。*
3.  **序列化效能**: `Marshal` vs `JSON` vs `MessagePack`。
    *   *目前決策：預設使用 Rails 標準 `Marshal`，若有跨語言需求改成 `JSON`。*
