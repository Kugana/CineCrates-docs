# Permissions

```
cinecrates.open       default true    open crates
cinecrates.preview    default true    view reward lists
cinecrates.odds       default true    /crate odds and /crate list
cinecrates.key        default op      grant / take / set / check keys
cinecrates.admin      default op      everything above plus the editor
```

The three `default true` nodes mean an ordinary player can use crates without
you configuring anything. Revoke them to lock the plugin down.

`cinecrates.admin` implies the others — an admin does not need them listed
separately.

## Per-crate permissions

A crate can require its own node on top of these:

```yaml
permission: crates.vip
```

Players without it are refused at every entry point — left-click, right-click,
either shift variant, the preview screen and `/crate open`. The check happens
before anything is charged, so a refused player never loses a key.

This is checked in one place that every click path runs through, so it applies
identically to block crates, BetterModel crates and ModelEngine crates. There
is no combination of backend and mouse button that gets around it.

## A worked example

A VIP crate that only donors can see or use, opened with virtual keys your web
store grants:

```yaml
# crates/vip.yml
display-name: '&dVIP Crate'
permission: crates.vip
key-mode: virtual
```

```
# permissions plugin
group.donor:
  permissions:
    - cinecrates.open
    - cinecrates.preview
    - crates.vip
```

```
# web store, on purchase
crate key give %player% vip 1
```

## Console

Every command works from the console except the ones that need a position —
`place`, `setcam`, `setmodel` and the editor GUI. Those report that they are
in-game only rather than failing silently.
