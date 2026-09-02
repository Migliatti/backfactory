# Contributing to backfactory

Thanks for looking. This project is in its **design phase** — there is no game code yet — so the useful contributions right now are not the usual ones. This document says what actually helps today, and what will help later.

## What helps most right now

### 1. Argue with the research

The two research documents are the foundation everything else rests on:

- [`docs/research/backend-curriculum-spine.md`](docs/research/backend-curriculum-spine.md) — the dependency-ordered curriculum
- [`docs/research/factory-game-prior-art.md`](docs/research/factory-game-prior-art.md) — prior art, the fidelity ladder, and the skill-transfer evidence

They cite their sources per claim precisely so they can be checked. If a source was misread, a stronger source exists, or a conclusion doesn't follow from its evidence — **open an issue**. That is the highest-value contribution available today, and it is genuinely wanted.

### 2. Weigh in on an open decision

Planning lives as a [wayfinder map](https://github.com/Migliatti/backfactory/issues/1): one issue per open decision, wired with dependency edges so you can see what is actually decidable right now versus what is waiting on something else.

Open tickets are questions, not tasks. If you have experience with something a ticket is deciding — teaching beginners, dataflow tooling, game progression design, running HTTP infrastructure — comment on it. Concrete experience beats opinion, and prior art beats both.

### 3. Tell us where the design is wrong

The design is built on a stack of assumptions that are written down and numbered in the [map body](https://github.com/Migliatti/backfactory/issues/1). Every one of them is fair game. The ones most likely to be wrong, in the maintainer's own estimation:

- that a factory metaphor survives contact with real HTTP semantics
- that a beginner can diagnose a backend failure from a visual factory floor
- that skill transfer can be designed in rather than hoped for

## What will help later

Code contributions, level authoring, translations, and art — once there is something to contribute to. The stack isn't chosen yet; that decision is [an open ticket](https://github.com/Migliatti/backfactory/issues/9). Watch the map if you want to be here when it lands.

## Two things worth knowing

**About the AI rule.** This project is built with an AI coding agent, under a deliberate constraint: the agent writes the shell (engine, UI, build, boilerplate), and every file that *is* a curriculum concept is written by hand by the maintainer, who is learning backend development by building this. That rule exists to protect the maintainer's learning. **It does not apply to you** — contribute however you normally work.

**About language.** Code, documentation, issues and this repo's contributor-facing surface are in English. The game's teaching content ships in Brazilian Portuguese first, with i18n in place from the start, English to follow.

## Issue conventions

Issues are triaged with five labels: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`. Wayfinder decision tickets carry `wayfinder:research`, `wayfinder:prototype`, `wayfinder:grilling` or `wayfinder:task`, and hang off the map as sub-issues.

You don't need to label anything yourself. Just describe the thing clearly.

## Code of conduct

Be decent. Disagree with the idea, not the person. Beginners are the entire point of this project — condescension toward them is the one thing that will get a contribution rejected on sight.

## License

By contributing, you agree that your contributions are licensed under the [MIT License](LICENSE), the same as the rest of the project.
