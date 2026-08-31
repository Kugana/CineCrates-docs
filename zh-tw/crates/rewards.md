# 獎勵與機率

```yaml
rewards:
  diamond_block:
    weight: 2
    broadcast: true                       # 全服公告
    message: '&fYou won a &bDiamond Block&f!'
    give:
      '1': { material: DIAMOND_BLOCK, amount: 1 }

  money:
    weight: 40
    message: '&fYou received &e1000 coins'
    display:                              # 動畫要展示的東西
      material: GOLD_NUGGET
      display-name: '&e1000 coins'
      lore: ['&7Paid straight into your balance']
    commands:
      - 'eco give %player% 1000'
```

| 鍵 | 意義 |
| --- | --- |
| `weight` | 相對權重。**不是百分比**——見下方。 |
| `give` | 要放進背包的物品，以編號 map 表示 |
| `commands` | 要執行的主控台指令。`%player%` 是領取者。 |
| `display` | 動畫展示的物品。未設時用 `give` 的第一件。 |
| `message` | 傳給開箱者的訊息 |
| `broadcast` | `true` 會全服公告 |

一個獎勵可以有 `give`、`commands` 或兩者皆有。**兩者皆無的獎勵會在載入時被標記**
——那會消耗一把鑰匙卻什麼都不給。

## 權重不是百分比

真實機率是 `該權重 ÷ 所有權重總和`。上面四個權重 `2 + 18 + 40 + 40 = 100`，數字
剛好讀起來像百分比，但那只是範例的巧合。再加一個權重 `100` 的獎勵，現有的每一個
機率都會**減半**。

這個算術你永遠不必自己做：

```
/crate odds starter
```

會印出算好的百分比，預覽介面也對玩家顯示同一組數字。

![預覽介面上顯示某個獎勵的真實機率](../../assets/preview.png)

提示框裡的那個機率是每次繪製畫面時從權重推算的。**沒有一個欄位讓你填數字**，
所以玩家看到的內容不可能跟箱子實際的行為脫節。

## 一次開出多件

```yaml
rewards-per-open: 3
```

或用加權，讓多數時候開出一件、幸運時開出五件：

```yaml
rewards-per-open:
  - { amount: 1, weight: 60 }
  - { amount: 3, weight: 30 }
  - { amount: 5, weight: 10 }
```

每一次抽取都是獨立的，所以同一個獎勵可能出現兩次。

每種動畫都會原生地把它們演出來：噴泉與雨的收尾會一次噴出全部、螺旋與光柱會一起
帶上去、轉盤會點亮相鄰的那麼多格、GUI 條會把它們排在指針底下。唯一的例外是
`three-toss`，它的舞台正好只有三個位置——會展示前三件、其餘照常發放，並在載入時
針對這個組合發出警告。

{% hint style="info" %}
一次開出多件的箱子**會跳過重抽介面**。「三件裡要重抽哪一件」是個複雜度遠高於其
價值的介面，而整組重抽又會讓夾在其中的保底獎消失。
{% endhint %}

## 保底:必得的獎勵

```yaml
milestones:
  tenth: { every: 10, reward: legendary }   # 每 10 次
  first: { at: 1,     reward: starter_kit } # 只在第 1 次
```

`every` 會重複；`at` 只在剛好那一次觸發。進度是**每位玩家、每個箱子**分別計算並
存進資料庫，所以重啟後仍在，共用 MySQL 時也能跨伺服器。

保底獎會佔用該次開箱的第一個位置。在一次開出多件的箱子上，其餘幾件照常在它旁邊
隨機抽出。

兩條值得知道的規則：

* 被拒絕的開箱**不會**推進計數——因為背包滿而被擋下，玩家不會有任何損失，包含進度。
* 重抽會放棄該次的保底獎。這是刻意的：保底是**保證**，不是可以重新進入的抽獎。

## 全服公告

`broadcast: true` 會把獎勵公告給全伺服器。只用在真正值得公告的少數幾個獎勵上。
公告的文案在你的語言檔裡，所以不動任何箱子就能改變它的樣子。
