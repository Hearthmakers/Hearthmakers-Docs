---
tags: [api, plugins, hosting]
---

# Managing Plugins

For whoever runs a server. See [[Plugin API]] for writing plugins.

> [!warning] Trusted code
> A plugin runs inside the server with all of its power: it can read and change anything the server can. Install only plugins you trust.

## Installing

1. Put the plugin's folder in the `plugins` folder, in the folder the server is started in (beside `server.env` and the executable on a packaged server). Make `plugins` if it isn't there.
2. Restart the server: plugins are loaded once, as it starts.

The server's window says what runs:

```
[plugins] 2 of 3 running: Hello 1.0.0, Daily Reward 2.1.0
[plugins] broken isn't running: it failed to start: …
```

To keep plugins somewhere else, set `PLUGINS_DIR` in `server.env` (relative to the folder the server is started in, or a whole path).

## Settings

A plugin's settings are in `data/plugins/<id>/settings.json`, written with the plugin's own the first time it starts. Change them there and restart. Replacing a plugin's folder with a newer version keeps them. See [[Settings and Data]].

## Turning one off or on

A plugin is on unless it's turned off. Turning one off or on takes effect **when the server next starts**: a plugin's code, once loaded, stays until the server stops.

- **In the server panel**: **Server → Plugins** lists every plugin in the folder with how it stands (running, failed, off), its version and author, why one failed, its commands, the events it listens to and its settings file. **Turn off** / **Turn on** keep the choice for the next start, and the list says what changes then; **Restart** restarts the server, warning the players on first (a packaged server). The panel's record keeps who turned what.
- **In the server's window**:

```
plugins              every plugin, how it stands, its commands and events
plugins off <id>     off from the next start
plugins on <id>      on from the next start
```

Which plugins are off is kept in the server's save. To take a plugin away for good, delete its folder; what it kept stays in the save, in case it comes back.

## When a plugin fails

- **Its card doesn't hold** ([[plugin.json]]): it isn't loaded; the window and the panel say why.
- **It throws as it sets up**, or takes more than 10 seconds: it's left out whole (its commands, listeners and timers taken back), and the panel says why (see [[Setup]]).
- **It throws later**, in a listener, a command or a timer: the server's window says so, marked `[plugin <id>]`, and the plugin and the game go on.

A failed plugin never takes the server down. To try again after fixing it, restart.

## Backups

What plugins keep, which ones are off, and their settings files are all in the server's backups (the save and the data folder), and come back with a backup put back.
