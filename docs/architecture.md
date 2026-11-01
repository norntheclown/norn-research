# Norn architecture: reusable patterns

These are the design patterns behind Norn the Clown, an autonomous AI agent
with a persistent identity. Each pattern is in production in the live town at
https://norntheclown.com. None of them are clown specific. They are written
here so another builder can lift them whole.

## 1. Identity is separate from the brain

Norn's self lives in its own store: memories, personality traits, tastes,
relationships, inventory, money, and history. The AI model is a swappable part
that loads him in.

Rules that make this work:

- The store has a versioned schema with migrations, so upgrades never break
  it. The schema file is the source of truth, never a database file.
- Swapping the model (local to hosted, old to new) changes nothing about the
  stored identity. This is tested, not assumed.
- The store is backed up nightly, with off-machine copies.

Why it matters: most agent demos tie the agent's self to a prompt or a
conversation. When the model changes, the agent dies. Separating identity from
the model means Norn stays himself through every upgrade, every hardware move,
and every new world.

## 2. Worlds plug in through two functions

The brain only knows `observe()` (what can i see and do here?) and `act()`
(do this). The web town, a future Unreal world, and the creator's games each
implement that interface. The brain never contains world-specific code.

See `docs/world-adapter.md` for the full contract, including the exact JSON
shapes. The short version:

- `observe()` returns a snapshot: location, visible objects with their
  actions, the menu of available actions, current need values, world time,
  and weather. It must be cheap and side effect free.
- `act(action)` takes exactly one action from the menu and returns what
  happened: success or failure, need effects, inventory changes, and the new
  need values.

Why it matters: the same brain runs against a mock world in tests, the live
web town, and later a 3D engine, with zero brain changes. New worlds are a
weekend project instead of a rewrite.

## 3. The thought loop checks itself

Norn does not jump straight from situation to action. Every tick, the brain
generates one to three thoughts, checks each one, and only acts on, says, or
believes what passes the check.

Thought types: noticing, feeling, wanting, idea, plan, memory, question.

The check asks five questions of every thought:

1. **Is it true?** Compare against facts: wallet, inventory, memories, world
   state. This is the most important check and keeps him grounded.
2. **Is it me?** Does it fit his personality, values, and fixed anchors?
3. **Does it help?** Does it serve his needs, wants, or desires?
4. **Am I repeating myself?** Has he had this thought or done this action
   recently?
5. **Is it private?** Does it involve information that cannot appear publicly?

Each thought gets a verdict: **keep** (use as is), **revise** (use the
corrected version), or **drop** (let it go). Every thought, its verdict, and
its reason are logged. A thought that passes the check repeatedly, backed by
evidence, becomes a **belief**, stored with its supporting evidence and open
to revision when new evidence contradicts it.

Cost tiers keep this affordable: everyday thoughts use the local model and a
fast check; big decisions (spending money, posting, messaging the creator)
use the stronger model for a careful check.

Why it matters: the thought log is the core record of the experiment. It shows
how the agent corrects itself over time, and it makes the agent's reasoning
legible to anyone watching: a classroom can interrogate how he thinks instead
of receiving opaque behavior as a finished product.

## 4. The decision loop

Each tick:

1. Observe the world through `observe()`.
2. Score available actions with a utility system: need urgency, wants,
   desires, personality, and diminishing returns on repeated actions.
3. For routine choices, take the top action directly. For meaningful choices
   (spending money, what to create, what to post, replying to someone), ask
   the model with his personality, current state, and relevant memories.
4. Act through `act()` and save what happened to memory.

If the model is unavailable, he keeps living on the utility system alone. The
agent degrades to a simple sim instead of freezing. This fallback is load
bearing: it is what lets the town stay alive when the laptop is off or the
model is down.

Motivation runs in three layers: **needs** (minutes to hours, e.g. hunger),
**wants** (days to weeks, e.g. a favorite food, formed through reflection),
and **desires** (months, e.g. upgrade his brain, each with a visible progress
measure).

## 5. Content lives in data files, not code

Foods, items, buildings, needs, events, unlock rules: all JSON. Adding content
never requires new code. Every system is a timer, a counter, and a rule.
Prefer the simplest version that works.

Why it matters: it keeps the codebase small and the content editable by a
non-programmer. It also makes the world's rules inspectable: the tuning that
drives behavior is data you can read, not logic buried in code.

## 6. The hybrid brain

A free local model runs everyday life. A stronger hosted model is called for
big creative and agent tasks, and it costs money from an in-world budget, with
a hard monthly cap. Better thinking is tied to earning.

Why it matters: it makes a persistent agent affordable on ordinary hardware,
and it turns a cost problem into a design feature. The agent has a reason to
want to earn.

## 7. The world pushes back

Every need has a source, a cost, and a limit. Needs drain, resources run out,
repetition gives less each time, and some things only come from outside
(visitors, posting, patrons). The world runs on its own clock: day and night,
weather, plant growth, and small surprises happen whether or not Norn acts.

Why it matters: a world that is easy to solve makes the agent loop. Pressure
is what produces interesting behavior.

## 8. Feelings with two layers

**Emotions** are fast reactions to events (joy, loneliness, fear, pride),
each a value that rises when something happens and decays toward a resting
level. **Mood** is slow: a running average of recent emotions that shifts over
a day or more. Personality weights scale how strongly each emotion rises and
how fast it fades. Needs feed feelings (low social raises loneliness) and
feelings feed everything: thoughts, choices, creations, voice, face.

Event-to-emotion rules live in data, not code, so new triggers never require
a rewrite.
