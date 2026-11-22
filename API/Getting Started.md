---
tags: [api, plugins, guide]
---

# Getting Started

A first plugin: a welcome line for everyone who comes on, and a `/hello` command. See [[Plugin API]] for the whole map.

## 1. The folder

Plugins live in a `plugins` folder in the folder the server is started in: beside the executable and `server.env` on a packaged server. The `PLUGINS_DIR` setting in `server.env` points elsewhere. Each plugin is a folder of its own, and the folder's name is the plugin's **id**: lower-case letters, digits, `-` and `_`.

```
my-server/
  hearthmakers-server-windows-x64.exe
  server.env
  data/
  plugins/
    hello/
      plugin.json
      main.mjs
```

## 2. The card

`plugin.json` says what the plugin is. Only the file itself is needed: every field has a default. See [[plugin.json]].

```json
{
  "name": "Hello",
  "version": "1.0.0",
  "author": "You",
  "description": "Welcomes players and answers /hello.",
  "settings": { "greeting": "Welcome" }
}
```

## 3. The code

`main.mjs` is a JavaScript module. Its default export takes `game` and sets the plugin up. See [[Setup]].

```js
export default function setup(game) {
  game.on('join', ({ player }) => {
    game.chat.tell(player.id, `${game.settings.greeting}, ${player.name}!`);
  });

  game.command({
    name: 'hello',
    help: 'says hello back',
    run: ({ player, reply }) => reply(player ? `Hello, ${player.name}!` : 'Hello, console!'),
  });
}
```

## 4. Run it

Start the server, or restart it if it's running: plugins are loaded once, as the server starts. Its window says what runs:

```
[plugins] 1 of 1 running: Hello 1.0.0
```

Come onto the server: the chat shows the welcome under the plugin's name. Type `/hello` in the chat, or `hello` in the server's window.

## 5. Change it

- Change the greeting in `data/plugins/hello/settings.json` (written the first time the plugin starts), then restart. See [[Settings and Data]].
- Change the code, then restart. A plugin's code is read once per start.
- If something goes wrong, the server's window and the panel's **Server → Plugins** say why. See [[Managing Plugins]].

## Writing in TypeScript

The server runs plain JavaScript modules (`.mjs` or `.js`). Write in TypeScript if you like and ship what it compiles to. [[Types]] has the declarations to paste into your project. A plugin may `import` files of its own folder; npm packages it needs go in its folder already bundled (with esbuild, for example).

## Next

- [[Events]] for everything the game tells a plugin.
- [[Windows]] to ask players something.
- [[Examples]] for whole plugins.
