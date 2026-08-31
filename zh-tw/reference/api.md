# 佔位符、事件與資料庫

## 自訂物品

鑰匙、獎勵與箱子圖示都接受來自其他物品外掛的 id，寫法到處都一樣：

```yaml
key:
  custom-item: itemsadder:myitems:ruby_key
```

| 前綴 | 外掛 |
| --- | --- |
| `itemsadder:` | ItemsAdder |
| `oraxen:` | Oraxen |
| `nexo:` | Nexo |
| `mmoitems:` | MMOItems —— `mmoitems:TYPE:ID` |
| `cig:` | CustomItemGUI |

比對是用各外掛自己的 id 查找，所以替物品換貼圖或改名**不會弄壞已存在的鑰匙**，
也沒有人能靠合成外觀相似的物品來偽造。

如果某個 id 解析不出來——物品被刪掉，或提供它的外掛不在——箱子會在載入時被標記，
指名箱子、獎勵與該 id，**而不是靜默地發出一個替代品**。

## PlaceholderAPI

| 佔位符 | 回傳 |
| --- | --- |
| `%cinecrates_keys_<crate>%` | 玩家的虛擬鑰匙餘額 |
| `%cinecrates_opens_<crate>%` | 他開過幾次 |
| `%cinecrates_daily_<crate>%` | 今日上限已用掉幾次 |
| `%cinecrates_cooldown_<crate>%` | 冷卻剩餘秒數 |
| `%cinecrates_chance_<crate>_<reward>%` | 該獎勵的真實機率，百分比 |

獎勵 id 可以包含底線——佔位符是**從右邊切**的，所以
`%cinecrates_chance_vip_diamond_block%` 能正確解析。

## 事件

```java
CrateOpenEvent      // 扣費之前 —— 可取消
CrateKeyUseEvent    // 消耗鑰匙之前 —— 可取消
CrateRewardEvent    // 發放獎勵時
CratePreviewEvent   // 開啟預覽介面時 —— 可取消
```

在任何東西被提交之前觸發的那幾個是可取消的，所以其他外掛可以否決一次開箱，
**而玩家不會損失鑰匙**。

```java
@EventHandler
public void onCrateOpen(CrateOpenEvent e) {
    if (isEventPaused()) {
        e.setCancelled(true);
    }
}
```

## API 類別

`CineCratesAPI` 提供配套外掛通常需要的唯讀查詢——箱子清單、某個箱子的獎勵與算好的
機率、玩家的虛擬鑰匙餘額，以及建構箱子的鑰匙物品以便發放。

## 資料庫

預設 SQLite；群組伺服器用 MySQL。兩者都走 HikariCP，而 JDBC 驅動是在執行期取得而非
shade 進 jar。

存在資料庫裡的：

* 虛擬鑰匙餘額
* 開箱計數，依玩家與箱子分別記錄
* 保底進度
* 每日上限計數與冷卻

存在磁碟上的：箱子定義（`crates/*.yml`）、放置點（`locations.yml`）、訊息，以及
開箱記錄（`open-log.csv`）。

如果連線池建立失敗，外掛會記錄錯誤並**退回 SQLite**，而不是拒絕啟動——所以資料庫
故障不會連帶讓伺服器的箱子一起停擺。
