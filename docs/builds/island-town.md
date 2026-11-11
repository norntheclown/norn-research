# Build notes: the island town (2026-11-10 to 2026-11-11)

Research record. Design summary in `docs/architecture.md` (patterns 20, 22
and 23).

## What changed

The town moved from a small square to an island town, shaped with the
creator over a series of map drafts: Old Florida, river-town in feel. A
marsh runs west to the sunset, the ocean is east with a lighthouse, an old
ferry used to cross to the north shore, and to the south a broken bridge
points across a wide channel at a circus on the far shore, lit at dusk.
Thirty-three buildings in districts: the old town, the park (quiet, far
from the lights), the artsy streets, the promenade at the centre with town
hall across from it, Main Street, and the lively fun end. A few houses
stand empty, so that later, given more than one mind, it will be
interesting to see which one each picks.

The creator's direction for the buildings: they are functionality, not
story. For the character, a place fills his body's needs or gives his mind
and creativity something to do; for visitors, it is somewhere to explore
and interact. Visitors never control him.

## How it is built

- **One map file** (pattern 20): the plan holds the shore, marsh, rivers,
  land across the water, streets, paths, places and trees. A build step
  writes the layout the server and client share, including a walking graph
  checked to reach everything. A map page is drawn from the same file.
- **His home:** his tent (his cot, desk and things) and, beside it, his
  caravan, his private space, on a patch at the park's quiet west edge.
- **Migration:** the live character moved worlds. The layout carries a
  version; on a new version the live state is migrated once, and on every
  load anything left somewhere that no longer exists is moved to the
  nearest place he can stand.
- **The barrier** (pattern 22): nobody walks off the island; one rule file,
  identical on client and server, tested on every step of every path.
- **The fountain** (pattern 23): water by day, blood after dark. Blood is no
  longer sold anywhere; he drinks it from the fountain at night.

## What the tests caught

- **Comfort on a bigger island:** sore and far from home, he sat on the
  nearest cold bench rather than walking a few minutes to his cot, and
  spent too long uncomfortable. The walk now weighs less when choosing a
  seat, and he walks home.
- **Hunger at a distance:** after a later change he got up inside his tent,
  a few metres further from everything; with food a few minutes' walk away,
  a hungry afternoon sometimes scored the trip as not worth it. A lower
  walking cost fixed it; the month passes on five seeds.
- **The ground drew over everything:** the flat ground layers were pushed
  forward in the depth buffer so hard that grass covered the bottom of
  every building and object, and small things vanished from above. Ground
  layers now draw after solid things, in height order, without writing
  depth.

## Open

- Building out what is inside each building (functionality first, the
  first two or three he would use).
- Real models in place of the blockout.
