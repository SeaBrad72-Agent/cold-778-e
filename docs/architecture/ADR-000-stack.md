# ADR-000: Technology Stack Selection

**Status:** Accepted
**Date:** 2026-11-27
**Deciders:** SeaBrad72 (intent owner, lead)

## Context
We're starting cold-778-e: `textstat`, a small command-line tool that counts lines, words and characters of text from files or stdin, matching `wc`, correct on UTF-8, and under 100 ms on a 1 MB file. It has no GUI, no network and no persistence. No technology was predetermined.

## Decision
Use **TypeScript on Node.js 24**, captured in `profiles/typescript-node.md`, built as a CLI on the profile's scaffold.

## Fit rationale
- **Workload:** IO-bound, single-pass streaming over text. Node's stream API reads stdin and files incrementally, and a 1 MB file is far inside its range; no CPU-bound or real-time need that would argue for Go or Rust.
- **Ecosystem / libraries:** Unicode-aware text handling (`Intl.Segmenter`, UTF-8 `TextDecoder`) is in the runtime, so the UTF-8 correctness metric needs no third-party dependency.
- **Deploy target:** distributed as an npm-installable CLI (`bin` entry); no hosted runtime, which suits Node's packaging.
- **Team skills / tooling:** the profile ships a runnable scaffold with the type-checker, linter, test runner and CI gates wired, which gets a green pipeline on the empty project before feature work.

## Alternatives considered
1. **Go** — pros: single static binary, fast start-up · cons: the profile is service-shaped; a single binary adds release packaging the scope does not need.
2. **Python** — pros: quick to write, good text handling · cons: interpreter start-up eats into the 100 ms budget; packaging a CLI is less uniform.
3. **Rust** — pros: fastest, strong Unicode crates · cons: delivery velocity for a tiny tool does not justify the ceremony.

## Consequences
- Profile in effect: `profiles/typescript-node.md` (selected).
- Easier: one runtime for build, test and run; UTF-8 handling built in. Harder: Node start-up time must be measured against the 100 ms target. Accepted trade-off: the scaffold's HTTP-service parts and DB archetype are not needed by a CLI and will be stripped in a later slice.

## Follow-up
- [x] Profile selected
- [ ] CI baseline green on empty project (per profile)
- [ ] `.env.example` seeded from profile
