---
tags: [api, plugins, reference]
---

# Worlds

`game.worlds` reads and changes the worlds. A world goes by its id: `realm:<id>` for a player's realm, `instance:<id>` for a public world or a party's instance (see [[Types]]).

| | |
|---|---|
| `open()` | The worlds open now: someone is in them, or just left. |
| `players(world)` | Who's in a world now. |
| `setBlock(world, cell, block)` | Sets a block of an open world's ground. |
| `spawnMob(world, mob, at, count = 1)` | Brings mobs out in an open world. |
| `weather(world, weather, minutes?)` | Sets a world's weather. |
| `setHour(hour)` | Moves every world's clock. |

## Changing an open world

`setBlock` and `spawnMob` reach only a world that's **open**, and return `false` when it isn't, or when what's asked can't be. `true` means the world's room was told; it does it on the world's own thread a moment later, by the world's rules.

```js
// A pillar of stone beside a player, in the world they're in.
const player = game.players.get('Ana');
if (player?.world) for (let y = 10; y < 14; y++) game.worlds.setBlock(player.world, { x: 2, y, z: 0 }, 'stone');
```

**setBlock(world, { x, y, z }, block)**: `x`, `y`, `z` are whole numbers; `x` and `z` run from minus half the world's size to half of it, `y` up from 0 (the bedrock). `block` is one of `air` (digs it out), `grass`, `dirt`, `stone`, `earth`, `wood`, `ironVein`, `copperVein`, `water`, `ice`. The room leaves alone what can't change there: bedrock, a building's blocks, a cell with something standing on it or in it, a cell outside the land. What changes is kept where the world is kept (a realm, a public world), as digging and filling are; water flows into a cell dug beside it.

**spawnMob(world, mob, { x, z }, count)**: `mob` is a mob kind this server has (`game.game.mobs()`); `count` up to 20 at once. They come out at free spots near `x`, `z`, on the ground, and live as any other mob of that kind there. A world with no mobs of its own leaves it.

## Sky

**weather(world, weather, minutes)**: `weather` is `clear`, `rain`, `storm`, `fog`, `snow`, `blizzard`, `sandstorm` or `smog`, set for `minutes` (or, with none, until it's set again); `null` gives the world back to its biome's own weather. Any world, open or not. `false` for a world id or weather that isn't one. Caves have no weather.

**setHour(hour)**: moves the clock every world's time of day is read off, so it reads `hour` (0 to 24, decimals allowed) now. It's kept through restarts, as an admin's `time` is.
