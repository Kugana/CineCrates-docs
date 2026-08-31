# Commands

Aliases: `/cinecrates`, `/crates`

## Players

| Command | What it does |
| --- | --- |
| `/crate list` | List the crates you can see |
| `/crate preview <crate>` | Reward list with the real odds |
| `/crate odds <crate>` | The same odds as text |
| `/crate open <crate>` | Open it, paying the configured cost |
| `/crate keys` | Your own virtual key balances |
| `/crate help` | Player help, trimmed to what you may use |

## Keys

| Command | What it does |
| --- | --- |
| `/crate key give <player> <crate> [n] [item]` | Grant keys — **works while the player is offline** |
| `/crate key take <player> <crate> [n]` | Remove virtual keys |
| `/crate key set <player> <crate> <n>` | Set the virtual balance |
| `/crate key check <player> <crate>` | Read a player's balance |

`give` grants virtual keys by default. On a `both` crate, append `item` to hand
out a physical key instead. `take`, `set` and `check` only ever touch the
virtual balance — physical keys live in the inventory.

Because `give` works offline and from the console, a web store can call it
directly:

```
crate key give %player% vip 1
```

## Administration

| Command | What it does |
| --- | --- |
| `/crate admin` | Open the editor GUI |
| `/crate admin create <id> [name]` | Create a crate |
| `/crate admin info <crate>` | Summary of one crate's configuration |
| `/crate admin setkey <crate>` | Set the key from the item in your hand |
| `/crate admin addreward <crate> [weight]` | Add the held item as a reward |
| `/crate admin delreward <crate> <reward>` | Remove a reward |
| `/crate admin weight <crate> <reward> <n>` | Change a reward's weight |
| `/crate admin setmodel <crate>` | Record the model spot — stand where it should appear |
| `/crate admin setcam <crate>` | Record the camera spot — stand where it should look from |
| `/crate admin modelid <crate> <id>` | Set the model plugin's model id |
| `/crate admin anim <crate> <style>` | Change the animation |
| `/crate admin length <crate> <ticks>` | Change the cinematic length |
| `/crate admin skip <crate> <true\|false>` | Toggle skippability |
| `/crate admin cinematic <crate> <...>` | The cinematic sub-settings |
| `/crate admin mode <crate> <menu\|direct>` | Change the interact mode |
| `/crate admin place <crate>` | Place a crate where you stand |
| `/crate admin unplace <placement-id>` | Remove a placement |
| `/crate admin places` | List every placement |
| `/crate reload` | Reload config, messages, crates and placements |
| `/crate help admin` | The full admin reference in game |

{% hint style="info" %}
`unplace` takes a **placement id**, not a crate id. Placement ids are generated
as `<crate>_<n>` — `starter_1`, `starter_2`. Tab completion lists them, and
`/crate admin places` prints them with their coordinates.
{% endhint %}

## Reloading

`/crate reload` re-reads `config.yml`, the language files, every crate file and
every placement, and rebuilds the placed models and holograms. It does not
require a restart.

A crate whose file fails to parse is named in the console and skipped; the rest
keep working.
