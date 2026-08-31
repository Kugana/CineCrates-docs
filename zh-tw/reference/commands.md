# 指令

別名：`/cinecrates`、`/crates`

## 玩家

| 指令 | 功能 |
| --- | --- |
| `/crate list` | 列出你看得到的箱子 |
| `/crate preview <crate>` | 附真實機率的獎勵一覽 |
| `/crate odds <crate>` | 同樣的機率，文字版 |
| `/crate open <crate>` | 依設定的代價開箱 |
| `/crate keys` | 你自己的虛擬鑰匙餘額 |
| `/crate help` | 玩家說明，依權限裁切 |

## 鑰匙

| 指令 | 功能 |
| --- | --- |
| `/crate key give <player> <crate> [n] [item]` | 發放鑰匙——**玩家離線時也有效** |
| `/crate key take <player> <crate> [n]` | 扣除虛擬鑰匙 |
| `/crate key set <player> <crate> <n>` | 設定虛擬餘額 |
| `/crate key check <player> <crate>` | 查詢某位玩家的餘額 |

`give` 預設發放虛擬鑰匙。在 `both` 的箱子上，結尾加 `item` 才會給實體鑰匙。
`take`、`set`、`check` 只會動到虛擬餘額——實體鑰匙在背包裡。

因為 `give` 對離線玩家有效、也能從主控台執行，網頁商店可以直接呼叫：

```
crate key give %player% vip 1
```

## 管理

| 指令 | 功能 |
| --- | --- |
| `/crate admin` | 開啟編輯器介面 |
| `/crate admin create <id> [name]` | 建立箱子 |
| `/crate admin info <crate>` | 單一箱子的設定摘要 |
| `/crate admin setkey <crate>` | 用手上的物品設定鑰匙 |
| `/crate admin addreward <crate> [weight]` | 把手上的物品加成獎勵 |
| `/crate admin delreward <crate> <reward>` | 移除獎勵 |
| `/crate admin weight <crate> <reward> <n>` | 改變獎勵權重 |
| `/crate admin setmodel <crate>` | 記錄模型位置——站在它該出現的地方 |
| `/crate admin setcam <crate>` | 記錄相機位置——站在它該看出去的地方 |
| `/crate admin modelid <crate> <id>` | 設定模型外掛的模型 id |
| `/crate admin anim <crate> <style>` | 更換動畫 |
| `/crate admin length <crate> <ticks>` | 更改運鏡長度 |
| `/crate admin skip <crate> <true\|false>` | 切換可否跳過 |
| `/crate admin cinematic <crate> <...>` | 運鏡的子設定 |
| `/crate admin mode <crate> <menu\|direct>` | 更改互動模式 |
| `/crate admin place <crate>` | 在你站的位置放置箱子 |
| `/crate admin unplace <placement-id>` | 移除一個放置點 |
| `/crate admin places` | 列出所有放置點 |
| `/crate reload` | 重新載入設定、訊息、箱子與放置點 |
| `/crate help admin` | 遊戲內的完整管理參考 |

{% hint style="info" %}
`unplace` 吃的是**放置點 id**，不是箱子 id。放置點 id 是自動編號的
`<箱子>_<序號>`——`starter_1`、`starter_2`。Tab 補全會列出它們，
`/crate admin places` 也會連座標一起印出來。
{% endhint %}

## 重新載入

`/crate reload` 會重讀 `config.yml`、語言檔、每一份箱子檔案與每一個放置點，並重建
已放置的模型與全息文字。**不需要重啟**。

解析失敗的箱子會在主控台被指名並跳過，其餘照常運作。
