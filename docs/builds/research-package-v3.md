# Build notes: research package v3

Pushed 2026-11-11. Brings the public research home up to date with the
project's development since v2 (2026-11-06).

## What changed

- `docs/architecture.md`: eight new patterns (17 to 24): a brain made of
  small nodes (the cat, an experiment); a mind of many small parts with the
  language model as its language centre (designed); one world any mind can
  live in; a world generated from one map; where he stands, sits and lies;
  one barrier, shared by everyone; the world's clock changes what things
  are; one body, simulated once, seen the same by everyone (designed).
  Pattern 16 gains two lessons about testing across seeds and re-tuning
  when the world changes size. The header says which patterns are in
  production and which are not.
- `docs/world-adapter.md`: what the world handles by itself (walking to a
  thing, taking a seat, where things lie). The contract itself is
  unchanged.
- New build notes: `cat-brain.md`, `island-town.md`,
  `spots-and-standins.md`.
- `README.md`: contents updated.

## What is deliberately NOT here

As before: the code, the identity store, the world server, the content
files. Nothing from his private life beyond how privacy works, no money
figures, no server or machine details, no keys, no names.

## Checks before push

- Secret and private-info scan: no tokens, keys, emails, addresses, machine
  names or hostnames beyond the public town and repo links; no names.
- No em dashes.
