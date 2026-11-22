---
tags: [index]
aliases: [Home, Hearthmakers]
---

# Hearthmakers

**Hearthmakers** is a social voxel game of building and settling, inspired by Stonehearth, made by **The Gearfinder Studios**. Its art, sounds and interface are all its own.

## The game

- **One pioneer per player.** Each player plays one character directly, by point and click (or a controller), never a crowd from above.
- **Realms of one's own.** Every player has a realm to build in: dig and shape the land, raise buildings from blueprints, set down workbenches and furniture, farm, keep animals in pens.
- **Colonists.** A realm's colonists work its benches in professions (builder, farmer, guard, smith, miner, beekeeper, herder, engineer, botanist), each growing with its work.
- **Public worlds.** The server's admins make adventure worlds with villages, forts and dungeons, folk with shops and quests, mobs per biome, and party instances.
- **Together.** Chat with channels, parties, trades, friends, emotes.
- **A pioneer's growth.** Experience and levels, attributes, classes, gear, crafting, and research trees for the pioneer and for the realm.

## How it's played

The game stands alone: there is no server the team hosts. **Anyone hosts a server**, each with its own accounts, and players add servers by their address in the game's **Multiplayer** window.

The game is played in its apps, downloaded from the [releases](https://github.com/Hearthmakers/Hearthmakers-Releases/releases); the game's site, [hearthmakers.the-gearfinder.org](https://hearthmakers.the-gearfinder.org), holds the development blog.

- **The desktop app** (Windows and Linux): it keeps itself and the game up to date, and its **Singleplayer** hosts a server of one's own, alone or open to the house's network.
- **The Android app**: the same, on a phone in landscape.

Every feature works on touch screens too.

## Hosting a server

A server is one file, for Windows or Linux x64, with Node inside: downloaded from the [releases](https://github.com/Hearthmakers/Hearthmakers-Releases/releases) (those named **Server**), run in a folder of its own, set up in a `server.env` beside it. It serves its owner a **server panel** in the browser, on that machine only: the machine, logs, backups, settings and updates; players, bans and the chat; the public worlds, mobs, recipes and research; the board, events and statistics.

A server is made its owner's own with **plugins**: see below.

## Plugin API

Code a server runs to make it its owner's own: commands, rules, rewards, events, windows. The notes are in the `API` folder; start at [[Plugin API]].

- [[Plugin API]]: what a plugin is, and the map of every note
- [[Getting Started]]: a first plugin, from an empty folder to the server running it
- [[Managing Plugins]]: installing, turning off, settings and failures, for server owners
- [[Examples]]: whole plugins to start from

Reference:

- [[plugin.json]]: the plugin's card
- [[Setup]]: the setup function and the `game` object
- [[Events]]: what the game tells a plugin, and what it can cancel
- [[Commands]]: commands in the chat and the server's console
- [[Chat]]: lines to players, and announcements
- [[Players]]: finding players, items, experience, moving them, kicks and mutes
- [[Worlds]]: blocks, mobs, weather and the hour
- [[Windows]]: windows described as data, drawn in the game's look
- [[Settings and Data]]: what the owner sets, and what the plugin keeps
- [[Timers and Stopping]]: timers, and the server stopping
- [[Types]]: every shape, as TypeScript declarations

## Under the hood

TypeScript throughout: the game's client a web page with three.js, played inside the desktop and Android apps, the server on Colyseus, the voxel models in the game's own `.qb` format and mesher. A server runs its worlds on threads of their own, keeps its save in PGlite or Postgres, and is the authority on everything: players' games send requests and draw what the server says.
