# Reroll

After the animation, before the reward is handed over, the player can be
offered a second roll for a price.

```yaml
reroll:
  enabled: true
  max: 1
  price:
    type: money       # money | free | key
    amount: 500
```

## How it behaves

* A reroll is **guaranteed to land on a different reward** than the one it
  replaces. It is not a fresh roll that might return the same thing.
* It does **not** re-check cooldowns or daily limits, because it is still the
  same opening.
* Every way out of the screen — taking the reward, closing the window, logging
  out, a server restart — claims whatever is currently on offer. A spent key
  can never yield nothing.
* `max` caps how many times one opening may be rerolled.

## Two rules that decide whether anyone uses it

**Price it below a key.** If a reroll costs a full key, opening again is
strictly better: the player gets another animation, another milestone tick, and
another chance at everything rather than a swap. Money or free is what makes it
attractive.

**Rerolling forfeits the milestone reward.** This is deliberate. Pity exists so
that a run of bad luck ends in a guarantee; if a player could reroll away from
a milestone reward and still keep the counter, the guarantee would be optional.

## When the screen does not appear

A crate that grants several rewards at once skips the reroll screen entirely —
`rewards-per-open` greater than one, or a weighted table that rolled more than
one. "Which of these three do I reroll?" is a complicated interface for very
little gain, and rerolling the whole set would destroy a milestone reward
sitting among them.

If you want reroll on a crate, keep it at one reward per opening:

```yaml
rewards-per-open: 1
reroll:
  enabled: true
```

## The reward announcement

The title that names the reward follows the reroll: the player is told what
they actually receive, not what the animation happened to show before they
rerolled. See [Reward presentation](../animations/reward-presentation.md).
