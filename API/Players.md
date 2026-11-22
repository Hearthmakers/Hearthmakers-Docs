---
tags: [api, plugins, reference]
---

# Players

`game.players` finds accounts and characters and acts on them. A **player** is an account; a **character** is one of its pioneers (see [[Types]]). Items go by their ids (`game.game.items()` lists them).

## Finding

| | |
|---|---|
| `online()` | Everyone playing now. |
| `get(idOrName)` | An account by its id, or by its name in any case; `null` if there's none. |
| `character(idOrName)` | A character by its id, or by its name in any case; `null` if there's none. |

A player's `name` is its account's name; its `character` is the pioneer it plays now, or last picked in the portal (with a name of its own); `world` is the world it's in, or `null`.

## Items and experience

| | Returns |
|---|---|
| `give(characterId, item, count = 1)` | How many went into the bag (the bag may be full). |
| `take(characterId, item, count = 1)` | How many it took. |
| `count(characterId, item)` | How many the bag holds (what's worn isn't counted). |
| `experience(characterId, amount)` | How many levels it went up. |

- `count` must be a whole number from 1. An item that isn't the game's gives and takes nothing.
- `experience` gives the amount as earned (not multiplied by the server's `EXPERIENCE_RATE`); the character's class and research get their shares, as with any experience. A level gained tells `levelUp` (see [[Events]]).
- What changes in a bag shows at once in the player's game, and is kept in the save.

## Moving players

| | Returns `false` when |
|---|---|
| `sendHome(characterId)` | there's no such character. In a world, it goes home (its realm, or the arrival); out of one, it starts at home next time. |
| `sendTo(playerId, world)` | the player isn't on, is in no world, is already there, or `world` is no world id. |
| `place(playerId, x, z)` | the player is in no world (or `x`, `z` aren't numbers). |

- **sendTo** pulls the player as a party's leader does: their game turns the page to that world, and for a minute they may walk in even where they aren't invited (never where they're banned). `enterWorld` listeners are still asked.
- **place** puts the player down on the ground of the world they're in, at the free spot nearest `x`, `z` (kept inside the world's land), their work dropped. A world's middle is `0, 0`.

## Kicks and mutes

| | |
|---|---|
| `kick(playerId, reason?)` | Shows the player out of the server; they may come back. `false` if there's no such account. |
| `mute(playerId, minutes, reason?)` | Silences them in the chat for so many minutes, or until unmuted with `minutes` `null`. The server's admins see it as set by the plugin. `false` if there's no such account, or `minutes` isn't a number above 0 or `null`. |
| `unmute(playerId)` | Lets them talk again. |
