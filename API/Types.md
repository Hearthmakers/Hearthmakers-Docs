---
tags: [api, plugins, reference]
---

# Types

Every shape the API takes and gives. Writing in TypeScript, paste this into a `hearthmakers-plugin.d.ts` of your project and type your setup with it:

```ts
import type { PluginApi } from './hearthmakers-plugin';
export default function setup(game: PluginApi): void { … }
```

Writing plain JavaScript, the same file gives your editor the types through JSDoc:

```js
/** @param {import('./hearthmakers-plugin').PluginApi} game */
export default function setup(game) { … }
```

## Ids

| | Looks like |
|---|---|
| A player's id | the account's id, as `player.id` gives it |
| A character's id | as `character.id` gives it |
| A world's id | `realm:<id>` (a player's realm) or `instance:<id>` (a public world, a party's instance `instance:x-<id>`) |
| An item's id | `oak_log`, `stone`, `coin`…: `game.game.items()` lists them all |
| A mob's kind | `cubling`, `entling`…: `game.game.mobs()` lists this server's |

## hearthmakers-plugin.d.ts

```ts
export interface PluginApi {
  readonly plugin: { id: string; name: string; version: string; folder: string; dataFolder: string };
  readonly server: { name: string; version: string; commit: string | null };
  readonly settings: Record<string, unknown>;

  log(...parts: unknown[]): void;
  warn(...parts: unknown[]): void;
  error(...parts: unknown[]): void;

  on<E extends PluginEventName>(event: E, listener: (event: PluginEvents[E]) => void | Promise<void>): () => void;
  command(command: PluginCommand): () => void;

  readonly chat: PluginChat;
  readonly players: PluginPlayers;
  readonly worlds: PluginWorlds;
  readonly windows: PluginWindows;
  readonly store: PluginStore;

  every(ms: number, run: () => void | Promise<void>): () => void;
  after(ms: number, run: () => void | Promise<void>): () => void;
  onStop(run: () => void | Promise<void>): void;

  readonly game: { items(): string[]; mobs(): string[]; isItem(id: string): boolean };
}

export interface PluginPlayer {
  id: string;
  /** The account's name (its pioneer's is `character.name`). */
  name: string;
  admin: boolean;
  online: boolean;
  /** The character it plays now (or last picked). */
  character: PluginCharacter | null;
  /** The world it's in, if it's in one. */
  world: string | null;
}

export interface PluginCharacter {
  id: string;
  name: string;
  accountId: string;
  level: number;
  experience: number;
}

export interface PluginCell {
  x: number;
  y: number;
  z: number;
}

export interface PluginEvents {
  join: { player: PluginPlayer };
  leave: { player: PluginPlayer };
  chat: { player: PluginPlayer; channel: string; text: string; cancel(reason?: string): void };
  enterWorld: { player: PluginPlayer; character: PluginCharacter; world: string; cancel(reason?: string): void };
  worldEntered: { player: PluginPlayer; character: PluginCharacter; world: string };
  worldLeft: { player: PluginPlayer; world: string };
  craft: { player: PluginPlayer; character: PluginCharacter; recipe: string; times: number };
  levelUp: { player: PluginPlayer | null; character: PluginCharacter; level: number };
  mobSlain: { world: string; mob: string; player: PluginPlayer | null; at: PluginCell };
  harvest: { world: string; player: PluginPlayer; items: { item: string; count: number }[] };
  death: { world: string; player: PluginPlayer; character: PluginCharacter };
}
export type PluginEventName = keyof PluginEvents;

export interface PluginCommand {
  name: string;
  aliases?: string[];
  help?: string;
  adminOnly?: boolean;
  where?: 'chat' | 'console' | 'both';
  run(command: PluginCommandRun): void | Promise<void>;
}

export interface PluginCommandRun {
  player: PluginPlayer | null;
  args: string[];
  line: string;
  reply(text: string): void;
}

export interface PluginChat {
  tell(playerId: string, text: string): void;
  broadcast(text: string): void;
  announce(text: string): void;
}

export interface PluginPlayers {
  online(): PluginPlayer[];
  get(idOrName: string): PluginPlayer | null;
  character(idOrName: string): PluginCharacter | null;
  give(characterId: string, item: string, count?: number): number;
  take(characterId: string, item: string, count?: number): number;
  count(characterId: string, item: string): number;
  experience(characterId: string, amount: number): number;
  sendHome(characterId: string): boolean;
  sendTo(playerId: string, world: string): boolean;
  place(playerId: string, x: number, z: number): boolean;
  kick(playerId: string, reason?: string): boolean;
  mute(playerId: string, minutes: number | null, reason?: string): boolean;
  unmute(playerId: string): boolean;
}

export type Weather = 'clear' | 'rain' | 'storm' | 'fog' | 'snow' | 'blizzard' | 'sandstorm' | 'smog';
export type Block = 'air' | 'grass' | 'dirt' | 'stone' | 'earth' | 'wood' | 'ironVein' | 'copperVein' | 'silverVein' | 'adamantVein' | 'water' | 'ice';

export interface PluginWorlds {
  open(): string[];
  players(world: string): PluginPlayer[];
  setBlock(world: string, cell: PluginCell, block: Block): boolean;
  spawnMob(world: string, mob: string, at: { x: number; z: number }, count?: number): boolean;
  weather(world: string, weather: Weather | null, minutes?: number): boolean;
  setHour(hour: number): void;
}

export interface PluginWindows {
  show(playerId: string, window: PluginWindow): Promise<PluginWindowAnswer | null>;
  close(playerId: string): void;
}

export interface PluginWindow {
  title: string;
  text?: string;
  items?: { item: string; count?: number }[];
  fields?: PluginField[];
  buttons?: { id: string; label?: string; style?: 'primary' | 'secondary' | 'danger' }[];
}

export type PluginField =
  | { kind: 'text'; id: string; label?: string; value?: string; placeholder?: string; max?: number }
  | { kind: 'number'; id: string; label?: string; value?: number; min?: number; max?: number }
  | { kind: 'choice'; id: string; label?: string; options: { id: string; label?: string }[]; value?: string }
  | { kind: 'toggle'; id: string; label?: string; value?: boolean };

export interface PluginWindowAnswer {
  button: string;
  values: Record<string, string | number | boolean>;
}

export interface PluginStore {
  get<T = unknown>(key: string): T | undefined;
  set(key: string, value: unknown): void;
  delete(key: string): boolean;
  keys(): string[];
}
```

See [[Plugin API]] for what each part does.
