> Historical build note from the Norn the Clown project, republished here as a research record. The full project repository is private; this research repo holds the reusable pieces.

# Phase 0 build notes: foundation and repo setup

Pushed 2026-10-30. Commit `002ad1b582412074ce3494e7e5b4ec5f2407dae1` on main (37 files).

## What was built
- `brain/`: the brain scaffold. `tick.py` (20s tick loop), `decide.py` (decision entry point), `llm/` with three backends: Ollama (local), hosted model, and a deterministic fallback so the brain always answers even with no model reachable.
- `identity/`: SQLite identity database, 25 tables per the V1 roadmap, with migrations and a backup routine.
- `content/`: 9 V1 content JSONs (needs, foods, genres, buildings, items, events, plants, emotion rules, unlocks).
- `world/`: three.js town skeleton, Cloudflare Worker stub, `mock_world.py` for local testing without deploys.
- `shared/world-adapter.md`: the contract between brain and world.
- `config/`: no secrets, everything via env vars. Monthly hosted-model cap $50. Content caps: romance_heat 2, horror_gore 2.

## Test results
- 28/28 pytest passed.
- Mock world tick ran end to end on the build machine via the fallback path (Ollama unreachable from the build machine, by design).

## Deploy notes
- No live deploy in Phase 0. The Worker stub was not wired to a route.

## Open items carried to Phase 1
- Real town layout from the blueprint (layout.json did not exist yet).
- World server rewrite for Cloudflare (rules/timers live on the Worker per V2).
- V2 content drop (11 JSONs) arrived after this push and superseded 4 of the V1 files; the superseded files were archived to `archive/phase0/` in Phase 1.
