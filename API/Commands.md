---
tags: [api, plugins, reference]
---

# Commands

```js
const remove = game.command({
  name: 'daily',
  aliases: ['d'],
  help: 'take your daily reward',
  run: ({ player, args, line, reply }) => { … },
});
remove(); // takes it away
```

`game.command(command)` adds a command, typed `/daily` in the chat, or `daily` in the server's window, and returns what takes it away again.

| Field | | |
|---|---|---|
| `name` | required | What's typed: lower-case letters, digits, `-` and `_`, up to 32. Typed in any case. |
| `aliases` | optional | Other names it's typed by, with the same rules. |
| `help` | optional | One line, shown by `/help` in the chat and `help` in the server's window. |
| `adminOnly` | optional | Only the server's admins may type it in the chat. Others are told they may not. The server's window always may. |
| `where` | optional | `'chat'`, `'console'` or `'both'` (the default). |
| `run` | required | What it does. May be `async`. |

`run` is given:

| | |
|---|---|
| `player` | Who typed it (see [[Types]]); `null` in the server's window. |
| `args` | The words after the command's name. |
| `line` | Those words joined by one space. |
| `reply(text)` | Answers whoever typed it: in their chat under the plugin's name (up to 1000 letters), or in the server's window. |

```js
game.command({
  name: 'give-logs',
  adminOnly: true,
  help: 'give-logs <character> <count>',
  run: ({ args, reply }) => {
    const [name, count] = args;
    const character = game.players.character(name ?? '');
    if (!character) return reply(`No character called ${name}.`);
    reply(`Gave ${game.players.give(character.id, 'oak_log', Number(count) || 1)} logs.`);
  },
});
```

## Names already taken

The game's own commands come first. A name the game already uses isn't taken, and neither is one another plugin took first (plugins load in their ids' order). The server's window says which names a plugin couldn't have; the command keeps its other names.

- **In the chat**, the game's own: `help`, `ajuda`, `?`, `w`, `whisper`, `msg`, `tell`, `party`, `invite`, `convidar`, `summon`, `puxar`, `trade`, `troca`, `trocar`, `friend`, `amigo`, `unfriend`, `block`, `bloquear`, `unblock`, `desbloquear`; the admins' console commands (`accounts`, `passwd`, `admin`, `grant`, `give`, `emote`, `items`, `world`, `time`, `weather`, `scene`); every chat channel's id and its first letter.
- **In the server's window**, the game's own: `accounts`, `passwd`, `admin`, `grant`, `give`, `emote`, `world`, `time`, `weather`, `scene`, `items`, `help`, `stop`, `update`, `panel`, `newkey`, `plugins`.
- The emote commands (`/dance`, `/wave` and the rest) are played by the player's game and never reach the server: a plugin's command of the same name is never typed.

## In the server's window

A command typed in the window has no `player`, and `reply` prints there. A command meant only for the server's owner takes `where: 'console'`. Admins typing commands in the chat reach only the game's console commands, never a plugin's console-only ones.
