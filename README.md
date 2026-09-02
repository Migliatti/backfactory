# backfactory

An open-source browser game that teaches API integration, automation and backend development — themed as a factory floor.

Machines are real HTTP endpoints. Belts are real HTTP calls. Buffers are real queues. A stalled machine is a real timeout. The factory is not a metaphor for a backend — it **is** one, and you learn backend development by making it run.

> ### Status: design phase
>
> **Nothing is playable yet.** The design is being worked out in the open, as a map of decision tickets on this repo's issue tracker: **[the wayfinder map →](https://github.com/Migliatti/backfactory/issues/1)**
>
> This README will say *"play it here"* when there is something to play. Until then, what's public is the reasoning — which is the interesting part anyway.

---

## What it teaches

The curriculum is not invented. It comes from a [dependency-ordered spine of 15 tiers](docs/research/backend-curriculum-spine.md) built from primary sources — roadmap.sh, freeCodeCamp, The Odin Project, MDN, the Node.js docs, Stripe's idempotency and webhook documentation, and the concept sets of Zapier, Make, n8n and Node-RED.

The spine runs roughly:

```
client/server → request/response → calling someone else's API → running code
outside the browser → your own server → middleware → persistence → auth →
failure as a subject → retries → idempotency keys → webhooks → polling vs push
→ automation composition → operating it → scale
```

Two findings from that research shape the whole game:

- **Integration comes before "build your own server."** freeCodeCamp's own global ordering teaches consuming APIs before the backend certification, and four commercial automation platforms teach automation with *zero* server concepts. You start by wiring things together, which is what a factory does.
- **The webhook is the thesis.** It is the single concept that requires the most of the spine — routing, POST, body parsing, status codes, signature verification, retries, duplicate delivery, no ordering guarantee — and it is **absent from every formal beginner curriculum surveyed**, while being day-one vocabulary at Zapier, Make, GitHub and Stripe. That gap is where this game lives.

## Design principles

**The factory is a real backend.** Not a simulation of one. This is the expensive choice and it is deliberate: a simulated factory teaches the *feeling* of backend development and none of the substance.

**Failure is the curriculum, not the difficulty setting.** Read as a syllabus, Stripe's webhook documentation is a list of failure modes: retries with exponential backoff, duplicate deliveries, unordered events, timeouts, signature mismatches. A factory where the pipes occasionally deliver twice, out of order, or not at all is *more* faithful to the domain than one where they don't. Scrap and rework are content, not punishment.

**Level 1 has no text editor.** Research on [block-to-text transition](docs/research/factory-game-prior-art.md) shows the gap between "no code" and "real code" is not one step but **thirteen separable burdens** — syntax memorisation, bracket matching, identifier matching, types, error messages, and so on. They can be turned on one at a time. And the prior art is emphatic that queues, workers, ordering, duplicate delivery, idempotency and backpressure are all teachable with no editor at all.

**The vendors are fictional, the HTTP is real.** You integrate with in-universe suppliers served by this project's own backend — real auth, real rate limits, real latency, and **authorable failures**. Third-party APIs would be authentic but fragile; a level cannot be allowed to break overnight because someone else shipped.

**Skill transfer is a feature, not a side effect.** A meta-analysis of 105 studies puts programming games at *g* = 0.16 against active control groups, with confirmed publication bias. The studies that *did* find transfer share one mechanism: the two representations were made explicitly isomorphic. So that isomorphism gets designed in on purpose rather than hoped for. The [full evidence, including the negative findings](docs/research/factory-game-prior-art.md), is in the repo.

## How this project is built

This is a learning-by-building project, and the process is part of the artifact.

**Research before design.** Both research passes above were completed and written up with per-claim source attribution *before* any design was locked. Where sources disagree, the disagreements are documented rather than smoothed over.

**Decisions are public and ordered.** Planning lives as a [wayfinder map](https://github.com/Migliatti/backfactory/issues/1): one issue per open decision, with native dependency edges, so the frontier of what is actually decidable right now is visible at a glance. Every closed ticket carries the reasoning that closed it.

**An explicit rule against AI ghostwriting.** This project is built with an AI coding agent, and the failure mode of that is well understood: you end up with a working repository you do not understand. So the boundary is drawn structurally — the agent writes the shell (engine, UI, build, boilerplate); every file that *is* a curriculum concept is written by hand. The agent may ask questions and point at errors in those files. It may not write the code.

## Research

| Document | What's in it |
| --- | --- |
| [Backend curriculum spine](docs/research/backend-curriculum-spine.md) | 15 dependency-ordered tiers, where each source places each concept, nine documented disagreements, and what to deliberately leave out |
| [Factory game prior art](docs/research/factory-game-prior-art.md) | The fidelity ladder from no-code to code, a six-tool dataflow vocabulary table, skill-transfer evidence with its negative findings, and progression loop analysis |

Both are written for humans, cite their sources, and are meant to be argued with.

## Following along

- **Watch the [map](https://github.com/Migliatti/backfactory/issues/1)** to see decisions land as they're made.
- **Open an issue** to disagree with one. The research documents are explicitly meant to be challenged — if a source was misread or a better one exists, that is a valuable contribution.
- Contribution guidelines and a license will land alongside the first playable slice.

## License

To be decided before the first release.
