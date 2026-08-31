# config.yml

The server-wide settings. Everything crate-specific lives in the crate file
instead.

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

## The settings

| Key | Meaning |
| --- | --- |
| `language` | Which file in `messages/` to use. All five ship complete. |
| `model-backend` | Which model plugin to use. `auto` picks whichever is installed. |
| `storage.type` | `sqlite` for a single server, `mysql` for a network. |
| `open-log.enabled` | Append every opening to `open-log.csv`. |
| `metrics` | Anonymous usage statistics via bStats. |
| `update-check` | Notify admins when a newer version is published. |
| `cinematic.camera-mode` | `spectator` (default), `packet` or `teleport`. |
| `cinematic.camera-eye-offset` | Puts the camera at eye level, since `setcam` records your feet. `1.62` is the vanilla eye height. |
| `cinematic.camera-teleport-duration` | How much the client smooths each camera hop. See below. |
| `cinematic.rumble-sound` / `-volume` | The tick played on every spin step. Empty (`''`) mutes it. |
| `cinematic.default-tasks` | The timeline used by crates with no `tasks:` of their own. |

## camera-teleport-duration

This one is worth understanding if a camera path looks jittery.

The value is how many ticks the client spends smoothing each camera hop. It
smooths the camera's **position** only — its facing is applied the moment the
packet lands. A value above the one-tick move interval therefore lets the view
lead the position, and the pan reads as judder.

`1` keeps them in step and is the default. Raise it for a softer but laggier
glide, or set `0` to disable smoothing entirely.

## The opening log

```
time, player, uuid, crate, reward, source
2026-01-14 21:02:11,Steve,f84c6a79-…,vip,legendary,milestone
2026-01-14 21:03:40,Alex,853c80ef-…,vip,iron,roll
```

`source` is `milestone` when the reward was the guaranteed one and `roll`
otherwise, so you can tell a pity payout from an ordinary draw at a glance.
Writes are asynchronous and append-only — disk I/O never touches the tick that
opened the crate.

`open-log.csv` sits in the plugin folder. It exists for the moment a player
disputes what they received — console logs get flooded by other plugins and are
hard to filter by player or date, whereas this file is one line per opening and
opens in any spreadsheet.

## Storage on a network

Point every server at the same MySQL database and virtual key balances,
opening counters and milestone progress are shared:

```yaml
storage:
  type: mysql
  host: db.example.com
  database: cinecrates
  username: cinecrates
  password: '...'
```

Both backends go through HikariCP. The JDBC driver is downloaded at runtime
rather than bundled, so the jar stays small and nothing is shaded into your
server.

## Language files

`messages/<language>.yml` holds every player-facing and admin string, including
the reward announcement. Edit the file for your language and run
`/crate reload` — there is no need to restart, and your edits are never
overwritten by an update.
