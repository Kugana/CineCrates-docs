# Placeholders, events and storage

## Custom items

Keys, rewards and crate icons all accept an id from another item plugin, with
the same syntax everywhere:

```yaml
key:
  custom-item: itemsadder:myitems:ruby_key
```

| Prefix | Plugin |
| --- | --- |
| `itemsadder:` | ItemsAdder |
| `oraxen:` | Oraxen |
| `nexo:` | Nexo |
| `mmoitems:` | MMOItems — `mmoitems:TYPE:ID` |
| `cig:` | CustomItemGUI |

Matching uses each plugin's own id lookup, so re-texturing or renaming an item
never breaks an existing key, and nobody can forge one by crafting a
look-alike.

If an id no longer resolves — the item was deleted, or the providing plugin is
missing — the crate is flagged at load time with the crate, the reward and the
id, rather than silently handing out a fallback item.

## PlaceholderAPI

| Placeholder | Returns |
| --- | --- |
| `%cinecrates_keys_<crate>%` | The player's virtual key balance |
| `%cinecrates_opens_<crate>%` | How many times they have opened it |
| `%cinecrates_daily_<crate>%` | Openings used against today's cap |
| `%cinecrates_cooldown_<crate>%` | Seconds left on the cooldown |
| `%cinecrates_chance_<crate>_<reward>%` | That reward's real chance, as a percentage |

Reward ids may contain underscores — the placeholder is split from the right,
so `%cinecrates_chance_vip_diamond_block%` resolves correctly.

## Events

```java
CrateOpenEvent      // before anything is charged — cancellable
CrateKeyUseEvent    // before the key is consumed — cancellable
CrateRewardEvent    // when a reward is granted
CratePreviewEvent   // when the preview screen opens — cancellable
```

The ones that fire before anything is committed are cancellable, so another
plugin can veto an opening without the player losing a key.

```java
@EventHandler
public void onCrateOpen(CrateOpenEvent e) {
    if (isEventPaused()) {
        e.setCancelled(true);
    }
}
```

## The API class

`CineCratesAPI` exposes the read-only queries a companion plugin usually
wants — the crate list, a crate's rewards and computed odds, a player's virtual
key balance, and building a crate's key item to hand out.

## Storage

SQLite by default; MySQL for a network. Both go through HikariCP, and the JDBC
driver is fetched at runtime rather than shaded into the jar.

What lives in the database:

* virtual key balances
* opening counters, per player per crate
* milestone (pity) progress
* daily-limit counters and cooldowns

What lives on disk instead: crate definitions (`crates/*.yml`), placements
(`locations.yml`), messages, and the opening log (`open-log.csv`).

If the connection pool cannot be created, the plugin logs the failure and falls
back to SQLite rather than refusing to start, so a database outage does not
take the server's crates down with it.
