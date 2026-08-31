# 安裝

## 需求

| | |
| --- | --- |
| **伺服器** | Spigot、Paper 或 Folia —— 1.21 到 26.x |
| **Java** | 21 以上。Minecraft 26.x 需要 Java 25——那是 Mojang 的要求，不是本外掛的。 |

以下全部是選用的，沒有它們外掛照樣運作。

| 外掛 | 提供什麼 |
| --- | --- |
| BetterModel **或** ModelEngine | 3D 動畫箱子模型。沒有的話用 `block:` 型箱子。 |
| Vault | `price: { type: money }` |
| PlaceholderAPI | `%cinecrates_*%` 佔位符 |
| ItemsAdder / Oraxen / Nexo / MMOItems / CustomItemGUI | 用自訂物品當鑰匙、獎勵與圖示 |

## 安裝步驟

1. 把 `CineCrates.jar` 放進 `plugins/`，啟動伺服器。
2. 外掛會產生 `plugins/CineCrates/`，內含 `config.yml`、`messages/` 資料夾，以及
   一個可以立刻開的 `example` 箱子。
3. 檢查主控台的啟動訊息。它會回報排程器（`Bukkit` 或 `Folia`）、選用的模型後端，
   以及載入了幾個箱子與放置點。

```
[CineCrates] Scheduler: Folia.
[CineCrates] Model backend: BetterModel.
[CineCrates] Loaded 1 crate(s).
[CineCrates] Loaded 0 placement(s).
```

如果模型後端顯示 `none`，代表沒有找到模型外掛——箱子仍然可用，但需要設定
`block:`（見[方塊型箱子](../models/block-crates.md)）。

## Folia

Folia 支援不是「能載入」而已。每一個實體、方塊與排程呼叫都會被導向擁有它的區域
執行緒，所以放置點、運鏡、動畫、資料庫寫入與 `/crate reload` 的行為都和 Paper
上一致。**沒有任何開關要開**——外掛在啟動時自動偵測 Folia 並切換排程器。

## 選擇語言

`config.yml`：

```yaml
language: en     # en | tw | cn | ja | es
```

五份語言檔都是**完整**的，不是留著英文空缺的半成品。檔案位於
`plugins/CineCrates/messages/`，所有面向玩家與管理員的字串都在裡面，包含獎勵
公布的文案——編輯你的語言檔後執行 `/crate reload` 即可。

## 資料庫

預設用 SQLite，不需要任何設定。若是群組伺服器，讓每一台指向同一個 MySQL：

```yaml
storage:
  type: mysql
  host: localhost
  port: 3306
  database: cinecrates
  username: root
  password: ''
  use-ssl: false
  pool-size: 8
```

兩種都走 HikariCP，而且 JDBC 驅動是在執行期下載而非打包進 jar——所以 jar 保持
輕巧，也不會有任何東西被 shade 進你的伺服器。

虛擬鑰匙餘額與開箱計數存在這個資料庫裡。如果連線池建立失敗，外掛會記錄錯誤並
**退回 SQLite**，而不是拒絕啟動。

## 更新

換掉 jar 再重啟即可。`config.yml` 與你的箱子檔案**永遠不會被覆蓋**——新選項會以
預設值補上，所以你沒設過的鍵行為如文件所述，設過的則原封不動。
