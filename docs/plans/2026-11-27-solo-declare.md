# Plan record — `SOLO-DECLARE`

| Field | Value |
|-------|-------|
| Row | `SOLO-DECLARE` |
| Design of record | `docs/architecture/2026-11-27-solo-declare-design.md` (design GO recorded on `c74e6f2`, go-by SeaBrad72) |
| Change class (derived, never self-asserted) | `control-plane` |

## Team

| seat | tier | concrete model | why |
|---|---|---|---|
| conductor | unset | unset (single-model session; `.kit/model-map.conf` all `unset`) | the main session; it also builds this two-line slice (Builder: conductor) |
| builder | unset | unset | not dispatched: the build is two config lines, written by the conductor |
| reviewer | unset | unset | the `reviewer` seat (`agents/reviewer.agent.md`), dispatched fresh on the built diff |
| security | unset | unset | the `security-reviewer` seat, dispatched fresh on the built diff (lens 7: capability change) |

## Task list

1. Create `.kit/dials.conf` with the single line `MERGE_RELATIONSHIP=solo`.
2. Add `solo-go-bound` as the last line of the fenced block in `REQUIRED-CHECKS.md`.
3. Dispatch the reviewer and security seats on the built diff; write `docs/reviews/2026-11-27-solo-declare.md` from their verdicts.
4. Run `sh scripts/sparkwright prepush` in the dev clone (after fetching the design note); push; open the PR; move the row to In Review with the PR number.
5. Ask the owner for the merge GO on the CI-green head; record it (`promotion-verify.sh record --gate merge`); the owner merges by hand.

## Ceremony budget
Control-plane, XS, small control-plane lane: this plan, one reviewer round, one security pass, the review record, one prepush, one CI run; owner acts: design GO (given), merge GO, the one hand merge, then `branch-protection-apply.sh --replace`.

## Verification plan
- `sh scripts/sparkwright prepush` in the clone: proves the touched surface (board gates, since `BACKLOG.md` is touched; `review-lane --pre-push`; `loop-state --head`). It does not prove the CI-derived core-live checks; CI runs those.
- CI on the PR: `branch-protection.sh --declared-only` proves the REQUIRED-CHECKS block is well-formed. `ceremony-binding` proves a design GO note binds the design commit. It does not prove the dial is read as solo; that is read back after the merge with `sh scripts/sparkwright doctor`.
