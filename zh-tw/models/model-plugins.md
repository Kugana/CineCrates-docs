# BetterModel 與 ModelEngine

兩者都支援、都是選用的，而且外掛會自動挑一個。

```yaml
# config.yml
model-backend: auto      # auto | bettermodel | modelengine | none
```

`auto` 會優先使用已安裝的那一個。選擇結果會在啟動時回報：

```
[CineCrates] Model backend: BetterModel.
```

## 使用模型

```yaml
model: chest_t3           # 模型外掛認得的模型 id
idle-animation: idle      # 箱子待在那裡時播放
cinematic:
  open-animation: open    # 箱子打開時播放
```

`idle-animation` 值得明確設定，而不要依賴模型自己的預設狀態——沒有預設狀態的模型
否則會呆立不動。

## Hitbox

這一節**只跟左鍵綁定有關**。

左鍵是一次攻擊，而承載模型的實體是一個原版攻擊碰不到的隱形標記。點擊必須改由模型
自己的 hitbox 抵達。

| 後端 | Hitbox |
| --- | --- |
| **ModelEngine** | 自動建立。不需要做任何事。 |
| **BetterModel** | 會替名為 `hitbox` 的骨骼、或帶 `b_` / `ob_` 前綴的骨骼（例如 `b_hitbox`）建立。 |

兩者皆無的 BetterModel 模型不會有 hitbox 實體，所以沒有東西可以被打中，左鍵完全
沒有反應。外掛會在啟動時直接講出來，不會讓你亂猜：

```
[CineCrates] Model chest_t3 has no hitbox bone, so left-click will do nothing.
BetterModel looks for a bone named b_… or ob_…. Bones found: main, group, top
```

修法是在 Blockbench 裡開啟模型，加一個名為 `hitbox` 的群組，裡面放一顆與箱子等大
的**無貼圖**方塊，然後重新載入模型外掛。右鍵永遠不依賴 hitbox。

{% hint style="info" %}
從 ModelEngine 轉過來的模型通常已經帶著一個名為 `hitbox` 的骨骼，那個 BetterModel
是認得的。上面那個警告只在模型**既沒有那個名稱、也沒有那個前綴**時才會出現。
{% endhint %}

## 個人可見性

開箱是**個人的演出**。為某位玩家佈置的箱子模型不會顯示給其他人，所以兩個玩家同時
開同一個箱子，不會看到對方的動畫與獎勵物品。這在兩種後端上都已經處理好了。

## 模型載入失敗時

如果模型 id 解析不出來——打錯字，或模型外掛還沒匯入它——外掛會記錄一則指名箱子與
id 的警告，並在該處退回一個**看得見的**標記盔甲架。這是刻意的：隱形的失敗看起來
會像箱子憑空消失，而一具裸盔甲架能讓你一眼看出模型沒載到，玩家也還是點得到它。

## 兩個外掛都沒裝

一切照常運作。改用[方塊型箱子](block-crates.md)。
