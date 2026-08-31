# config.yml

全伺服器共用的設定。箱子專屬的設定都在箱子檔案裡。

```yaml
language: en                 # en | tw | cn | ja | es

model-backend: auto          # auto | bettermodel | modelengine | none

storage:
  type: sqlite               # sqlite | mysql
  host: localhost
  port: 3306
  database: cinecrates
  username: root
  password: ''
  use-ssl: false
  pool-size: 8
  max-lifetime-ms: 1800000
  connection-timeout-ms: 5000

open-log:
  enabled: true

metrics: true
update-check: true

cinematic:
  camera-mode: spectator
  camera-eye-offset: 1.62
  camera-teleport-duration: 1
  rumble-sound: ui.button.click
  rumble-volume: 0.7
  default-tasks:
    - { delay: 15, type: sound, sound: block.anvil.place, volume: 0.8, pitch: 0.9 }
    - { delay: 40, type: particle, particle: CLOUD, amount: 15, spread: 0.35, y: 1.0 }
```

## 各項設定

| 鍵 | 意義 |
| --- | --- |
| `language` | 使用 `messages/` 裡的哪一份。五份都是完整的。 |
| `model-backend` | 使用哪個模型外掛。`auto` 會挑已安裝的那個。 |
| `storage.type` | 單機用 `sqlite`，群組用 `mysql`。 |
| `open-log.enabled` | 把每次開箱附加到 `open-log.csv`。 |
| `metrics` | 透過 bStats 回報匿名使用統計。 |
| `update-check` | 有新版本發布時通知管理員。 |
| `cinematic.camera-mode` | `spectator`（預設）、`packet` 或 `teleport`。 |
| `cinematic.camera-eye-offset` | 把相機放回眼睛高度，因為 `setcam` 記錄的是腳下位置。`1.62` 是原版眼高。 |
| `cinematic.camera-teleport-duration` | 客戶端對每次相機跳躍做多少平滑。見下方。 |
| `cinematic.rumble-sound` / `-volume` | 輪播每一步的滴答聲。留空（`''`）可靜音。 |
| `cinematic.default-tasks` | 沒有自己 `tasks:` 的箱子所使用的時間軸。 |

## camera-teleport-duration

如果運鏡路徑看起來會抖，這一項值得理解。

這個值是客戶端花多少 tick 平滑每一次相機跳躍。它**只平滑相機的位置**——朝向是封包
一到就套用。因此數值大於每 tick 的移動間隔時，視線會跑在位置前面，運鏡就會讀起來
像在抖。

`1` 讓兩者同步，也是預設值。想要更柔但更延遲的滑行就調高，設 `0` 則完全關閉平滑。

## 開箱記錄

```
time, player, uuid, crate, reward, source
2026-01-14 21:02:11,Steve,f84c6a79-…,vip,legendary,milestone
2026-01-14 21:03:40,Alex,853c80ef-…,vip,iron,roll
```

`source` 在該次是保底獎時為 `milestone`，其餘為 `roll`，所以一眼就能分辨保底發放與
一般抽取。寫入是**非同步且只附加**的——磁碟 I/O 永遠不會發生在開箱的那個 tick 上。

`open-log.csv` 位於外掛資料夾。它存在的意義是玩家對自己拿到什麼有爭議的那一刻——
主控台記錄會被其他外掛洗版，很難依玩家或日期過濾，而這個檔案一次開箱一行，用任何
試算表都打得開。

## 群組伺服器的資料庫

讓每一台指向同一個 MySQL，虛擬鑰匙餘額、開箱計數與保底進度就會共用：

```yaml
storage:
  type: mysql
  host: db.example.com
  database: cinecrates
  username: cinecrates
  password: '...'
```

兩種後端都走 HikariCP。JDBC 驅動是在執行期下載而非打包，所以 jar 保持輕巧，也不會
有任何東西被 shade 進你的伺服器。

## 語言檔

`messages/<語言>.yml` 裝著所有面向玩家與管理員的字串，包含獎勵公布的文案。編輯你
語言的那一份再執行 `/crate reload` 即可——**不需要重啟，而且更新時不會被覆蓋**。
