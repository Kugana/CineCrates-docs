# 點擊與權限

## 快速版

```yaml
interact-mode: menu     # menu(預設)| direct
```

`interact-mode` 決定四種點擊綁定的**預設值**：

| 點擊 | `menu` | `direct` |
| --- | --- | --- |
| 右鍵 | 預覽 | 開箱 |
| Shift + 右鍵 | 預覽 | 開箱 |
| 左鍵 | 預覽 | 預覽 |
| Shift + 左鍵 | 預覽 | 預覽 |

在 `menu` 模式下所有點擊都開啟預覽介面，玩家從介面裡的按鈕開箱。在 `direct` 模式
下右鍵直接開箱，預覽則保留在左鍵。

## 自己綁定每個鍵

```yaml
interactions:
  RIGHT: open
  SHIFT_RIGHT: instant
  LEFT: preview
  SHIFT_LEFT: none
```

可用動作：`open`、`instant`（跳過動畫）、`preview`、`none`。

這個區塊會與預設值**合併**——你沒寫的鍵位保留 `interact-mode` 給的值。只寫
`RIGHT: open` 就只會改變右鍵，其他不動。

`instant` 綁在 Shift 上很好用，適合要連開一整組鑰匙的玩家，因為它完全跳過動畫。

## 權限

```yaml
permission: crates.vip
```

設了 `permission` 節點的箱子，會對沒有它的玩家拒絕**每一個入口**——左鍵、右鍵、
兩種 Shift 變體、預覽介面，以及 `/crate open`。檢查發生在扣費之前。

{% hint style="info" %}
權限閘門位於每條點擊路徑都會經過的**同一個地方**，所以它對方塊型箱子、BetterModel
箱子與 ModelEngine 箱子的作用完全一致。沒有任何「模型後端 × 滑鼠鍵」的組合能繞過它。
{% endhint %}

## 模型箱子的左鍵

如果你的箱子使用 3D 模型，有一件事要知道。

左鍵是一次**攻擊**，而承載模型的實體是一個原版攻擊碰不到的隱形標記。外掛因此把
這個點擊改由模型外掛自己的 hitbox 轉送——這代表**模型必須定義 hitbox**，左鍵綁定
才會有作用。

* **ModelEngine** 會自己替模型建立 hitbox。不需要做任何事。
* **BetterModel** 會替名為 `hitbox` 的骨骼、或帶有 `b_` / `ob_` 前綴的骨骼
  （例如 `b_hitbox`）建立。**兩者皆無**的模型不會有 hitbox 實體，所以沒有東西
  可以被打中，左鍵完全沒有反應。

BetterModel 箱子的左鍵沒反應時，這幾乎一定是原因——見
[BetterModel 與 ModelEngine](../models/model-plugins.md#hitbox)。

右鍵**永遠不依賴** hitbox，方塊型箱子則兩個鍵都直接收得到，所以兩者都不受影響。
