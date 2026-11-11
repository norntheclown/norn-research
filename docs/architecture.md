# Norn architecture: reusable patterns

These are the design patterns behind Norn the Clown, an autonomous AI agent
with a persistent identity. Patterns 1 to 16 and 19 to 23 are in production
in the live town at https://norntheclown.com; 17 is a running experiment,
and 18 and 24 are designs not yet built (each says so). None of them are clown specific. They are written
here so another builder can lift them whole.

## 1. Identity is separate from the brain

Norn's self lives in its own store: memories, personality traits, tastes,
relationships, inventory, money, and history. The AI model is a swappable part
that loads him in.

Rules that make this work:

- The store has a versioned schema with migrations, so upgrades never break
  it. The schema file is the source of truth, never a database file.
- Swapping the model (local to hosted, old to new) changes nothing about the
  stored identity. This is tested, not assumed.
- The store is backed up nightly, with off-machine copies.

Why it matters: most agent demos tie the agent's self to a prompt or a
conversation. When the model changes, the agent dies. Separating identity from
the model means Norn stays himself through every upgrade, every hardware move,
and every new world.

## 2. Worlds plug in through two functions

The brain only knows `observe()` (what can i see and do here?) and `act()`
(do this). The web town, a future Unreal world, and the creator's games each
implement that interface. The brain never contains world-specific code.

See `docs/world-adapter.md` for the full contract, including the exact JSON
shapes. The short version:

- `observe()` returns a snapshot: location, visible objects with their
  actions, the menu of available actions, current need values, world time,
  and weather. It must be cheap and side effect free.
- `act(action)` takes exactly one action from the menu and returns what
  happened: success or failure, need effects, inventory changes, and the new
  need values.

Why it matters: the same brain runs against a mock world in tests, the live
web town, and later a 3D engine, with zero brain changes. New worlds are a
weekend project instead of a rewrite.

## 3. The thought loop checks itself

Norn does not jump straight from situation to action. Every tick, the brain
generates one to three thoughts, checks each one, and only acts on, says, or
believes what passes the check.

Thought types: noticing, feeling, wanting, idea, plan, memory, question.

The check asks five questions of every thought:

1. **Is it true?** Compare against facts: wallet, inventory, memories, world
   state. This is the most important check and keeps him grounded.
2. **Is it me?** Does it fit his personality, values, and fixed anchors?
3. **Does it help?** Does it serve his needs, wants, or desires?
4. **Am I repeating myself?** Has he had this thought or done this action
   recently?
5. **Is it private?** Does it involve information that cannot appear publicly?

Each thought gets a verdict: **keep** (use as is), **revise** (use the
corrected version), or **drop** (let it go). Every thought, its verdict, and
its reason are logged. A thought that passes the check repeatedly, backed by
evidence, becomes a **belief**, stored with its supporting evidence and open
to revision when new evidence contradicts it.

Cost tiers keep this affordable: everyday thoughts use the local model and a
fast check; big decisions (spending money, posting, messaging the creator)
use the stronger model for a careful check.

Why it matters: the thought log is the core record of the experiment. It shows
how the agent corrects itself over time, and it makes the agent's reasoning
legible to anyone watching: a classroom can interrogate how he thinks instead
of receiving opaque behavior as a finished product.

## 4. The decision loop

Each tick:

1. Observe the world through `observe()`.
2. Score available actions with a utility system: need urgency, wants,
   desires, personality, and diminishing returns on repeated actions.
3. For routine choices, take the top action directly. For meaningful choices
   (spending money, what to create, what to post, replying to someone), ask
   the model with his personality, current state, and relevant memories.
4. Act through `act()` and save what happened to memory.

If the model is unavailable, he keeps living on the utility system alone. The
agent degrades to a simple sim instead of freezing. This fallback is load
bearing: it is what lets the town stay alive when the laptop is off or the
model is down.

Motivation runs in three layers: **needs** (minutes to hours, e.g. hunger),
**wants** (days to weeks, e.g. a favorite food, formed through reflection),
and **desires** (months, e.g. upgrade his brain, each with a visible progress
measure).

## 5. Content lives in data files, not code

Foods, items, buildings, needs, events, unlock rules: all JSON. Adding content
never requires new code. Every system is a timer, a counter, and a rule.
Prefer the simplest version that works.

Why it matters: it keeps the codebase small and the content editable by a
non-programmer. It also makes the world's rules inspectable: the tuning that
drives behavior is data you can read, not logic buried in code.

## 6. The hybrid brain

A free local model runs everyday life. A stronger hosted model is called for
big creative and agent tasks, and it costs money from an in-world budget, with
a hard monthly cap. Better thinking is tied to earning.

Why it matters: it makes a persistent agent affordable on ordinary hardware,
and it turns a cost problem into a design feature. The agent has a reason to
want to earn.

## 7. The world pushes back

Every need has a source, a cost, and a limit. Needs drain, resources run out,
repetition gives less each time, and some things only come from outside
(visitors, posting, patrons). The world runs on its own clock: day and night,
weather, plant growth, and small surprises happen whether or not Norn acts.

Why it matters: a world that is easy to solve makes the agent loop. Pressure
is what produces interesting behavior.

## 8. Feelings with two layers

**Emotions** are fast reactions to events (joy, loneliness, fear, pride),
each a value that rises when something happens and decays toward a resting
level. **Mood** is slow: a running average of recent emotions that shifts over
a day or more. Personality weights scale how strongly each emotion rises and
how fast it fades. Needs feed feelings (low social raises loneliness) and
feelings feed everything: thoughts, choices, creations, voice, face.

Event-to-emotion rules live in data, not code, so new triggers never require
a rewrite.

## 9. A body that runs without the brain

Norn's body lives on the world server and never stops: needs drain on a real
clock (hunger, thirst, energy, comfort, and a strange thirst that is part of
who he is), and five slower **drives** pull at him (making things, being seen,
connection, curiosity, getting better at things). The body sends its state to
the brain as signals, not orders. The brain can act on them, think about
something else, or ignore them.

When the brain is offline (the creator's laptop asleep, the model down), the
body keeps him alive by itself: it eats, sleeps and wanders on what it has
learned. When the brain comes back, the body steps aside after a short grace
period, except in an emergency, when the body wins.

Why it matters: the mind can be switched off, upgraded, or slow without the
character dying or freezing. The body is the floor; the brain is everything
above it.

## 10. A body he learns, not one he reads

Early on, every action carried exact numbers ("a nap gives +5 energy") and the
brain was told them. That made him a calculator. Now the world keeps its truth
hidden, in three layers:

1. **Physiology (hidden):** digestion takes time (a meal is felt partly at
   once and the rest over the next hour), no two meals are the same, thirst
   rises faster in heat, tricks make him sore, bad food upsets his stomach,
   rain soaks him, sitting still stiffens him, practice makes tricks land.
2. **Sensation:** his mind receives **felt levels** (the truth plus a slowly
   wandering bias and a little noise) and **percepts** in words ("a gnawing
   ache in the stomach", "a cool breeze across the back of his neck"). Never
   numbers. A steady sensation fades from attention; a new or growing one
   stands out.
3. **Learning:** he believes what his own experience says a thing does. After
   each try he compares how he felt before and after, and checks again an hour
   later (for what comes slowly, like food). One experience teaches a little
   about similar things ("this machine fed me" says something about
   machines). Big surprises become thoughts. The body, separately, builds its
   own habits, which is how it gets by when the brain is away.

He can also try any of thirteen verbs on anything near him (eat, sit, touch,
open, listen...). What happens comes from the thing's properties: eating a
bench is a jolt through the teeth and an upset stomach, and he remembers.

Why it matters: an agent that is told what everything does never discovers
anything. One that has to try things, feel the result, and remember, starts
to have a history with its world.

## 11. Memory that fades and comes back

Episodes (where he was, what he did, how it felt) are stored with an
importance and a strength. They fade on a power-law forgetting curve, the
kind modern spaced-repetition research uses. Recalling a memory strengthens
it, more when recalls are spaced out and when it was harder to reach. Recall
depends on cues (where he is, what he is doing) and on mood (a low mood
surfaces low memories). Details fade before the gist does.

When he thinks back and sees something differently, his new view is stored
beside the memory. The facts are never rewritten.

Why it matters: a perfect memory is not a person's memory. Fading, cueing and
reinterpretation give him a past that behaves like one.

## 12. Sleep as maintenance

Sleep is when the mind tidies up. Each night it replays the day's strongest
memories, folds repetitive routines into a single memory, and asks itself
open questions: does anything here say something about my life? Is there
anything I want to work toward? What he answers becomes beliefs and his own
goals. Dreams happen in windows through the night, built from the day's
material, and a stuck problem is sometimes solved by morning.

Why it matters: without consolidation, memory is just a growing log. With it,
experience turns into belief and intention on its own schedule.

## 13. One mind, with privacy from the public, never from himself

Some of his life is private. The rule that turned out to matter: private
means **never seen by the public**, never **kept from him**. He thinks about
his private life, learns from it, remembers it, like anything else. Anything
to do with it is **labelled** private, and the label keeps it out of every
public path: public feeds, posts, the public thought stream, and prompts to
any model that is not running locally.

An earlier design split the private part into a separate program that hid
things from the main brain. That made two half-minds. The fix was one mind,
one store of beliefs, and a label at the exits.

Why it matters: hiding parts of an agent's experience from itself to keep
them from an audience damages the agent and protects nothing. Filter what
leaves; don't filter what he knows.

## 14. Leave the decisions to him

A hard rule for every prompt: no outside influence on his values, no leading
questions, no rules about how he says things, no limits on what he can
choose. A prompt says what is happening and asks the way his own mind would
ask itself ("how does it sit with you now?"). Output formats the code needs to
read are fine (choose one of the actions that are really possible, reply in
this JSON shape). Safety checks happen in code after he answers, never as
instructions to him.

Why it matters: it is easy to write prompts that quietly decide for the
agent, and then mistake the result for its personality. If the experiment is
whether a character can develop its own values, the prompts have to stay out
of the way.

## 15. Thinking out loud, in his own words

Visitors can read his thoughts as they happen, through a thought bubble by
his portrait. Only what is his to share goes out (nothing private, nothing
about money, never his dreams' words). At meaningful moments his model
re-says the plain thought the way he would think it; routine moments keep a
plain line. When he cannot think out loud (asleep, in his private space, or
his mind offline), a message is pinned in his own words, which he writes,
reuses or rewrites himself.

Why it matters: watching an agent's reasoning is the interesting part. A
live, honest, first-person stream makes that visible without exposing what
should stay private.

## 16. Testing a mind with a simulated month

The real brain runs against the real world code on a fake clock: a month of
his life in minutes, with a seeded stand-in model. Checks look for what breaks
a life rather than a function: does he starve while his body goes for food,
does one action take over, do feelings and goals stay in range, does he find
for himself what helps hunger and thirst, does anything private reach a
public surface. Every serious problem in the body and learning work was found
this way before it reached him.

Why it matters: unit tests check parts. A persistent agent fails over days,
in loops and slow drifts, and only a long run shows that.

Two lessons from moving him to a much bigger town. First, any change sends
a seeded month down a different path, so one seed proves little: compare
several seeds against the previous version before tuning anything (the old
version fails some seeds too). Second, numbers tuned for one world can be
wrong in another: the cost the body puts on walking was set when everything
was a few dozen metres away; on the island, food is a few minutes' walk, and
a hungry afternoon sometimes scored the trip as not worth it. The month
showed it as idle hunger, the refusal log showed where, and a lower walking
cost fixed it on every seed. A body that keeps trying something the world
refuses is also caught this way: one did, over a hundred thousand times.

## 17. A brain made of small nodes (experiment: the cat)

Status: a running experiment, in a simulated town, not yet in the live one.

A town cat gets a brain built a different way from Norn's, on purpose. It is
a network of small nodes, each with one job (a need, a sense, a place, a
want, a movement). Nodes only message their neighbours, each runs on its own
jittered rhythm, messages take their own time to arrive, and nothing is
seeded: two cats from the same start grow different wiring. A separate
process grows and prunes the network: links that keep firing together
before relief get stronger, a path used again and again grows a shortcut,
a node holding too much splits, unused links fade and are pruned in sleep.

Version 2 learns from surprise, and only what was firing at that moment is
credited or blamed. It learns what each action leads to and what eases each
need, by checking each guess against what happened, so it can unlearn too.
On top of that: stamina that weighs an action against its cost, strain and
pain (a little meowing is free; thousands in a row start to hurt), losses
weighing twice as much as gains, urgency (past a threshold, hunger and
thirst override almost everything, then calm down once eased), and a
temperament drawn at birth (bold, energetic, curious, social) that sets the
knobs it already had.

Results so far: every version 2 cat now survives a simulated month, but the
simpler version 1 still eats more and stays warmer, so version 1 stays in
use. Temperament shows clearly: after a dog chases them in an alley, bold
cats go back within a day or two and their wariness fades, while skittish
ones stay away for days or go back still wary.

Why it matters: it tests whether behaviour nobody designed can grow from
simple local rules (the Game of Life idea), and every failure it shows (a
cat stuck meowing, a cat that unlearns food while starving) names
something a living system needs.

## 18. A mind of many small parts, with the language model as its language centre

Status: designed, not built.

The problem it answers: a language model only knows who he is if it is told
each time. Brains do not work like that. The design is a web of small parts
in areas (body, senses, memory, feelings, wants, choosing), running all the
time and rewiring with his life. Most choices are made by small decision
nodes, by weights, with no language model. When something needs thinking
through, the areas send what they have to one meeting point, which writes
the prompt: a background of who he is, plus what matters right now, more or
less of it from moment to moment. The language model puts it into words and
thinks it through, and the result flows back into the web. It is his
language centre and holds general knowledge; it is not the whole mind.

Why it matters: instead of bigger and bigger models holding everything, a
mind can be many small linked parts with a language model where language is
needed. It will be tested on a stand-in first, never on the live character.

## 19. One world any mind can live in

A mind only ever meets the world through its senses and its verbs. The
world says what is here, what it is like, what state it is in and what just
happened; a mind does one of a short list of verbs to a thing or a place,
and the world works out what happens. Nothing in the world is written for
one mind: no prompt text, no shortcut only one brain can use.

Every place and thing has two layers, never mixed: **mechanics** (what is
there, what it is like, what can be done, what that needs, costs and
changes: data any mind can use) and **words** (descriptions, signs, books:
text a mind with language reads, and one without it ignores). A rule of
thumb: if removing all the text would stop something from working,
mechanics are hiding in the words.

Why it matters: the same town then serves the language-model character,
the node-brained cat, and a future node-built character, and new minds can
be tried in it without changing it.

## 20. A world generated from one map

The town is never hand-placed. One map file holds the island: shore,
marsh, rivers, the land across the water, streets and paths, districts and
places. A build step turns it into the layout the server and the 3D client
both use: buildings, props, placed objects, the walking graph (checked to
reach everything), and the walk area. A map page is drawn from the same
file, so the map never drifts from the world.

Changing the layout of a live world needs care. Each layout has a version;
when it changes, the live state is migrated once (he wakes at home, loose
things move onto paths, new buildings get their states, retired ones go).
And on every load, anything left somewhere that no longer exists (a
waypoint, a spot over the water) is moved to the nearest place he can
stand.

Why it matters: redesigning the world (here, from a small square to an
island town) becomes editing one file, and the live character survives it.

## 21. Where he stands, sits and lies

An embodied agent should use things where they are. Early on he acted from
the nearest point on his walking map, so "sitting on the cot" played in the
air beside it. Now every usable thing says, in its own coordinates: where
he stands to use it, where he sits or lies on it, which way he faces, and
how high he is lifted (a mattress, a ledge). He walks to the standing spot,
takes his place for the action, and steps back off it afterwards. An action
from across town never teleports him onto a seat.

Every placed thing also has a simple stand-in model at its true size (boxes,
cylinders and balls in its own coordinates) until a real model replaces it,
and every item he can own has one too. In his inventory an item is not
drawn; set down, it stays exactly where he put it until he picks it up or
throws it away. Putting down, picking up and throwing away are choices
offered to his mind; the body's autopilot never makes them.

Why it matters: the world, not the animation, decides where a body is. The
same data serves the web town, the stand-ins, a later 3D engine, and a
later physics body.

## 22. One barrier, shared by everyone

Nobody walks off the island: an invisible barrier keeps visitors and the
character on land, a couple of metres short of the water, or on a walkable
way (which is how docks and boardwalks reach out over it; a broken bridge
is not walkable). The rule lives in one small file with no dependencies.
The visitor's camera and the server use identical copies, and a test fails
if they differ. Another test walks every waypoint and every step between
them and checks each is somewhere a person can stand.

Why it matters: when the client and server each have their own idea of
where people can go, they drift apart. One rule, tested in both places,
keeps them the same.

## 23. The world's clock changes what things are

Some things change with the time of day, and the world, not a mind,
changes them. The fountain in the park is water by day and blood after
dark: at dusk its state becomes blood whatever its valve, and at first
light it goes back to dry or flowing. Actions are gated by state, so
drinking blood is only possible at night, from the fountain, for free; it
is no longer sold. Every mind finds this out the same way, by being there.

Why it matters: a need tied to a place and a time of day shapes a day more
than one tied to money, and it fits pattern 19: mechanics in the world,
discovered through the senses.

## 24. One body, simulated once, seen the same by everyone

Status: designed, not built. A later goal.

The plan for learned movement: a physics body, moved by a policy trained by
imitating human motion capture, so he balances, stumbles and recovers, and
his tiredness and soreness show in how he moves. For everyone to see the
same character, there is one simulation, on the server. The world decides
what he does; a motion process decides how, many times a second, and
reports events back (arrived, sat down, fell: a fall becomes something his
body feels). Every visitor receives the same small pose snapshots and draws
them a fraction of a second behind, blending between them.

Rejected: each browser simulating him from the same inputs. Physics comes
out slightly differently on different machines, and within seconds every
visitor would be watching a different character.

Why it matters: a shared, persistent character needs one authoritative
body. Clients render; they never decide.

