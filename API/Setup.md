---
tags: [api, plugins, reference]
---

# Setup

A plugin's code is an ES module. The server imports the file its [[plugin.json]] names, and calls its **default export**, or else an export named `setup`, with `game`, the plugin's whole API.

```js
export default async function setup(game) {
  game.log(`${game.plugin.name} ${game.plugin.version} starting on ${game.server.name}`);
  // listen, add commands, start timers…
}
```

- Setup may be `async`. It has **10 seconds** to finish.
- Setup runs once, as the server starts, before any player can come in, so a plugin hears everything from the first player on.
- Listeners, commands and timers may also be added later, from a listener or a timer.

## When setup fails

If setup throws, rejects, takes more than 10 seconds, or the module exports no function, the plugin **fails**:

- everything it added is taken back: its listeners, commands, timers and open windows;
- nothing more is taken from it: a call it makes later does nothing;
- the server's window and the panel say why (see [[Managing Plugins]]).

Other plugins and the game go on.

## When a listener fails

If a listener, a command, a timer or an `onStop` throws (or its promise rejects), the server's window says so, marked with the plugin's id, and the game goes on. The plugin keeps running.

## What `game` holds

| | |
|---|---|
| `game.plugin` | `{ id, name, version, folder, dataFolder }`: the plugin itself. `folder` is its code's folder; `dataFolder` is a folder of its own for files it writes (`data/plugins/<id>`, made when its settings file is written; make it yourself before writing there). Both are paths as the server sees them, relative to the folder it was started in when its settings are. |
| `game.server` | `{ name, version, commit }`: the server's name, the game's version, and the commit it runs (or `null`). |
| `game.settings` | The plugin's settings. See [[Settings and Data]]. |
| `game.log(…)`, `game.warn(…)`, `game.error(…)` | Lines in the server's window (and its log file), marked `[plugin <id>]`. |
| `game.on` | [[Events]] |
| `game.command` | [[Commands]] |
| `game.chat` | [[Chat]] |
| `game.players` | [[Players]] |
| `game.worlds` | [[Worlds]] |
| `game.windows` | [[Windows]] |
| `game.store` | [[Settings and Data]] |
| `game.every`, `game.after`, `game.onStop` | [[Timers and Stopping]] |
| `game.game` | What the game holds: `items()` (every item's id), `mobs()` (every mob kind this server has), `isItem(id)`. |

## Where it runs

All of a plugin's code runs on the server's main thread, beside the lobby and the chat, one plugin after another. Keep listeners quick: long work belongs in a timer, or split up with `await`.
