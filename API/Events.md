---
tags: [api, plugins, reference]
---

# Events

```js
const stop = game.on('join', ({ player }) => { … });
stop(); // listens no more
```

`game.on(event, listener)` listens to something the game does, and returns what stops listening. A listener may be `async`. If it throws, the server's window says so and the game goes on (see [[Setup]]). `player` and `character` are described in [[Types]].

| Event | When | Told |
|---|---|---|
| `join` | A player came onto the server (not a dropped connection coming back) | `{ player }` |
| `leave` | A player left the server | `{ player }` |
| `chat` | A line is about to be said on a channel | `{ player, channel, text, cancel(reason?) }` |
| `enterWorld` | A player is about to come into a world | `{ player, character, world, cancel(reason?) }` |
| `worldEntered` | A player came into a world | `{ player, character, world }` |
| `worldLeft` | A player left a world, for another or for none | `{ player, world }` |
| `craft` | A player made something | `{ player, character, recipe, times }` |
| `levelUp` | A character went up a level by experience earned | `{ player, character, level }` |
| `mobSlain` | A mob was slain | `{ world, mob, player, at }` |
| `harvest` | A player gathered something | `{ world, player, items }` |
| `death` | A player's pioneer went down | `{ world, player, character }` |

## Cancelling: `chat` and `enterWorld`

These two happen before the game does the thing, and a listener can stop it by calling `cancel()`:

```js
game.on('chat', (event) => {
  if (/badword/i.test(event.text)) event.cancel('Please keep it friendly.');
});

game.on('enterWorld', (event) => {
  if (event.world === 'instance:arena' && event.character.level < 10) event.cancel('The arena opens at level 10.');
});
```

- `cancel` counts only **while the listener runs**: call it before the listener returns, never after an `await`.
- The first plugin to cancel wins, and its reason (up to 300 letters) is the one the player is told.
- **chat**: the line never goes out. With a reason, the player gets it in the chat under the plugin's name; with none, nothing is said.
- **enterWorld**: the player stays where they were, and their game shows the reason (or, with none, the game's own "This server doesn't let you in there just now."). `player.world` is the world they're in now (or `null`).
- Keep `enterWorld` listeners quick: the player waits for them to come in, and the world's thread waits for the answer.

`chat` is told only of lines said on a channel (`global`, `realm`, `party` or the server's own: see `CHAT_CHANNELS`). It is never told of whispers, or of commands.

## The others, in detail

- **join**, **leave**: once per player coming on and going away. A second connection taking a first one's place is not a new join.
- **worldEntered**, **worldLeft**: moving from one world to another tells `worldLeft` (the old one) then `worldEntered` (the new one). `world` is a world's id (see [[Types]]).
- **craft**: `recipe` is the recipe's id, `times` how many it was made at once.
- **levelUp**: `level` is the new level (a big gain may jump several). Told whenever experience takes a character up: earned in play, given by an admin in the panel, or by a plugin ([[Players]]' `experience`); not for a level an admin sets by hand. `player` is `null` only for a character with no account left.
- **mobSlain**: `mob` is the mob's kind (as `game.game.mobs()` lists them); `at` the cell it was slain on; `player` whoever struck the last blow, or `null` when a colonist or a pet did.
- **harvest**: told when a player (not a colonist) fells a tree, breaks a rock, picks a plant or works a resource node. `items` is what went into the bag (`[{ item, count }]`), so nothing is told when the bag took nothing.
- **death**: told as the pioneer goes down, before it rises.

`mobSlain`, `harvest` and `death` come from the worlds' own threads: they arrive a moment after the fact, never holding the world up, and only while some plugin listens to them.
