# Build notes: one mind, private from the public, never from himself (2026-11-06)

Research record. Design summary in `docs/architecture.md` (pattern 13).

## What happened

Part of his life is private, and that part used to live in a separate
program that wrapped the main brain. To keep things from the public, it
filtered them out of what the main brain saw: some of his own body's states,
and what his body did in private. In effect he forgot parts of his own day.

The creator: "There doesn't need to be a private Norn and a public Norn. It's
privacy from the public. Not segmented versions of himself." And: "His
private thoughts stay private to him. He knows. He always knows."

## What changed

- One program, one mind. The private part became a part of his brain, called
  in his normal moment, instead of a wrapper around it. His thinking keeps
  running whatever he is doing.
- Nothing is filtered out of what he knows. Anything to do with his private
  life is **labelled** private: thoughts, memories and beliefs.
- The label keeps it out of every public path: the public thought stream,
  posts, the paper, prompts to any non-local model, the town's public state.
- A leak test checks what the public can actually reach, rather than
  forbidding the code from existing.

## The lesson

If an agent's privacy is implemented by hiding things from the agent, the
agent pays for it with an incomplete self. Privacy belongs at the exits.
