# Keys, pricing and limits

## Key modes

```yaml
key-mode: item      # item | virtual | both
```

| Mode | Behaviour |
| --- | --- |
| `item` | A physical key in the inventory. |
| `virtual` | A balance in the database. Cannot be lost on death, and can be granted while the player is offline. |
| `both` | Either will do. Virtual is spent first. |

Physical keys are matched by the plugin's own id, not by name or texture — so
re-texturing or renaming the key item never invalidates keys already in
circulation, and a player cannot forge one by crafting a look-alike.

```
/crate key give <player> <crate> [n] [item]
/crate key take <player> <crate> [n]
/crate key set  <player> <crate> <n>
/crate key check <player> <crate>
```

`give` works for offline players, so a web store can call it straight from the
console. On a `both` crate it grants virtual keys by default — append `item` to
hand out a physical one. `take`, `set` and `check` only ever touch the virtual
balance; physical keys live in the inventory where you can see them.

Players check their own balances with `/crate keys`.

## Pricing without keys

```yaml
price:
  type: money       # key | money | permission | free
  amount: 2500
```

| Type | Cost |
| --- | --- |
| `key` | A key, per `key-mode`. This is the default when `price:` is omitted. |
| `money` | Withdraws `amount` through Vault. |
| `permission` | Free for anyone holding the node — set `amount` aside and give `node:` instead. |
| `free` | Nothing at all. |

`money` needs Vault **and** an economy plugin behind it. Without one the
withdrawal cannot happen and the opening is refused with a message rather than
silently succeeding.

## Restricting who may open at all

```yaml
permission: crates.vip
```

A crate with a `permission` refuses every interaction from a player who lacks
the node — clicking it, previewing it, opening it by command. This is checked
before anything is charged, and it applies the same way to block crates and to
model crates whichever mouse button is used.

## Cooldowns and daily caps

```yaml
limits:
  cooldown-seconds: 30
  daily: 5
```

`daily` resets on the server's own day boundary and is stored per player per
crate. Both are checked before the cost is taken.

A reroll does **not** re-check either of these — it is still the same opening.

## Refusing an opening

```yaml
restrictions:
  full-inventory: true              # refuse rather than drop items on the floor
  world-blacklist: [world_nether, world_the_end]
```

The important property of every refusal in this section: it happens **before
anything is charged**. A blocked opening never consumes a key, never withdraws
money, and never advances milestone progress. A player who tries to open a
crate with a full inventory loses nothing and is told why.

`full-inventory: false` (the default) lets the opening proceed and drops what
does not fit at the player's feet.
