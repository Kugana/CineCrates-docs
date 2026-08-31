# Installation

## Requirements

| | |
| --- | --- |
| **Server** | Spigot, Paper or Folia — 1.21 through 26.x |
| **Java** | 21 or newer. Minecraft 26.x requires Java 25 — that is Mojang's requirement, not the plugin's. |

Everything below is optional. The plugin runs without any of it.

| Plugin | What it adds |
| --- | --- |
| BetterModel *or* ModelEngine | 3D animated crate models. Without one, use `block:` crates. |
| Vault | `price: { type: money }` |
| PlaceholderAPI | `%cinecrates_*%` placeholders |
| ItemsAdder / Oraxen / Nexo / MMOItems / CustomItemGUI | Custom items as keys, rewards and icons |

## Install

1. Drop `CineCrates.jar` into `plugins/` and start the server.
2. The plugin writes `plugins/CineCrates/` with a `config.yml`, a `messages/`
   folder and an `example` crate you can open immediately.
3. Check the console for the startup banner. It reports the scheduler
   (`Bukkit` or `Folia`), the model backend it picked, and how many crates and
   placements it loaded.

```
[CineCrates] Scheduler: Folia.
[CineCrates] Model backend: BetterModel.
[CineCrates] Loaded 1 crate(s).
[CineCrates] Loaded 0 placement(s).
```

If the model backend line says `none`, no model plugin was found — crates will
still work, but they need `block:` (see [Block crates](../models/block-crates.md)).

## Folia

Folia support is not a compatibility shim. Every entity, block and scheduler
call is routed through the region that owns it, so placements, cinematics,
animations, database writes and `/crate reload` all behave the same as they do
on Paper. There is nothing to enable — the plugin detects Folia at startup and
switches schedulers.

## Choosing a language

`config.yml`:

```yaml
language: en     # en | tw | cn | ja | es
```

All five files ship complete, not partial translations with English gaps. They
live in `plugins/CineCrates/messages/` and every player-facing and admin string
is in there, including the reward announcement — edit the file for your
language and run `/crate reload`.

## Storage

SQLite by default, with nothing to configure. For a network, point every server
at the same MySQL database:

```yaml
storage:
  type: mysql
  host: localhost
  port: 3306
  database: cinecrates
  username: root
  password: ''
  use-ssl: false
  pool-size: 8
```

Both go through HikariCP, and the JDBC driver is downloaded at runtime rather
than bundled, so the jar stays small and nothing is shaded into your server.

Virtual key balances and opening counters live in this database. If the pool
cannot be created the plugin logs the failure and falls back to SQLite rather
than refusing to start.

## Updating

Replace the jar and restart. `config.yml` and your crate files are never
overwritten — new options are added with their defaults, so a key you have not
set behaves as documented and one you have set is left alone.
