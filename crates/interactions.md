# Clicks and permissions

## The quick version

```yaml
interact-mode: menu     # menu (default) | direct
```

`interact-mode` sets the **defaults** for the four click bindings:

| Click | `menu` | `direct` |
| --- | --- | --- |
| Right | Preview | Open |
| Shift + right | Preview | Open |
| Left | Preview | Preview |
| Shift + left | Preview | Preview |

In `menu` mode everything opens the preview screen and the player opens the
crate from a button inside it. In `direct` mode right-click opens immediately
and the preview stays available on left-click.

## Binding each click yourself

```yaml
interactions:
  RIGHT: open
  SHIFT_RIGHT: instant
  LEFT: preview
  SHIFT_LEFT: none
```

Actions: `open`, `instant` (skip the animation), `preview`, `none`.

The block **merges** with the defaults — keys you leave out keep the value
`interact-mode` gave them. Writing only `RIGHT: open` changes right-click and
nothing else.

`instant` is useful on a shift binding for players burning through a stack of
keys, since it skips the animation entirely.

## Permissions

```yaml
permission: crates.vip
```

A crate with a `permission` node refuses every entry point for players who lack
it — left-click, right-click, either shift variant, the preview, and
`/crate open`. The check happens before the cost is taken.

{% hint style="info" %}
The permission gate lives in one place that every click path runs through, so
it applies identically to block crates, BetterModel crates and ModelEngine
crates. There is no combination of model backend and mouse button that gets
around it.
{% endhint %}

## Left-click on a model crate

There is one thing to know if your crate uses a 3D model.

A left-click is an *attack*, and the entity that carries the model is an
invisible marker that vanilla attacks cannot reach. The plugin routes the click
through the model plugin's own hitbox instead — which means **the model must
define a hitbox** for left-click bindings to work at all.

* **ModelEngine** builds a hitbox for the model on its own. Nothing to do.
* **BetterModel** builds one for a bone named `hitbox`, or for a bone tagged
  with the `b_` or `ob_` prefix (`b_hitbox`). A model with no such bone has no
  hitbox entity, so nothing can be hit and left-click does nothing.

If left-click does nothing on a BetterModel crate, that is almost always the
cause — see [BetterModel and ModelEngine](../models/model-plugins.md#hitboxes).

Right-click never depends on a hitbox, and block crates receive both buttons
directly, so neither is affected.
