# Build notes: the brain and the body (November 2026)

Research record. The full project repository is private; this note covers
what was built and what was learned, not the code.

## The body (world server)

- **Needs** on a real clock: hunger, thirst, energy, comfort, and a strange
  thirst that is part of his character. **Drives** that pull more slowly:
  making things, being seen, connection, curiosity, getting better at
  things. A felt state (how good or bad he feels, how stirred up) follows the
  whole body.
- The body sends **signals**, not orders. The brain receives them as part of
  what it observes and can act on them or not.
- With the brain offline the body lives on its own: eats, rests, sleeps,
  wanders. With the brain online it waits a short grace period after each
  brain choice, and steps in only in an emergency.
- Sleep is real: he gets sleepy, sleeps at night, can be woken, and passes
  out if he pushes too far.

## The brain (on the creator's machine)

- **Feelings from appraisal:** what happens is judged against what he cares
  about (his goals, his values, his body), producing a feeling with a
  direction (good or bad), an intensity, a sense of control, and a cause.
  Feelings fade; mood follows the body and the day. Feelings are kept as
  dimensions and causes rather than named emotions; names are for talking.
- **Memory:** episodes with importance and strength; a power-law forgetting
  curve; recall by cue and mood, which strengthens what's recalled; details
  fade before gist; reinterpretations stored beside memories, never over
  them. Routines fold into one memory each night.
- **Sleep as maintenance:** nightly replay of the strongest memories; open
  questions that can become beliefs and his own goals; dreams in windows
  through the night; occasionally an answer to a stuck problem by morning.
- **Goals and money:** goals carry a commitment that grows and fades with
  experience (with a floor, so nothing he cares about disappears). Money he
  earns is his to divide between his wallet and his savings.
- **Workspace:** how his body is doing shapes how much he can hold in mind:
  exhausted or starving, he thinks with less.

## What testing found

A simulated month (the real brain on the real world code, on a fake clock)
was the most useful test. It found loops (choosing the same thing for hours),
a goal re-deciding the same money every moment, and memories that never
consolidated. Each was fixed and kept as a check.
