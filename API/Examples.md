---
tags: [api, plugins, examples]
---

# Examples

Whole plugins to start from. Each is a folder with the `plugin.json` and `main.mjs` shown. See [[Getting Started]] for where they go.

## Daily reward

A reward once a day per character, asked for with a window: [[Windows]], [[Settings and Data]], [[Commands]].

`plugins/daily-reward/plugin.json`

```json
{
  "name": "Daily Reward",
  "version": "1.0.0",
  "description": "A reward once a day, taken with /daily.",
  "settings": { "item": "oak_log", "count": 5, "hours": 24 }
}
```

`plugins/daily-reward/main.mjs`

```js
export default function setup(game) {
  const { item, count, hours } = game.settings;
  const wait = hours * 60 * 60 * 1000;

  game.command({
    name: 'daily',
    help: 'take your daily reward',
    where: 'chat',
    run: async ({ player, reply }) => {
      const character = player?.character;
      if (!character) return reply('Pick a pioneer first.');
      const taken = game.store.get('taken') ?? {};
      const left = (taken[character.id] ?? 0) + wait - Date.now();
      if (left > 0) return reply(`Come back in ${Math.ceil(left / 3_600_000)} hours.`);

      const answer = await game.windows.show(player.id, {
        title: 'Daily reward',
        text: 'Your reward for today:',
        items: [{ item, count }],
        buttons: [{ id: 'take', label: 'Take' }, { id: 'later', label: 'Later', style: 'secondary' }],
      });
      if (answer?.button !== 'take') return;
      const given = game.players.give(character.id, item, count);
      if (!given) return reply('Your bag is full: make room and try again.');
      game.store.set('taken', { ...game.store.get('taken'), [character.id]: Date.now() });
      reply(`You took ${given}.`);
    },
  });
}
```

## Chat guard

Lines with words the owner lists never go out: [[Events]].

`plugins/chat-guard/plugin.json`

```json
{ "name": "Chat Guard", "version": "1.0.0", "settings": { "words": ["badword"], "warning": "Please keep it friendly." } }
```

`plugins/chat-guard/main.mjs`

```js
export default function setup(game) {
  const words = game.settings.words.map((word) => String(word).toLowerCase());
  game.on('chat', (event) => {
    const text = event.text.toLowerCase();
    if (words.some((word) => text.includes(word))) event.cancel(game.settings.warning);
  });
}
```

## Arena gate

A public world only from level 10, and a welcome there: [[Events]], [[Players]].

`plugins/arena-gate/plugin.json`

```json
{ "name": "Arena Gate", "version": "1.0.0", "settings": { "world": "instance:arena", "level": 10 } }
```

`plugins/arena-gate/main.mjs`

```js
export default function setup(game) {
  const { world, level } = game.settings;
  game.on('enterWorld', (event) => {
    if (event.world === world && event.character.level < level) event.cancel(`The arena opens at level ${level}.`);
  });
  game.on('worldEntered', ({ player, world: entered }) => {
    if (entered === world) game.chat.tell(player.id, 'Welcome to the arena. Fight well!');
  });
}
```

## Mob bounty

Coins for every mob a player slays, counted for a leaderboard: [[Events]], [[Settings and Data]], [[Commands]].

`plugins/mob-bounty/plugin.json`

```json
{ "name": "Mob Bounty", "version": "1.0.0", "settings": { "coins": 2 } }
```

`plugins/mob-bounty/main.mjs`

```js
export default function setup(game) {
  game.on('mobSlain', ({ player }) => {
    if (!player?.character) return;
    game.players.give(player.character.id, 'coin', game.settings.coins);
    const slain = game.store.get('slain') ?? {};
    slain[player.name] = (slain[player.name] ?? 0) + 1;
    game.store.set('slain', slain);
  });

  game.command({
    name: 'bounty',
    help: 'the best hunters',
    run: ({ reply }) => {
      const best = Object.entries(game.store.get('slain') ?? {}).sort((a, b) => b[1] - a[1]).slice(0, 5);
      reply(best.length ? best.map(([name, count], i) => `${i + 1}. ${name}: ${count}`).join('\n') : 'No hunts yet.');
    },
  });
}
```

## Night event

Every evening at a set real hour: a storm over a public world, more mobs, and the word to everyone: [[Timers and Stopping]], [[Worlds]], [[Chat]].

`plugins/night-event/plugin.json`

```json
{ "name": "Night Event", "version": "1.0.0", "settings": { "world": "instance:fields", "hour": 21, "mob": "cubling" } }
```

`plugins/night-event/main.mjs`

```js
export default function setup(game) {
  const { world, hour, mob } = game.settings;
  let lastDay = '';
  game.every(60_000, () => {
    const now = new Date();
    const today = now.toDateString();
    if (now.getHours() !== hour || lastDay === today) return;
    lastDay = today;
    game.worlds.weather(world, 'storm', 30);
    if (game.worlds.open().includes(world)) game.worlds.spawnMob(world, mob, { x: 0, z: 0 }, 10);
    game.chat.announce('A storm rolls in over the fields: the night event has begun!');
  });
}
```
