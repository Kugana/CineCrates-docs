# 建立第一個箱子

十分鐘，完全不用編輯 YAML。以下全部在遊戲中完成。

## 1. 建立箱子

```
/crate admin create starter Starter Crate
```

這會產生 `plugins/CineCrates/crates/starter.yml`，帶著合理的預設值，並開啟編輯器。

![箱子編輯畫面](../assets/admin-gui.png)

這裡每一個按鈕都會寫進箱子檔案，所以你在遊戲中設定的東西之後都能用手改，反過來
也一樣。

## 2. 加入獎勵

手持要送出的物品，在編輯器裡按 **add reward**，或是：

```
/crate admin addreward starter 40
```

那個 `40` 是**權重，不是百分比**——見[獎勵與機率](../crates/rewards.md)。每個獎勵
重複一次。獎勵 id 會從物品名稱推導，你不必自己想。

## 3. 設定鑰匙

手持要當鑰匙的物品，在編輯器裡按 **set key**。外掛是用它自己的 id 儲存的，所以
日後替物品換貼圖或改名，**都不會讓已經發出去的鑰匙失效**，也沒有人能靠合成一個
外觀相似的物品來偽造。

比起實體物品更想用餘額？在編輯器把 `key-mode` 切成 `virtual`，然後用
`/crate key give <player> starter 1` 發放——**玩家離線時也有效**。

## 4. 放到世界上

站在箱子該在的位置，面向它該面對的方向：

```
/crate admin place starter
```

若有安裝模型外掛且箱子設了 `model:`，你會得到 3D 模型。否則會得到一個仍然點得到
的標記——或者設 `block: CHEST` 變成一般方塊。

## 5. 開箱

```
/crate key give <你的名字> starter 1
```

右鍵點箱子。在預設的 `interact-mode: menu` 之下你會看到預覽介面，從那裡開箱；
左鍵則直接開啟預覽。

## 接下來可以改什麼

| 你想要 | 前往 |
| --- | --- |
| 換一種動畫 | [選擇動畫](../animations/overview.md) |
| 一次開出多件獎勵 | [獎勵與機率](../crates/rewards.md) |
| 冷卻、每日上限或保底獎 | [鑰匙、計價與限制](../crates/keys-pricing-limits.md) |
| 用金錢取代鑰匙 | [鑰匙、計價與限制](../crates/keys-pricing-limits.md) |
| 完整的運鏡演出 | [運鏡演出](../animations/cinematic.md) |

## 直接編輯檔案

編輯器做的每一件事都會寫進 `crates/<id>.yml`，而那個檔案裡的每一項都能手動編輯。
改完之後：

```
/crate reload
```

解析失敗的箱子會在主控台指名回報，其餘的照常運作。
