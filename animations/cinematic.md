# The cinematic shot

`animation: cinematic` takes over the player's camera and flies it through a
shot you author. This is the only animation that needs coordinates.

## Building the room

1. Build the set wherever you like — most people use a walled room out of
   sight, but it can be anywhere.
2. Stand where the **crate model** should appear and run:

   ```
   /crate admin setmodel <crate>
   ```
3. Stand where the **camera** should sit, looking at the model, and run:

   ```
   /crate admin setcam <crate>
   ```
4. Set the crate to use the room:

   ```yaml
   open-location: cinematic
   ```

Both commands record your feet position plus the direction you are facing.

{% hint style="warning" %}
`setcam` records the angle you were looking at **when you ran it**. If you move
the model afterwards with `setmodel`, run `setcam` again — otherwise the camera
keeps pointing where the model used to be, and the shot frames empty floor.
{% endhint %}

## What the section holds

```yaml
cinematic:
  enabled: true
  model-location: world,18.7,73.0,107.8,-43.8,4.6
  camera-location: world,22,74,111,134.6,22.1
  length-ticks: 170          # total length of the shot (20 ticks = 1 second)
  skippable: true
  open-animation: open       # the model animation to play
  reward-delay: 70           # when the reward appears
```

`enabled: false` turns the whole cinematic off without deleting the
coordinates.

## Camera paths

![A crate opening with a camera path](../assets/anim-camera.gif)

Add `camera_move` steps to the crate's timeline and the camera glides between
them with smooth easing:

```yaml
cinematic:
  tasks:
    - { delay: 10,  type: camera_move, to: 'world,24,73,108,110,40', duration: 50 }
    - { delay: 65,  type: camera_move, to: 'world,21,72,111,150,35', duration: 45 }
    - { delay: 125, type: camera_move, to: 'world,23,75,112,135,52', duration: 35 }
```

| Key | Meaning |
| --- | --- |
| `delay` | Ticks from the start of the opening |
| `to` | Destination, in the same format `setcam` writes |
| `duration` | Travel time in ticks. `0` is an instant cut. |

The easiest way to author one: stand where you want the camera, run
`/crate admin setcam <crate>`, and copy the coordinate it writes into a
`camera_move` line. Repeat for each waypoint.

A step whose `delay` falls after `length-ticks` never runs — raise the length
if you add a late waypoint.

## Camera modes

```yaml
# config.yml
cinematic:
  camera-mode: spectator      # spectator | packet | teleport
  camera-eye-offset: 1.62
  camera-teleport-duration: 1
```

`spectator` is the default and works on both model backends.
`camera-eye-offset` exists because `setcam` records your **feet** — the offset
puts the camera back at eye level, so what you saw when you ran the command is
what the shot shows.

`camera-teleport-duration` controls how much the client smooths each camera
hop. It smooths the camera's **position** only; its facing is applied the
moment the packet arrives. A value above the one-tick move interval lets the
view lead the position, and the pan reads as judder — `1` keeps them in step.
Raise it for a softer, laggier glide, or `0` to disable smoothing.

## Crash recovery

If the server stops mid-shot, the interrupted opening is picked up on the
player's next join: they are restored to where they were standing, their game
mode and flight state are put back, and the reward is granted rather than lost.
Nothing needs to be configured for this.
