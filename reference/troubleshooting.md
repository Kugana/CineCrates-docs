# Troubleshooting

## Left-click does nothing on a model crate

Almost always a missing hitbox. A left-click is an attack, and the entity
carrying the model is an invisible marker that vanilla attacks cannot reach —
the click has to arrive through the model's own hitbox.

ModelEngine builds one automatically. **BetterModel** builds one for a bone
named `hitbox`, or one tagged with the `b_` / `ob_` prefix. A model with
neither has no hitbox entity, so there is nothing to hit.

The plugin warns at startup when this is the case and lists the bones the model
actually has. Add a group named `hitbox` containing one untextured cube the
size of the crate, then reload the model plugin.

Right-click never depends on a hitbox, so a crate where right-click works and
left-click does not is the classic signature.

## A setting seems to do nothing

Check its indentation. `reward-offset`, `reward-target`, `tasks` and the rest
of the reward and timeline keys belong **inside** the `cinematic:` block,
indented two spaces. A key at column 0 is parsed as a top-level setting,
silently ignored, and nothing is logged.

This bites most often when un-commenting an example line, because the comment
may sit at a different indentation than the key needs.

## Rewards fly off in the wrong direction

`reward-offset` is not the knob. It sets where the item is **born**; it cannot
aim it. Use `reward-target`, which is measured in the camera's frame so a crate
placed at any angle needs no maths:

```yaml
cinematic:
  reward-target: '0,0,1.2'    # 1.2 blocks in front of the camera
```

Without it, the horizontal velocity is a fixed world-axis push from
`reward-velocity` that knows nothing about the crate's facing.

## Rewards drop inside the crate model

For the in-place family, `reward-offset`'s **z** is measured in the crate's own
frame — forward. Setting z to `0` puts the reward inside the model, where a
large model swallows it. The default is `0,1.1,0.75`.

## The camera looks at empty floor

`setcam` records the angle you were looking at when you ran it. If you moved
the model afterwards with `setmodel`, run `setcam` again — otherwise the camera
still points where the model used to be.

## The camera path judders

Set `cinematic.camera-teleport-duration` to `1` in `config.yml`. Above the
one-tick move interval the client smooths the camera's position but not its
facing, so the view leads the position and the pan reads as judder.

## The reward title spoils the spin

Do not announce the reward with a `title` timeline task. A task fires at a
fixed offset from the start of the opening, while the reward arrives on its own
clock — so a delay that looks right on one crate fires mid-spin on another.

The announcement is built in and fires at the real reveal. Configure it with
`reward-title` and `reward-title-delay`.

## A crate's timeline stopped working after I added a camera path

A crate's own `tasks:` **replaces** the server-wide `default-tasks`; they are
never merged. Copy the default lines you still want into the crate.

## `/crate admin unplace <crate>` says "not found"

`unplace` takes a **placement id**, not a crate id. They are generated as
`<crate>_<n>`. Tab completion lists them, or run `/crate admin places`.

## Nothing happened and there is nothing in the console

Check for a load-time warning at startup — the plugin reports crates it could
not parse, rewards that grant nothing, custom-item ids that no longer resolve,
and models with no hitbox. If the console is genuinely clean, the crate may be
refusing the opening: a permission node, a cooldown, a daily cap, a full
inventory or a blacklisted world all refuse **before** anything is charged, and
each sends the player a message explaining which.

## Getting help

Post in the resource discussion thread with:

* your **`latest.log`**
* the **crate yml** that misbehaves

Those two together answer most questions immediately.
