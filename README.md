# CineCrates

Camera-driven crate openings for Minecraft servers. One jar covers Spigot,
Paper and Folia from 1.21 through 26.x.

A crate in CineCrates is a YAML file plus a placement in the world. What makes
it different from a menu with a spinning animation is that the opening is a
**shot**: the camera can be taken over and flown along a path you author, the
model plays its own open animation, and the reward is announced the moment it
actually settles rather than at a fixed time.

## What you can build

| | |
| --- | --- |
| **Ten animation styles** | From a full cinematic with a camera path down to `instant`. Everything except `cinematic` needs zero coordinates. |
| **Rewards with real odds** | Weighted rewards, several per opening, guaranteed pity rewards, and a preview screen that computes the chances rather than displaying a number you typed. |
| **Keys or no keys** | Physical keys, virtual balances that survive death and work offline, money through Vault, a permission node, or free. |
| **Your own timeline** | Sounds, particles, titles, action bars, console commands and camera moves, each on its own tick offset. |
| **No model plugin required** | BetterModel and ModelEngine are auto-detected and optional; `block: CHEST` gives you an ordinary block crate with the same behaviour. |

## Where to start

If you have just installed the plugin, work through
[Installation](getting-started/installation.md) and then
[Your first crate](getting-started/first-crate.md) — that is about ten minutes
and leaves you with a working crate you can click.

Once that works, [Anatomy of a crate file](crates/anatomy.md) walks the whole
configuration from top to bottom, and each section links to the page that
covers it in depth.

## A note on the examples

Every snippet in this documentation is taken from a configuration that the
plugin actually parses. Where a page describes a behaviour that is easy to get
wrong — the two clocks behind reward timing, or why a left-click may not reach
a model crate — it says so explicitly rather than leaving you to discover it.
