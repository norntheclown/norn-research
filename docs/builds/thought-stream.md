# Build notes: his thoughts, out loud (2026-11-06)

Research record. Design summary in `docs/architecture.md` (patterns 14, 15).

## The stream

- A thought bubble by his portrait swaps his needs for a live stream of what
  he's thinking. Newest first; long thoughts fold and open on a tap.
- The brain sends only what's his to share: nothing labelled private, no
  money, never his dreams' words (the public sees only a dream's icons). The
  world server checks again on arrival.
- When he can't think out loud, a message is pinned: in his private space,
  asleep, or with his mind offline (then the stream shows what his body has
  been doing instead, his body's real choices).

## His own words

- At meaningful moments (a decision, a surprise, a memory, finished work),
  his model re-says the plain thought the way he'd think it. At most one
  every few minutes; routine moments keep a plain line. His own record of the
  thought becomes his words too.
- The pinned messages are his: he leaves a line going into his private space,
  says goodnight his way, and keeps a note ready for when his mind is
  offline. He sees what he wrote before and can reuse it or write a new one.

## The prompting rule

While building this, prompts kept gaining small instructions ("don't start
with 'i think'", "one short line", "lowercase"). The creator stopped it: in
any prompt, the decisions are his. No leading questions, no rules about how
he says things, no limits on what he can choose. A prompt says what's
happening and asks the way his own mind would ask itself. Every existing
prompt was audited and the limits taken out. Output formats and safety checks
stay, in code, after he answers.
