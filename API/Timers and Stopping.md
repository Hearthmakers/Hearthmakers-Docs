---
tags: [api, plugins, reference]
---

# Timers and Stopping

```js
const stop = game.every(60_000, () => game.chat.broadcast('Another minute on the server!'));
game.after(5_000, () => game.log('five seconds after setup'));
game.onStop(async () => { /* last words */ });
```

| | |
|---|---|
| `every(ms, run)` | Runs `run` every `ms` milliseconds (at least 50); returns what stops it. |
| `after(ms, run)` | Runs `run` once, `ms` milliseconds from now; returns what calls it off. |
| `onStop(run)` | Runs `run` when the server stops. |

- `run` may be `async`. If it throws, the server's window says so, and an `every` goes on.
- Timers never keep the server from stopping, and every one ends as it stops (or if the plugin fails as it sets up: see [[Setup]]).
- Timers stop with the server: a reward every 24 hours of play needs the time kept in [[Settings and Data|the store]], read again at start.

## When the server stops

When the server stops (its `stop`, Ctrl+C, a restart from the panel):

1. everyone is let go from the worlds, their places saved;
2. the plugins hear nothing more from the game;
3. plugin after plugin: its timers end, its `onStop` functions run in the order they were added (each has **5 seconds**, after which it's left behind), its windows are put away, and what it keeps is written;
4. the rest of the save is written last.

So an `onStop` may still `game.store.set(…)` and it's kept. Players are no longer in the worlds by then.

A plugin's code is loaded once per start: to run a changed plugin, restart the server.
