---
tags: [api, plugins, reference]
---

# Settings and Data

Two kinds of a plugin's own things: its **settings**, which the server's owner sets, and its **data**, which the plugin itself keeps.

## Settings: `game.settings`

The plugin's card gives its settings as it comes ([[plugin.json]]):

```json
{ "name": "Daily Reward", "settings": { "item": "oak_log", "count": 5, "hours": 24 } }
```

The first time the plugin starts, the server writes them to `data/plugins/<id>/settings.json`, so the owner finds what can be set. The owner changes them there and restarts. `game.settings` is the card's settings under the owner's file:

- only the keys the card has count: a key the owner made up (or misspelled) never reaches the plugin;
- a value counts only if it's of the same kind as the card's (text for text, a number for a number, a list for a list, an object for an object); a card's `null` takes anything;
- a file that isn't JSON is said in the server's window, and the card's settings are used.

The file lives in the data folder, not the plugin's: replacing the plugin's folder with a newer version keeps it, and it's in the server's backups. Settings are read once, as the server starts.

```js
const { item, count } = game.settings;
```

## Data: `game.store`

What a plugin keeps from one start to the next, by key: any value JSON can hold.

```js
const visits = game.store.get('visits') ?? {};
visits[player.id] = (visits[player.id] ?? 0) + 1;
game.store.set('visits', visits);
```

| | |
|---|---|
| `get(key)` | The value kept, or `undefined`. A copy: changing it changes nothing until it's `set` again. |
| `set(key, value)` | Keeps a copy of `value`, as JSON holds it. |
| `delete(key)` | Takes a key out; whether it was there. |
| `keys()` | Every key kept. |

- A key is text of 1 to 200 letters. A value is anything JSON holds, up to **256 KB** of JSON; `set` throws for one too big, or that JSON can't hold (`undefined`, a function).
- It's written to the server's save a second after a change, before a backup is made, and as the server stops; it's in the save's backups.
- A plugin turned off and on again finds its data as it left it. Deleting the plugin's folder leaves its data in the save.
- It's read whole as the server starts and held in memory: keep big things few, or split them over keys.

## Files of its own

`game.plugin.dataFolder` is a folder for files the plugin writes (`data/plugins/<id>`). Make it first if it isn't there (`mkdir` with `recursive`). Files there are in the data folder's backups too.
