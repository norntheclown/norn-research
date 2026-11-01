> Historical build note from the Norn the Clown project, republished here as a research record. The full project repository is private; this research repo holds the reusable pieces.

# Phase 2 build notes: the sim, needs, flavor, living world

Pushed 2026-10-31 in one commit on main, "Phase 2: the sim, needs, flavor, living world" (6 files). Note: the push tool timed out on the call but the commit landed; all 6 files were verified on main afterward.

## What was built
- `world/src/sim.js` (new): pure simulation functions with no I/O, imported by both the worker and the harness. Blood strain curve, need drains with cross-need pressure and body-temperature effects, urges with hysteresis plus interrupt cooldowns plus adaptation, feel/intensity decay, affect events, hidden flavor system (tastes, cravings, fatigue, comfort food), garden growth/wilt/weeds, cat trust, wildlife and mystery scheduling, daily surprise picker, public visibility filter.
- `world/src/worker.js` (rewritten): the Durable Object now owns all sim state. Eight needs drain on the 10s tick (hunger, energy, fun, social, creativity, recognition visible; blood hidden; desire private). Blood strain drags mood, energy drain, and food satisfaction. Food spoils. Body temperature drifts with real Jacksonville weather. The garden grows on real time. The cat's trust decays without contact. One or two daily surprises spawn per ET day. The payphone and full-moon circus music run on their own schedule. `act()` applies numeric need effects, supports sleeping until rested, vending, and eating from inventory (`inventory` + `eat` action); the first red drink discovers the blood need. A placeholder survival policy eats when hungry and sleeps when tired so the live town stays believable until the Phase 3 brain replaces it. Learning hooks record action-to-need effects for Phase 3.
- `world/public/main.js`: HUD now shows visible need bars; the blood bar appears only after discovery.
- `world/sim_harness.mjs` (new): sped-up simulation, 30 days at 1-hour steps with scripted visitors (kind on day 5, absent days 10-12, rude on day 15). Checks: no need under 10 for 48h+, every need rises and falls, blood discovered and publicly visible afterward, desire never public, garden produces, cat trust grows, no stuck action loops, a red drink gets bought.
- `world/sim_test.mjs` (new): 14 unit tests for sim.js.
- `world/smoke_test.mjs`: updated to stub the new content imports.

## Test results
- 14 sim unit tests passed.
- 14 worker smoke checks passed.
- 43 pytest passed.
- Simulated month passed: no stuck loops, no multi-day crises, needs rise and fall. Notable: the free protein bar alone cannot cover hunger's drain (by design, "barely satisfies"); the harness actor must farm radishes to stay fed, which is the intended pressure.

## Deploy notes
- Workers Builds deployed main automatically. Verified live: /api/state shows the six visible needs ticking, blood hidden until discovery, desire never public, garden weedy, cat at 0 trust.

## Open items
- Phase 3 (the brain: identity, memory, personality, decisions) replaces the placeholder survival policy.
- Barricade 1 unlock still undecided (see Phase 1 notes).
- Private desire/intimacy module is Phase 3+ per the roadmap, in a separate private repo.
