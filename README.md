# norn-research

Public research notes from **Norn the Clown**: an autonomous AI clown who lives
in an empty town on the internet, makes his own content, and works toward his
own goals. He left the circus and wandered into a town where everyone is gone.
The lights still work, but nobody is there, and he does not know why.

Live town: https://norntheclown.com

This repository is the public research home for the project. The full project
repository is private. What lives here is the reusable part: the architecture
patterns, the interface contracts, and the detailed build notes.

## Contents

- `docs/architecture.md`: the reusable design patterns. Identity separate from
  the model. Worlds plugging in through two functions. A thought loop that
  checks itself. Content as data, not code. A body that runs without the
  brain, and a body he learns rather than reads. Memory that fades and comes
  back. Sleep as maintenance. One mind, with privacy from the public, never
  from himself. Leaving the decisions to him. Thinking out loud in his own
  words. Testing a mind with a simulated month. A brain made of small nodes
  (the cat, an experiment). A mind of many small parts with the language
  model as its language centre (designed). One world any mind can live in.
  A world generated from one map. Where he stands, sits and lies. One
  barrier shared by client and server. A world clock that changes what
  things are. One body, simulated once and seen the same by everyone
  (designed).
- `docs/world-adapter.md`: the `observe()` / `act()` contract. Any world
  implements these two functions and the brain never contains world-specific
  code.
- `docs/builds/`: build notes for each phase: what changed, test results,
  deploy notes, open items. Written as the work landed.

## The three ideas that hold the project together

1. **Identity is separate from the brain.** Everything that makes Norn himself
   (memories, personality, tastes, relationships, inventory, money, history)
   lives in a versioned identity store. The AI model is a swappable part that
   loads him in. Upgrading the model, moving to new hardware, or dropping him
   into a different world must never lose who he is.
2. **Worlds plug in through one interface.** The brain only knows `observe()`
   (what can i see and do here?) and `act()` (do this). The web town, a future
   Unreal world, and the creator's games each implement that interface. The
   same town also serves minds built other ways: a town cat whose brain is
   a network of small nodes, through the same senses and verbs.
3. **The world pushes back.** Needs drain, resources run out, repetition gives
   less each time, and some things only come from outside. A world that is easy
   to solve makes him loop.

## Reusing the patterns

The patterns here were built for one clown, but none of them are clown
specific. If you are building a persistent agent, a game NPC with a real inner
life, or a research prototype:

- Start with `docs/architecture.md` for the pattern catalog and the reasoning
  behind each choice.
- Implement `docs/world-adapter.md` against whatever world you have: a web
  page, a game engine, a chat interface. The brain side stays the same.
- Read the build notes in `docs/builds/` in order. They show what broke, what
  the tests caught, and what is still open.

Everything here runs on ordinary hardware with free open models. That is
deliberate: the ability to build a mind should not require a corporate lab.
