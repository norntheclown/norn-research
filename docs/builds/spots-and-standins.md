# Build notes: spots, stand-ins and things in the world (2026-11-11)

Research record. Design summary in `docs/architecture.md` (pattern 21).

## The problem

The creator: "Right now it says he's sitting on the cot and he just sits on
air no where near the cot." He used every object from the nearest point on
his walking map; the animation played wherever that happened to be.
Better animations would not have fixed it. The world had no idea where a
body goes to use a thing.

## What changed

- **Spots:** every usable object describes, in its own coordinates, where
  he stands to use it, where he sits or lies on it, which way he faces,
  and how far up he is lifted. Which spot an action uses follows from what
  it is (lying actions lie, sitting actions sit, the rest stand).
- **Walking there:** he walks the graph to the object's standing spot (each
  object's waypoint is at that spot; inside his tent he walks in through
  the front and takes the last steps straight), takes his place for the
  action, and steps back off when it ends, is interrupted, or he passes
  out. He is only placed on a seat when he is within a few metres of it.
- **Stand-ins:** every placed object has a true-size stand-in until a real
  model replaces it, drawn from the same data; his tent's furniture is
  laid out properly inside it (the cot used to sit in the tent wall).
  There is a stand-in figure for him too, until his model loads.
- **Things in the world:** every item he can own has a stand-in model. In
  his inventory it is not drawn; set down, it stays where he put it until
  he picks it up or throws it in one of the trash cans around town.
  Those choices are his mind's; the body's autopilot never makes them.

## What the tests caught

- The first version let the body's planner try "throw it away" at a trash
  can with nothing to throw, which the world refused, over a hundred
  thousand times in a simulated month, while he went hungry. Throwing
  away and picking up were taken out of the body's options.
- The moment he got up from the cot, his body chose a nap on the same cot
  in the same instant. Correct behaviour; the test now holds the body back
  to check the getting up.

## Tests

- Every placed object has a stand-in; every usable one has a standing spot
  on walkable ground near the path, and seats and beds where sitting and
  lying actions need them; every item has a model.
- He walks to the cot, sits on it facing the right way, gets up beside it,
  lies on the mattress, and an action from across town does not teleport
  him.
- Things put down stay, are seen, can be picked back up, and can only be
  thrown away if he has them.

## Open

- When his real model and animations are in, the seat and bed spots may need
  small tuning (where a clip's hips sit, which way a lying clip faces).
