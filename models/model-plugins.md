# BetterModel and ModelEngine

Both are supported, both are optional, and the plugin picks one automatically.

```yaml
# config.yml
model-backend: auto      # auto | bettermodel | modelengine | none
```

`auto` prefers whichever is installed. The choice is reported at startup:

```
[CineCrates] Model backend: BetterModel.
```

## Using a model

```yaml
model: chest_t3           # the model id as the model plugin knows it
idle-animation: idle      # played while the crate sits there
cinematic:
  open-animation: open    # played when the crate opens
```

`idle-animation` is worth setting explicitly rather than relying on the model's
own default state — a model that has no default state would otherwise sit
motionless.

## Hitboxes

This matters for **left-click** bindings only.

A left-click is an attack, and the entity carrying the model is an invisible
marker that vanilla attacks cannot reach. The click has to arrive through the
model's own hitbox instead.

| Backend | Hitbox |
| --- | --- |
| **ModelEngine** | Built automatically. Nothing to do. |
| **BetterModel** | Built for a bone named `hitbox`, or for a bone tagged with the `b_` / `ob_` prefix (for example `b_hitbox`). |

A BetterModel model with no such bone has no hitbox entity, so there is nothing
to hit and left-click does nothing at all. The plugin says so at startup rather
than leaving you guessing:

```
[CineCrates] Model chest_t3 has no hitbox bone, so left-click will do nothing.
BetterModel looks for a bone named b_… or ob_…. Bones found: main, group, top
```

To fix it, open the model in Blockbench and add a group named `hitbox`
containing one untextured cube the size of the crate, then reload the model
plugin. Right-click never depends on a hitbox.

{% hint style="info" %}
Models converted from ModelEngine often carry a bone named `hitbox` already,
which BetterModel does recognise. The warning above appears only when the model
has neither that name nor the prefix.
{% endhint %}

## Per-viewer visibility

An opening is a personal show. The crate model staged for one player is not
shown to anyone else, so two players opening the same crate at the same time do
not see each other's animation or reward items. This is handled for you on both
backends.

## When the model fails to load

If the model id does not resolve — a typo, or the model plugin has not imported
it — the plugin logs a warning naming the crate and the id, and falls back to a
**visible** marker stand at that spot. That is deliberate: an invisible failure
would look like the crate had vanished, whereas a bare stand tells you at a
glance that the model did not load, and players can still click it.

## Neither plugin installed

Everything still works. Use [block crates](block-crates.md).
