# Anatomy of a crate file

One crate is one file in `plugins/CineCrates/crates/`. The file name is the
crate id: `starter.yml` is the crate `starter`.

Only two things are required — `key:` and `rewards:`. Everything else has a
default, and a crate that omits a section simply gets the documented behaviour
for it.

```yaml
display-name: '&6Example Crate'      # shown in menus, messages and holograms
icon:                                # optional: the icon in /crate admin
  material: ENDER_CHEST

animation: cinematic                 # how the opening is staged
open-location: placement             # where it is staged

key-mode: item                       # item | virtual | both
key:                                 # REQUIRED
  material: TRIPWIRE_HOOK
  model-data: 113
  display-name: '&6Example Key'
  lore: ['&7Used to open the Example Crate']

model: gun_crate_1                   # model plugin id; omit for block: crates
hologram:
  - '&6Example Crate'
  - '&7Right-click to open'

rewards:                             # REQUIRED
  diamonds:
    weight: 18
    message: '&fYou received &b5 Diamonds'
    give:
      '1': { material: DIAMOND, amount: 5 }
```

## The sections, in order

| Section | What it decides | Page |
| --- | --- | --- |
| `display-name` | The crate's name everywhere it appears | — |
| `icon` | Its icon in the admin list. Defaults to the key item. | below |
| `permission` | Who may use it at all | [Clicks and permissions](interactions.md) |
| `animation` | Which of the ten styles plays | [Choosing an animation](../animations/overview.md) |
| `open-location` | At the crate, or in a cinematic room | [The cinematic shot](../animations/cinematic.md) |
| `interact-mode` / `interactions` | What each click does | [Clicks and permissions](interactions.md) |
| `key-mode` / `key` / `price` | What an opening costs | [Keys, pricing and limits](keys-pricing-limits.md) |
| `limits` / `restrictions` | When an opening is refused | [Keys, pricing and limits](keys-pricing-limits.md) |
| `rewards` / `rewards-per-open` / `milestones` | What comes out | [Rewards and odds](rewards.md) |
| `reroll` | Paying for a second roll | [Reroll](reroll.md) |
| `model` / `block` / `hologram` / `idle-animation` | What it looks like in the world | [Models](../models/model-plugins.md) |
| `cinematic` | The shot, its timing and the reward presentation | [The cinematic shot](../animations/cinematic.md) |
| `orbit` | Ring size for `roulette` and `spiral` | [Choosing an animation](../animations/overview.md) |
| `preview` | The preview screen's layout | below |

## Items, everywhere

Anywhere the plugin takes an item — the key, a reward, the admin icon, a
preview button — it takes the same shape:

```yaml
key:
  material: TRIPWIRE_HOOK
  model-data: 113
  display-name: '&6Example Key'
  lore: ['&7line one', '&7line two']
  amount: 1
```

or an id from another plugin:

```yaml
key:
  custom-item: itemsadder:myitems:ruby_key
```

See [Custom items](../reference/api.md#custom-items) for the supported
prefixes.

## The admin icon

```yaml
icon:
  material: ENDER_CHEST
  model-data: 7
```

Without `icon:`, the crate shows its **key item** in `/crate admin`. That is
fine until several crates share a key, or use virtual keys, and the list
becomes a row of identical items. Setting `icon:` gives each crate a face.

You can also set it in game: open the crate in `/crate admin` and click the
list-icon button while holding the item. Shift-click clears it.

## The preview screen

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
    - { slot: 4, material: PAPER, name: '&eServer store', command: 'store' }
```

The odds shown on this screen are **computed from the weights**, not typed in,
so they cannot drift away from the actual drop rates.

## After editing

```
/crate reload
```

A crate whose file fails to parse is reported by id in the console and skipped;
the others keep working. Load-time warnings also cover the mistakes that would
otherwise be silent — a reward that grants nothing, a custom-item id that no
longer resolves, an animation and `rewards-per-open` combination that cannot
show every item.
