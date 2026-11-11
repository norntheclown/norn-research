# World adapter interface

The brain never contains world-specific code. Every world (the web town now,
a future Unreal world, the creator's games later) plugs into the brain through
exactly two functions: `observe()` and `act()`. This document is the contract.

## observe()

Returns a snapshot of everything the brain is allowed to know right now.

```json
{
  "location": "town_square",
  "visible_objects": [
    {"id": "fountain", "name": "dry fountain", "state": "dry", "actions": ["inspect", "fix"]},
    {"id": "tent", "name": "circus tent", "state": "pitched", "actions": ["enter", "sleep"]}
  ],
  "available_actions": [
    {
      "id": "eat_protein_bar",
      "label": "eat a protein bar",
      "satisfies": {"hunger": 15},
      "time_minutes": 5,
      "requires": ["protein_bar"],
      "needs_llm": false
    }
  ],
  "needs": {"hunger": 62.0, "energy": 71.0, "fun": 48.0, "social": 18.0, "creativity": 55.0, "recognition": 30.0},
  "time": "2026-10-29T22:30:00-04:00",
  "weather": {"condition": "clear", "temp_c": 21.5, "note": "clear night over the square"}
}
```

Field notes:

- `location`: string id of where Norn currently is.
- `visible_objects`: objects Norn can see from here. Each lists the action ids
  that make sense on it. The world decides what is visible, not the brain.
- `available_actions`: the full menu of actions the brain may choose from this
  tick. Each action has:
  - `id` (required, unique string), `label` (required, human readable)
  - `satisfies` (optional): map of need id to need points this action grants
  - `time_minutes` (optional, default 0): simulated minutes the action takes
  - `requires` (optional): item ids that must be in inventory
  - `needs_llm` (optional, default false): true when the choice is meaningful
    (spending money, creating, posting, replying) and deserves model input
- `needs`: current need values, 0 to 100. Need ids come from content/needs.json.
- `time`: ISO 8601 timestamp in the world's clock (America/New_York for the town).
- `weather`: at least `condition` (clear, cloudy, rain, storm, snow, fog).

## act(action)

Takes exactly one action and returns its result. `action` is one action dict
copied from `available_actions` (it must include at least `id`). The world may
ignore extra keys the brain adds, such as `reason`.

```json
{
  "ok": true,
  "action_id": "eat_protein_bar",
  "result": "norn eats the protein bar. it tastes like cardboard with ambition.",
  "effects": {"hunger": 15},
  "time_advanced_minutes": 5,
  "needs": {"hunger": 77.0, "energy": 70.5, "fun": 47.8, "social": 17.9, "creativity": 54.9, "recognition": 30.0}
}
```

Field notes:

- `ok`: false when the action was invalid, impossible, or failed. `result`
  always explains what happened, in lowercase (Norn's world speaks his voice).
- `effects`: need deltas actually applied, for the log.
- `grants`: items added to Norn's inventory by this action, each with
  `item_id`, `name`, and `qty`. The brain records them in the identity store.
- `consumes`: item ids removed from inventory by this action.
- `needs`: need values after the action, so the brain can snapshot them into
  the identity store without a second observe() call.

## Rules for world implementers

1. The brain only ever calls `observe()` and `act()`. Worlds must not require
   any other entry point.
2. `observe()` must be cheap and side effect free. Acting happens only in `act()`.
3. Never invent need ids: they come from content/needs.json. Never invent
   content: foods, items, buildings, events, and unlock rules live in data
   files under content/.
4. Every system is a timer, a counter, and a rule. Keep the world pushing back:
   needs drain, resources run out, repetition gives less, some things only come
   from outside.
5. Action ids and object ids are stable strings. The identity store logs them;
   renaming one breaks history, so treat ids as permanent.

## Versioning

## What the world handles by itself (2026-11-11)

The brain names a thing and an action; it never deals with positions. The
world walks him there (to the thing's standing spot), takes his place on a
seat or a bed for the action, and steps him back off it afterwards. Things
he sets down stay where he put them and appear in `visible_objects` like
anything else; putting down, picking up and throwing away are actions like
any other. The world also keeps everyone on walkable ground. See
`docs/architecture.md`, patterns 19 to 22.

This contract is version 1. If it ever needs to change, add a new section here
and keep old fields working. Worlds advertise the contract version they
implement; brain.tick checks it on startup and refuses to run on mismatch.
