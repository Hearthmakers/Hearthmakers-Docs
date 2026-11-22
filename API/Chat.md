---
tags: [api, plugins, reference]
---

# Chat

```js
game.chat.tell(player.id, 'Your reward is waiting: type /daily');
game.chat.broadcast('The arena opens in 5 minutes!');
game.chat.announce('Double experience all weekend!');
```

| | |
|---|---|
| `tell(playerId, text)` | A line in one player's chat. |
| `broadcast(text)` | A line in the chat of everyone playing now. |
| `announce(text)` | The server's announcement to everyone on: set apart in gold, and kept, so whoever comes in soon after reads it too. |

- `tell` and `broadcast` lines show **under the plugin's name**, on every tab of the chat. They aren't kept: a player who comes in later doesn't see them.
- A line keeps its line breaks (`\n`); other control characters are taken out. Up to 1000 letters.
- A player who isn't on gets nothing: `tell` does nothing for them.
- To listen to what players say, or keep a line from going out, see `chat` in [[Events]]. To answer a command, see `reply` in [[Commands]].
