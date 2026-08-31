# 箱子檔案結構

一個箱子就是 `plugins/CineCrates/crates/` 裡的一份檔案。檔名就是箱子 id：
`starter.yml` 就是箱子 `starter`。

**只有兩項是必填**——`key:` 與 `rewards:`。其餘都有預設值，省略某個區段就是採用
文件所述的預設行為。

```yaml
display-name: '&6Example Crate'      # 選單、訊息與全息文字裡顯示的名字
icon:                                # 選用:在 /crate admin 清單裡的圖示
  material: ENDER_CHEST

animation: cinematic                 # 開箱怎麼演
open-location: placement             # 在哪裡演

key-mode: item                       # item | virtual | both
key:                                 # 必填
  material: TRIPWIRE_HOOK
  model-data: 113
  display-name: '&6Example Key'
  lore: ['&7Used to open the Example Crate']

model: gun_crate_1                   # 模型外掛的 id;方塊型箱子不需要
hologram:
  - '&6Example Crate'
  - '&7Right-click to open'

rewards:                             # 必填
  diamonds:
    weight: 18
    message: '&fYou received &b5 Diamonds'
    give:
      '1': { material: DIAMOND, amount: 5 }
```

## 各區段依序說明

| 區段 | 決定什麼 | 頁面 |
| --- | --- | --- |
| `display-name` | 箱子在各處顯示的名字 | — |
| `icon` | 管理清單裡的圖示。未設時用鑰匙物品。 | 見下方 |
| `permission` | 誰可以使用它 | [點擊與權限](interactions.md) |
| `animation` | 播放十種樣式中的哪一種 | [選擇動畫](../animations/overview.md) |
| `open-location` | 在箱子原地，或在運鏡房 | [運鏡演出](../animations/cinematic.md) |
| `interact-mode` / `interactions` | 每種點擊做什麼 | [點擊與權限](interactions.md) |
| `key-mode` / `key` / `price` | 開一次要付出什麼 | [鑰匙、計價與限制](keys-pricing-limits.md) |
| `limits` / `restrictions` | 什麼情況拒絕開箱 | [鑰匙、計價與限制](keys-pricing-limits.md) |
| `rewards` / `rewards-per-open` / `milestones` | 開出什麼 | [獎勵與機率](rewards.md) |
| `reroll` | 付費再抽一次 | [重抽](reroll.md) |
| `model` / `block` / `hologram` / `idle-animation` | 在世界上長什麼樣 | [模型](../models/model-plugins.md) |
| `cinematic` | 演出、時間點與獎勵呈現 | [運鏡演出](../animations/cinematic.md) |
| `orbit` | `roulette` 與 `spiral` 的環圈大小 | [選擇動畫](../animations/overview.md) |
| `preview` | 預覽介面的版面 | 見下方 |

## 物品的寫法,到處都一樣

只要是外掛接受物品的地方——鑰匙、獎勵、管理圖示、預覽按鈕——寫法都相同：

```yaml
key:
  material: TRIPWIRE_HOOK
  model-data: 113
  display-name: '&6Example Key'
  lore: ['&7第一行', '&7第二行']
  amount: 1
```

或是來自其他外掛的 id：

```yaml
key:
  custom-item: itemsadder:myitems:ruby_key
```

支援的前綴見[自訂物品](../reference/api.md#自訂物品)。

## 管理清單的圖示

```yaml
icon:
  material: ENDER_CHEST
  model-data: 7
```

不設 `icon:` 時，箱子在 `/crate admin` 裡會顯示它的**鑰匙物品**。這在幾個箱子共用
同一把鑰匙、或使用虛擬鑰匙時就會變成一整排長得一樣的東西。設了 `icon:` 就能讓每個
箱子有自己的臉。

也可以在遊戲中設定：在 `/crate admin` 開啟該箱子，手持物品按清單圖示按鈕。
Shift+左鍵清除。

## 預覽介面

```yaml
preview:
  title: '&8%crate% — rewards'
  rows: 6
  slots: [10, 11, 12, 13, 14, 15, 16]
  filler:
    material: GRAY_STAINED_GLASS_PANE
    name: ' '
  buttons:
    open:  { slot: 49, material: LIME_DYE }
    prev:  { slot: 45, material: ARROW }
    next:  { slot: 53, material: ARROW }
  custom:
    - { slot: 4, material: PAPER, name: '&e伺服器商店', command: 'store' }
```

這個畫面上的機率是**從權重算出來的**，不是你填的數字，所以不可能跟實際掉落率
脫節——玩家看到的樣子見[獎勵與機率](rewards.md)。

## 編輯之後

```
/crate reload
```

解析失敗的箱子會在主控台被指名並跳過，其他箱子照常運作。載入時的警告也涵蓋了那些
否則會靜默發生的錯誤——發不出東西的獎勵、已經失效的 custom-item id、以及動畫與
`rewards-per-open` 組合起來無法把每一件都演出來的情況。
