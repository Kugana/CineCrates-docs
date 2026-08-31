# 時間軸任務

每個箱子都可以帶自己的時間軸——音效、粒子、標題、ActionBar、主控台指令與運鏡，
各自帶自己的 tick 偏移。

```yaml
cinematic:
  tasks:
    - { delay: 10, type: camera_move, to: 'world,24,73,108,110,40', duration: 50 }
    - { delay: 25, type: sound,       sound: block.chest.open, volume: 1.0 }
    - { delay: 25, type: particle,    particle: CLOUD, amount: 15, y: 1.0 }
    - { delay: 40, type: title,       title: '&6Almost...', subtitle: '%player%' }
    - { delay: 60, type: actionbar,   text: '&7%crate%' }
    - { delay: 90, type: command,     command: 'eco give %player% 100' }
```

`delay` 是從開箱起算的 tick 數——20 tick 是一秒。

## 六種類型

| 類型 | 可用鍵 |
| --- | --- |
| `sound` | `sound`、`volume`、`pitch` |
| `particle` | `particle`、`amount`、`spread`、`speed`、`y`、`at`（`model` 或 `camera`） |
| `title` | `title`、`subtitle`、`fade-in`、`stay`、`fade-out` |
| `actionbar` | `text` |
| `command` | `command` —— 以主控台身分執行 |
| `camera_move` | `to`、`duration` —— 見[運鏡演出](cinematic.md) |

文字欄位可用 `%player%`、`%crate%` 與 `%reward%`。

音效與粒子**只送給開箱的那位玩家**。開箱是個人的演出；兩個玩家並肩開箱不會看到或
聽到對方的。

## 全伺服器的預設時間軸

沒有自己的 `tasks:` 的箱子，會執行 `config.yml` 裡共用的那一組：

```yaml
# config.yml
cinematic:
  default-tasks:
    - { delay: 15, type: sound, sound: block.anvil.place, volume: 0.8, pitch: 0.9 }
    - { delay: 25, type: sound, sound: block.iron_door.open, volume: 0.8 }
    - { delay: 40, type: sound, sound: block.chest.open, volume: 1.0 }
    - { delay: 40, type: particle, particle: CLOUD, amount: 15, spread: 0.35, y: 1.0 }
```

在那裡設定一次全站的風格，只針對值得特別處理的箱子覆寫。

{% hint style="warning" %}
箱子自己的 `tasks:` 會**取代**預設清單——兩者永遠不會合併。所以加了運鏡路徑的箱子
就等於放棄了預設音效，除非把它們重複寫一遍。把你還想要的那幾行複製過去。
{% endhint %}

## 不要在這裡公布獎勵

`title` 任務可以用 `%reward%` 寫出獎勵名稱，用它來做揭曉很誘人。**不要這樣做。**

任務是在從開箱**起點**起算的固定偏移觸發，但獎勵是照另一套時鐘抵達的——在運鏡演出
裡是飛行結束之後，在原地家族裡是輪播停下之後。兩者毫無關聯，所以一個在某個箱子上
看起來剛好的固定延遲，在另一個箱子上就會在輪播中途把答案喊出來，破壞揭曉。

獎勵公布是**內建的**，並且在真正的揭曉點觸發。見[獎勵呈現](reward-presentation.md)。

## 縮排

`tasks:` 屬於 `cinematic:` 區塊**內部**，跟那裡的每個鍵一樣縮排兩個空格。放在
第 0 欄的 `tasks:` 會被當成頂層設定、**靜默忽略，而且不會有任何記錄**——症狀就是
時間軸看起來完全沒有作用。
