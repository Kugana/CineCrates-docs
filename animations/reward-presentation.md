# Reward presentation

Where the reward comes out, where it lands, and when the player is told what
they got.

## The announcement

When the reward is revealed the player gets a title, a level-up sound and a
particle burst. It fires at the **actual reveal** for every animation — when
the item locks to its reward in the classic shot, when the wheel stops in the
in-place family, when the pointer settles in the GUI roulette.

```yaml
cinematic:
  reward-title: true        # false turns off the title
  reward-title-delay: 10    # ticks to wait after the freeze
```

`reward-title-delay` lets the settling hop land before the text appears. Raise
it to let the shot breathe first.

Turning `reward-title` off silences the title but keeps the sound and the
particles — the toggle is about the text. The wording lives in your language
file (`cinematic.reward-title` and `cinematic.reward-subtitle`), where
`%crate%` and `%reward%` both work.

{% hint style="info" %}
This is why the announcement is not a timeline task: a task fires at a fixed
offset from the start of the opening, while the reward arrives on its own
schedule. Built in, it cannot spoil a spin that is still turning.
{% endhint %}

## Where the reward comes out

```yaml
cinematic:
  reward-anchor: crate         # crate (default) | player
  reward-offset: '0,1.2,0'     # x, y, z
  reward-velocity: '0,0.28,0.12'
```

`reward-offset` is the **launch point** — where the item is born. It cannot aim
the item.

The frame it is measured in depends on the animation:

| Animation | Frame |
| --- | --- |
| `cinematic` | World axes, measured from `model-location` |
| the in-place family | The **crate's own frame**: x = right, y = up, z = forward |

`reward-anchor: player` switches the frame to the viewer instead — the opening
player at a placement, or the camera inside a cinematic room. With it,
"in front of the player" is just `0,1.0,1.5`.

For the in-place family the default is `0,1.1,0.75`. Setting z to `0` puts the
reward inside the crate model, where a large model simply swallows it — if
items look like they drop on the spot, that z is why.

`reward-velocity`'s **y** value sets the arc height, and with it how long the
item stays in the air. Lower it for a snappier opening.

## Where it lands (cinematic only)

```yaml
cinematic:
  reward-target: '0,0,1.2'     # x = right, y unused, z = forward
```

This is the key that **aims** the reward, and it is measured in the **camera's
frame** — so a crate placed at any angle needs no maths. `0,0,1.2` means "1.2
blocks in front of the camera", whichever way the camera happens to be facing.

Without `reward-target`, the horizontal velocity is whatever `reward-velocity`
says: a fixed push along the world axes that knows nothing about the crate's
facing or where the camera is. On a crate placed at an angle the reward then
flies off-shot no matter how you tune the offset.

With it set, the velocity is solved from the launch point to the landing point,
so the throw follows the crate → camera line automatically.

On a crate with a camera path, the aim uses the camera pose at the moment the
reward **lands**, not the moment it launches — the camera keeps travelling
during the flight, and aiming at where it started would drop the reward
somewhere the shot has already left.

The in-place family ignores `reward-target`; each style has its own trajectory.

## When the reward appears

```yaml
cinematic:
  reward-delay: 70     # ticks after the opening starts
```

Every animation reads it the same way: the classic one pops the item out at
this tick, the in-place family starts its show then. The model's own open
animation plays first either way.

Pushing `reward-delay` past `length-ticks` means the reward show never gets to
run — raise both together.

## Two traps worth knowing

**Indentation.** `reward-anchor`, `reward-offset`, `reward-target` and the rest
belong **inside** `cinematic:`, indented two spaces. Un-commenting one back to
column 0 makes it a top-level key that is silently ignored — tuning it then
appears to do nothing.

**Wrong knob.** If rewards are not flying toward the camera, `reward-offset` is
not the fix. It sets where the item is born; `reward-target` is what aims it.
