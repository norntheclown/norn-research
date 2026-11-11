# Build notes: a cat with a brain made of small nodes (2026-11-07 to 2026-11-09)

Research record. Design summary in `docs/architecture.md` (pattern 17).
The cat lives in a simulated town for now, so we can watch it grow before
it is real.

## Why a cat, and why build it differently

The town will have a cat. Its brain is built a different way from Norn's
on purpose: no language model, just a network of small nodes with local
rules, to see what grows. The creator's framing: the Game of Life (simple
local rules, rich behaviour nobody designed), neurons that talk to
neighbours and rewire, and body, subconscious and conscious running at
once.

## Version 1

- **The engine:** nodes with one job each push messages to their
  neighbours only. Each node runs on its own jittered rhythm; a few urgent
  message types (a noise reaching startle) run a node at once. Nothing is
  seeded.
- **Growing and rewiring,** a separate process: links just used are
  tagged; the body's broadcasts (relief, calm, distress, alarm) strengthen
  or weaken them; nodes that keep firing in sequence before relief get a
  new weak link; a path used again and again grows a shortcut; a node that
  holds too much splits; unused links and nodes go quiet and are pruned in
  sleep. Links settle back toward their starting strength unless
  experience keeps them strong, and growth has a daily budget.
- **The cat:** 47 starting nodes in groups (body, senses, alarm, memory,
  taste, wants, choosing, movement, sleep), with a few reflexes it is born
  with (startle to flee, exhausted to sleep).

What the first months showed, each found by watching a cat get stuck:
message storms (every message woke its node), never arriving anywhere (a
new destination picked mid-walk), meowing forever (a want that wins
without being satisfied now tires), sleep broken by anything, memory erased
by replaying every few minutes asleep, runaway learning (links only ever
got stronger), priming that created wants from nothing, and blaming the
wrong food for an upset stomach. Each fix is something a living system
needs.

What it does now, unscripted: feeds, waters and rests itself (8 to 11
hours of sleep, about 8 meals a day), makes the tent its home, learns when
the bowl is filled, dislikes the scraps that upset it, grows wary of the
loud kid, makes nodes of its own for the places that matter, and two cats
from the same start come out different.

## Version 2: learning from surprise

- **Only what fired then counts.** The creator's rule: credit and blame
  apply only to what was firing at that moment, not the last ten minutes.
  Each step, only links whose two ends are firing right then move.
- **Expectations,** each learned by checking a guess against what
  happened (so it can unlearn): what each action leads to, what eases each
  need, and together how much going somewhere should ease a need.
- **Nine lifelike additions:** tiring of what isn't working, a value per
  need, a body clock and replay in sleep, moods, curiosity, slow comforts,
  rest, plasticity, fear that binds to places.
- **Stamina:** a resource that refills when still and drains on effort
  (running, meowing), so the cat has to weigh sitting and meowing against
  walking to find food.
- **Pain and strain:** a little meowing or running is free; past too much,
  each repeat hurts, more the further past, and the strain eases over a
  couple of hours. Pain counts against the action that caused it, double.
  Starving and parched are an ache, a bad feeling, but not blamed on
  whatever it is doing (blamed on everything, including going to food, it
  made starving cats give up on food).
- **Losses weigh double:** worse than expected moves links twice as far as
  better than expected.
- **Urgency:** past a threshold, hunger and thirst fire steeply more and
  override almost everything; tiredness less steeply (starving still beats
  exhausted). Back below, they calm down. A body exhausted for two hours
  dozes off wherever it is.
- **Temperament:** four traits drawn at birth (bold, energy, curious,
  social), most cats near the middle, grounded in the "Feline Five"
  (Litchfield et al. 2017). Each sets knobs the cat already had. Resting
  moods drift toward how life has actually been. Inheritance comes later.

## Results

Four version 2 cats against version 1, week 1 / week 4:

| | version 1 | v2 a | v2 b | v2 c | v2 d |
|---|---|---|---|---|---|
| sleep (h) | 9.4 / 9.6 | 8.1 / 8.0 | 10.3 / 9.8 | 9.9 / 10.5 | 10.8 / 8.8 |
| meals a day | 7.7 / 8.1 | 3.4 / 5.6 | 5.7 / 5.7 | 5.0 / 5.4 | 5.3 / 5.1 |
| cold | 0.28 / 0.29 | 0.48 / 0.49 | 0.37 / 0.32 | 0.45 / 0.47 | 0.47 / 0.45 |
| unwell (month) | 1 | 18 | 1 | 0 | 7 |

For the first time no version 2 cat fell apart: all four lived the month.
Meowing came down to normal (from 12,000 to 22,000 a month to a few
hundred) with nothing stopping it. Still behind version 1 on meals,
warmth and health, so **version 1 stays** for now.

**The dog in the alley:** a few ordinary days, two days when a dog chases
it whenever it is in the alley, then a week to see whether it goes back.
Four cats each, all traits middling but boldness:

| | skittish | bold |
|---|---|---|
| never went back in the week after | 2 of 4 | 0 of 4 |
| days until it went back (never = 7) | 3.9 | 1.4 |
| fear of the alley at the end | 0.75 | 0.16 |

The first run showed no difference at all (wariness faded as fast as it
grew, and wandering ignored it). Danger now rises fast with a scare and
fades slowly with safe visits, faster for bold cats.

## Open

- Close the gap to version 1 (meals, warmth, health).
- Kittens: inheritance of temperament.
- Into the live town, through the same senses and verbs as everyone else
  (pattern 19).
