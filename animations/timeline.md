# Timeline tasks

Every crate can carry its own timeline — sounds, particles, titles, action
bars, console commands and camera moves, each on its own tick offset.

```yaml
cinematic:
  tasks:
    - { delay: 10, type: camera_move, to: 'world,24,73,108,110,40', duration: 50 }
    - { delay: 25, type: sound,       sound: block.chest.open, volume: 1.0 }
    - { delay: 25, type: particle,    particle: CLOUD, amount: 15, y: 1.0 }
    - { delay: 40, type: title,       title: '&6Almost...', subtitle: '%player%' }
    - { delay: 60, type: actionbar,   text: '&7%crate%' }
    - { delay: 90, type: command,     command: 'eco give %player% 100' }
```

`delay` is in ticks from the start of the opening — 20 ticks is one second.

## The six types

| Type | Keys |
| --- | --- |
| `sound` | `sound`, `volume`, `pitch` |
| `particle` | `particle`, `amount`, `spread`, `speed`, `y`, `at` (`model` or `camera`) |
| `title` | `title`, `subtitle`, `fade-in`, `stay`, `fade-out` |
| `actionbar` | `text` |
| `command` | `command` — run from the console |
| `camera_move` | `to`, `duration` — see [The cinematic shot](cinematic.md) |

Text fields take `%player%`, `%crate%` and `%reward%`.

Sounds and particles are sent **to the opening player only**. The opening is a
personal show; two players opening crates side by side do not see or hear each
other's.

## The server-wide default

A crate with no `tasks:` of its own runs the shared timeline from `config.yml`:

```yaml
# config.yml
cinematic:
  default-tasks:
    - { delay: 15, type: sound, sound: block.anvil.place, volume: 0.8, pitch: 0.9 }
    - { delay: 25, type: sound, sound: block.iron_door.open, volume: 0.8 }
    - { delay: 40, type: sound, sound: block.chest.open, volume: 1.0 }
    - { delay: 40, type: particle, particle: CLOUD, amount: 15, spread: 0.35, y: 1.0 }
```

Set your house style once there, and override only the crates that deserve
something special.

{% hint style="warning" %}
A crate's own `tasks:` **replaces** the default list — the two are never
merged. A crate that adds a camera path therefore gives up the default sounds
unless it repeats them. Copy the lines you still want.
{% endhint %}

## Do not announce the reward here

A `title` task can name the reward with `%reward%`, and it is tempting to use
one for the reveal. Do not.

A task fires at a fixed offset from the **start** of the opening, but the
reward arrives on a different clock — after its flight in the cinematic shot,
after the spin in the in-place family. The two are unrelated, so a fixed delay
that looks right on one crate announces the reward mid-spin on another and
spoils it.

The reward announcement is built in and fires at the real reveal. See
[Reward presentation](reward-presentation.md).

## Indentation

`tasks:` belongs **inside** the `cinematic:` block, indented two spaces like
every other key there. A `tasks:` at column 0 is parsed as a top-level setting,
silently ignored, and nothing is logged — the symptom is a timeline that
appears to do nothing at all.
