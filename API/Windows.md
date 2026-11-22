---
tags: [api, plugins, reference]
---

# Windows

A plugin asks a player something with a window: described as data, drawn by the game in its own look (its sign, its parchment, its buttons), on desktop and on touch. No code of the plugin goes to the player's game.

```js
const answer = await game.windows.show(player.id, {
  title: 'Daily reward',
  text: 'Pick what to take today.\nIt goes straight into your bag.',
  items: [{ item: 'oak_log', count: 5 }],
  fields: [
    { kind: 'text', id: 'note', label: 'A word for the board', placeholder: 'Optional', max: 40 },
    { kind: 'number', id: 'n', label: 'How many', min: 1, max: 5, value: 2 },
    { kind: 'choice', id: 'wood', label: 'Wood', options: [{ id: 'oak', label: 'Oak' }, { id: 'pine', label: 'Pine' }] },
    { kind: 'toggle', id: 'again', label: 'Remind me tomorrow' },
  ],
  buttons: [
    { id: 'take', label: 'Take' },
    { id: 'later', label: 'Later', style: 'secondary' },
  ],
});

if (answer?.button === 'take') {
  game.players.give(player.character.id, `${answer.values.wood}_log`, Number(answer.values.n));
}
```

| | |
|---|---|
| `show(playerId, window)` | Shows the window; resolves with the answer, `{ button, values }`, or `null`. |
| `close(playerId)` | Puts away every window this plugin shows that player (each resolves `null`). |

## The answer

- `button` is the id of the button pressed; `values` holds every field's value by its id, always **within the field's rules** (a number between its `min` and `max`, a choice among its options, a text no longer than its `max`), whatever the player's game sent.
- `null` when the player closes the window, opens another window of the game in its place, leaves the server, or the plugin puts it away. A player who isn't on gets `null` at once.

## What a window holds

| Field | | Rules |
|---|---|---|
| `title` | required | Up to 60 letters, on the window's sign. |
| `text` | optional | Up to 2000 letters; line breaks kept. |
| `items` | optional | Up to 12 `{ item, count }`: each item's picture, name and count. `count` defaults to 1. |
| `fields` | optional | Up to 12 (below). |
| `buttons` | optional | 1 to 4 `{ id, label, style }`; with none, one OK (`id` `ok`). |

Under the title the window always says which plugin it's from.

**Buttons**: `style` is `primary` (green), `secondary` (brown) or `danger` (red); the first defaults to `primary`, the rest to `secondary`. An empty `label` reads OK. They stand on one line at the window's foot.

**Fields**, each with an `id` (letters, digits, `-` and `_`, up to 40, unique in the window) and a `label` (up to 80):

| `kind` | Options | Its value |
|---|---|---|
| `text` | `value`, `placeholder`, `max` (1 to 300, default 300) | text |
| `number` | `min` (default 0), `max` (default 100), `value` (default `min`): whole numbers | a whole number, set with − and + or typed |
| `choice` | `options`: 1 to 12 `{ id, label }` (the label defaults to the id); `value` (default the first) | an option's id |
| `toggle` | `value` (default `false`) | `true` or `false` |

## When a window doesn't hold

`show` **throws** at once, saying what's wrong (`the window doesn't hold: a window needs a title…`). Catch it, or let it be said in the server's window.

## Several at once

A player sees one window at a time: those a plugin shows while another is up wait their turn, in order, each coming up as the one before is answered. A plugin window comes up over whatever window the player has open. A player has at most 8 waiting from the plugins: past that, the oldest is put away (`null`).
