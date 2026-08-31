# 鑰匙、計價與限制

## 鑰匙模式

```yaml
key-mode: item      # item | virtual | both
```

| 模式 | 行為 |
| --- | --- |
| `item` | 背包裡的實體鑰匙。 |
| `virtual` | 資料庫裡的餘額。死亡不會掉，且**離線也能發放**。 |
| `both` | 兩種都可以。優先扣虛擬的。 |

實體鑰匙是用外掛自己的 id 比對的，不是靠名稱或貼圖——所以替鑰匙物品換貼圖或改名
**不會讓已經流通在外的鑰匙失效**，玩家也無法靠合成一個外觀相似的物品來偽造。

```
/crate key give <player> <crate> [n] [item]
/crate key take <player> <crate> [n]
/crate key set  <player> <crate> <n>
/crate key check <player> <crate>
```

`give` 對離線玩家有效，所以網頁商店可以直接從主控台呼叫。在 `both` 的箱子上預設
發放虛擬鑰匙——結尾加上 `item` 才會給實體鑰匙。`take`、`set`、`check` 只會動到
虛擬餘額；實體鑰匙在背包裡，看得見。

玩家用 `/crate keys` 查自己的餘額。

## 不用鑰匙的計價方式

```yaml
price:
  type: money       # key | money | permission | free
  amount: 2500
```

| 類型 | 成本 |
| --- | --- |
| `key` | 一把鑰匙，依 `key-mode` 而定。省略 `price:` 時的預設。 |
| `money` | 透過 Vault 扣除 `amount`。 |
| `permission` | 持有該節點的人免費開——不用寫 `amount`，改寫 `node:`。 |
| `free` | 完全免費。 |

`money` 需要 Vault **以及**背後的經濟外掛。沒有的話扣款無法進行，開箱會**帶著
訊息被拒絕**，而不是靜默地成功。

## 限制誰能開

```yaml
permission: crates.vip
```

設了 `permission` 的箱子，會對沒有該節點的玩家拒絕**每一種互動**——點擊、預覽、
用指令開箱。這個檢查發生在扣費之前，而且方塊型箱子與模型箱子、不管用哪個滑鼠鍵，
行為都一致。

## 冷卻與每日上限

```yaml
limits:
  cooldown-seconds: 30
  daily: 5
```

`daily` 依伺服器自己的日界重置，按玩家與箱子分別儲存。兩者都在扣費之前檢查。

**重抽不會**重新檢查這兩項——因為那仍然是同一次開箱。

## 拒絕開箱

```yaml
restrictions:
  full-inventory: true              # 背包滿時拒絕,而不是把東西掉在地上
  world-blacklist: [world_nether, world_the_end]
```

這一節每一種拒絕都有一個重要性質：它發生在**扣費之前**。被擋下的開箱永遠不會消耗
鑰匙、不會扣款，也不會推進保底進度。背包滿的玩家不會有任何損失，並且會被告知
原因。

`full-inventory: false`（預設）會讓開箱照常進行，放不下的東西掉在玩家腳邊。
