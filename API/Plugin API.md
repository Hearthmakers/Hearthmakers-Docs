---
tags: [api, plugins]
aliases: [API, Plugins]
---

# Plugin API

A **plugin** is code a Hearthmakers server runs to make the server its owner's own: commands, rules, rewards, events, windows. The server hands each plugin one object, `game`, and everything the plugin does goes through it, by the game's own rules and records: the save, the server panel and the worlds keep up with whatever a plugin does.

> [!warning] Trusted code
> A plugin runs inside the server with all of its power, as Minecraft's plugins do. Install only plugins you trust, from people you trust.

Nothing of a plugin ever goes to the players' games. What players see of it is what the game already shows: its lines in the chat, its windows drawn in the game's own look, and what it does to the world.

## Start here

- [[Getting Started]]: a first plugin, from an empty folder to the server running it.
- [[Managing Plugins]]: installing, turning off, settings, what happens when one fails (for server owners).

## Reference

| Note | What it covers |
|---|---|
| [[plugin.json]] | The plugin's card: id, name, version, its code's file, its settings |
| [[Setup]] | The setup function, `game.plugin`, `game.server`, `game.game`, logging, failures |
| [[Events]] | `game.on`: what the game tells a plugin, and the two events it can cancel |
| [[Commands]] | `game.command`: commands typed in the chat and in the server's console |
| [[Chat]] | `game.chat`: lines to one player, to everyone, and announcements |
| [[Players]] | `game.players`: finding players, items, experience, moving them, kicks and mutes |
| [[Worlds]] | `game.worlds`: open worlds, blocks, mobs, weather and the hour |
| [[Windows]] | `game.windows`: windows described as data, drawn in the game's look |
| [[Settings and Data]] | `game.settings` and `game.store`: what the owner sets, and what the plugin keeps |
| [[Timers and Stopping]] | `game.every`, `game.after`, `game.onStop` |
| [[Types]] | Every shape the API takes and gives, as TypeScript declarations |
| [[Examples]] | Whole plugins to start from |

## At a glance

```js
// plugins/hello/main.mjs
export default function setup(game) {
  game.on('join', ({ player }) => game.chat.tell(player.id, `Welcome, ${player.name}!`));
  game.command({ name: 'hello', help: 'says hello back', run: ({ reply }) => reply('Hello!') });
}
```

```json
// plugins/hello/plugin.json
{ "name": "Hello", "version": "1.0.0" }
```
