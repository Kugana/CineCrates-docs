# 權限

```
cinecrates.open       預設 true    開箱
cinecrates.preview    預設 true    查看獎勵一覽
cinecrates.odds       預設 true    /crate odds 與 /crate list
cinecrates.key        預設 op      發放／扣除／設定／查詢鑰匙
cinecrates.admin      預設 op      以上全部,加上編輯器
```

那三個 `預設 true` 的節點代表一般玩家不必你做任何設定就能使用箱子。想鎖起來就把
它們收回。

`cinecrates.admin` 已經隱含其餘節點——管理員不必另外列出。

## 每個箱子的權限

箱子可以在這些之上要求自己的節點：

```yaml
permission: crates.vip
```

沒有它的玩家會在**每一個入口**被拒絕——左鍵、右鍵、兩種 Shift 變體、預覽介面與
`/crate open`。檢查發生在扣費之前，所以被拒絕的玩家永遠不會損失鑰匙。

這個檢查位於每條點擊路徑都會經過的**同一個地方**，所以對方塊型箱子、BetterModel
箱子與 ModelEngine 箱子的作用完全一致。沒有任何「後端 × 滑鼠鍵」的組合能繞過它。

## 一個完整範例

一個只有贊助者看得到也用得到的 VIP 箱子，用網頁商店發放的虛擬鑰匙開啟：

```yaml
# crates/vip.yml
display-name: '&dVIP Crate'
permission: crates.vip
key-mode: virtual
```

```
# 權限外掛
group.donor:
  permissions:
    - cinecrates.open
    - cinecrates.preview
    - crates.vip
```

```
# 網頁商店,購買時執行
crate key give %player% vip 1
```

## 主控台

除了需要位置的那幾個——`place`、`setcam`、`setmodel` 與編輯器介面——每個指令都能
從主控台執行。那幾個會回報「僅限遊戲內」，而不是靜默失敗。
