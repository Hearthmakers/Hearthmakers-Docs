---
tags: [api, plugins, reference]
---

# plugin.json

Every plugin's folder holds a `plugin.json`: its card. A folder without one isn't a plugin and is left alone, as is a folder whose name starts with a dot. See [[Plugin API]].

```json
{
  "name": "Daily Reward",
  "version": "2.1.0",
  "author": "Ana",
  "description": "Gives each player a reward once a day.",
  "main": "main.mjs",
  "settings": { "item": "oak_log", "count": 5, "hours": 24 }
}
```

| Field | Default | Rules |
|---|---|---|
| (the folder's name) | | The plugin's **id**: lower-case letters, digits, `-` and `_`, starting with a letter or digit, up to 40. Not a field: the folder is named so. |
| `name` | the id | How it's shown: the console, the panel, its chat lines and windows. Up to 60 letters. |
| `version` | `0.0.0` | Any text up to 60 letters. |
| `author` | none | Up to 60 letters. |
| `description` | none | Up to 300 letters, shown in the panel. |
| `main` | `main.mjs` | The file its code starts from: a `.mjs` or `.js` inside the folder (no `..`, no absolute path). |
| `settings` | `{}` | An object: the plugin's settings as it comes. See [[Settings and Data]]. |

## When the card doesn't hold

The plugin isn't loaded, and the server says why, in its window and in the panel (see [[Managing Plugins]]):

- the folder's name can't be an id;
- `plugin.json` isn't JSON, or isn't an object;
- `main` isn't a `.mjs` or `.js` inside the folder, or the file isn't there;
- `settings` isn't an object.

## The order

Plugins are loaded one after another, in their ids' order. A plugin's command name taken by an earlier plugin isn't taken (see [[Commands]]).
