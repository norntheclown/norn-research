# Build notes: research package v1 (initial public package)

Pushed 2026-11-01. First content commit to norntheclown/norn-research
(replacing the generated initial README).

## What was built

The initial public research package for the Norn the Clown project: 7 files.

- `README.md`: what Norn is, the three core ideas, what lives in the repo,
  and how another builder can reuse the patterns. Links the live town at
  https://norntheclown.com.
- `docs/architecture.md`: the reusable pattern catalog, written from the
  project roadmap and the shipped phases: identity separate from the model,
  the two-function world interface, the self-checking thought loop, the
  decision loop, content as data, the hybrid brain, a world that pushes back,
  and two-layer feelings.
- `docs/world-adapter.md`: the `observe()` / `act()` contract, v1, copied
  from the project source.
- `docs/builds/phase-0.md`, `phase-1.md`, `phase-2.md`: the historical build
  notes from the main project, republished here as research records with a
  header noting the full project repository is private.

What is deliberately NOT here: the brain codebase, the identity store code,
the world server code, and the content JSONs. Those stay in the private
project repository by the creator's choice. This repo holds the patterns,
the contracts, and the notes.

## Test results

Docs-only package, no code shipped. Checks run before push:

- All 7 files verified UTF-8, no binaries.
- Secret and private-info scan passed on every file: no tokens, no API keys,
  no personal emails, no addresses, no phone numbers, no machine names.
- No em dashes anywhere in the package.

## Deploy notes

- Pushed to norn-research `main` via the snapshot push flow, replacing the
  generated initial README.md. No other remote content existed.
- Public URL: https://github.com/norntheclown/norn-research

## Open items

- Future phases get their build notes added here as research records when
  they land.
- The world adapter contract is v1; if it ever changes, old fields keep
  working per the versioning rule in the contract.
- Whether to publish reference code samples (e.g. a minimal observe/act
  implementation) is the creator's call.
