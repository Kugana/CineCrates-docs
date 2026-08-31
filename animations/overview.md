# Choosing an animation

```yaml
animation: cinematic
```

One crate has one animation. **Everything except `cinematic` needs zero
coordinates** — no room to build, nothing to align. Set the style, place the
crate, done.

| Style | What the player sees |
| --- | --- |
| `cinematic` | The camera is taken over and locked; the shot can follow a path you author. |
| `in-place` | Rewards pop out of the crate where it stands. |
| `three-toss` | Three items thrown left / centre / right; the winner stays, the losers puff away. |
| `roulette` | Items orbit a ring and slow to a stop. |
| `fountain` | A burst that erupts with every reward at the finale. |
| `rain` | Rewards fall from above. |
| `spiral` | A helix climbing out of the crate. |
| `beam` | A light column carrying the rewards up. |
| `gui` | A CS:GO-style scrolling strip with a pointer. |
| `instant` | No animation; the reward goes straight to the inventory. |

`in-place`, `three-toss`, `roulette`, `fountain`, `rain`, `spiral` and `beam`
are together called the **in-place family** throughout this documentation —
they share the same staging code and the same tuning keys.

## Where it is staged

```yaml
open-location: placement     # placement (default) | cinematic
```

`placement` runs the animation at the crate the player clicked. `cinematic`
takes the player to a room you build once, at the coordinates in the
`cinematic:` section.

For the in-place family this simply decides whether the player stays put or is
taken to the room. For `animation: cinematic` the shot always happens — this
only decides where it is set up, and `placement` moves the **entire shot**
(camera path, model and reward landing point) onto the clicked crate, rotated
to that crate's facing. One camera setup then covers every placement on your
map.

## Tuning the orbiting styles

`roulette` and `spiral` share one section:

```yaml
orbit:
  radius: 1.15     # how wide the ring is
  scale: 0.55      # how large each item renders
```

Omit it and each style keeps its own defaults.

## Tuning the spin

```yaml
cinematic:
  rumble-length: 40    # how long the item cycles before locking
  rumble-period: 4     # how often the shown item changes, in ticks
```

These live in the `cinematic:` section but apply to the in-place family too —
the section holds the timing for every style, not just the cinematic one. A
short `rumble-length` still gets a minimum spin so the slow-down reads.

The sound played on each step is server-wide:

```yaml
# config.yml
cinematic:
  rumble-sound: ui.button.click
  rumble-volume: 0.7
```

## Skipping

```yaml
cinematic:
  skippable: true
```

Lets a player shift out of a cinematic. The reward is granted exactly as if the
animation had finished — skipping never costs anything.
