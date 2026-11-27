# Review Record — `SOLO-DECLARE`

| Field | Value |
|-------|-------|
| Change / PR | branch `feature/solo-declare` (PR number recorded at open) |
| Risk tier | `high-risk` — trigger: control-plane (`.kit/dials.conf`, `REQUIRED-CHECKS.md`) |
| Builder | conductor (main session) |
| Independent review by | the kit's `reviewer` subagent and `security-reviewer` subagent, each dispatched fresh on the built diff |

---

## THE GRADED SHAPE (`conformance/review-lane.sh`)

Builder: conductor (main session; the build is two config lines)
Reviewer: reviewer seat (fresh subagent, agents/reviewer.agent.md)

## Rounds

| commit | reviewer seat (role first) | verdict (APPROVE\|NEEDS-FIXES) | findings (n) |
|---|---|---|---|
| 952929f | reviewer (fresh subagent) | NEEDS-FIXES | 8 |
| 59c5de9 | reviewer (same seat, round 2) | APPROVE | 4 |

## Findings

| id | file:line | severity | disposition |
|---|---|---|---|
| R1 | head commit message | blocker | accepted — the Kit-Row, Kit-Class, Kit-Plan and Kit-Review trailers ride the final commit that adds this record, as its last contiguous paragraph |
| R2 | docs/architecture/2026-11-27-solo-declare-design.md:27 | major | accepted — the lens-5 claim is true at the graded head once R1's final commit lands; the design is not edited after its GO |
| R3 | docs/architecture/2026-11-27-solo-declare-design.md:40 | major | accepted — the design's prose controls are carried below in the gate grammar; the design is not edited after its GO |
| R4 | docs/reviews/2026-11-27-solo-declare.md | major | accepted — this record is that file, with the security section, added by the final commit |
| R5 | docs/plans/2026-11-27-solo-declare.md:25 | minor | fixed 59c5de9563e6 |
| R6 | BACKLOG.md:34 | minor | accepted — the row moved to In Review, whose table has no Links column; the PR cell carries the branch |
| R7 | BACKLOG.md:22 | nit | accepted — kit-owned prose under the removed placeholder; no project action |
| R8 | docs/plans/2026-11-27-solo-declare.md:6 | nit | accepted — the design GO note was recorded and published to origin from the root; ceremony-binding in CI reads it |
| N1 | docs/reviews/2026-11-27-solo-declare.md:21 | minor | accepted — the closing APPROVE row is added in this record's final commit |
| N2 | BACKLOG.md:41 | minor | accepted — the PR number goes into the In Review cell in a bookkeeping commit once the PR opens |
| N3 | docs/reviews/2026-11-27-solo-declare.md:46 | nit | accepted — grammar passes; the existing CI leg is named in the reason rather than as a test token |
| N4 | docs/reviews/2026-11-27-solo-declare.md:72 | nit | accepted — the ratification date is filled at the owner's merge GO |
| S1 | head commit message | medium | accepted — same as R1: the trailers ride the final commit |
| S2 | docs/architecture/2026-11-27-solo-declare-design.md:12 | medium | fixed 59c5de9563e6 |
| S3 | .github/workflows/solo-go.yml:29 | medium | accepted — pre-existing ceiling the design records at lens 7: the GO is self-asserted and trunk-trace runs in observe mode |
| S4 | (repository account model) | medium | accepted — pre-existing: one account is likely admin and can rewrite protection; protection is a drift alarm until a limited agent account exists |
| S5 | .github/workflows/solo-go.yml:32 | low | accepted — fork shadow-check risk; merges go only through land or actuate, whose T17 check reads the real run |

## Design-promised controls

| control (from the design) | discharged by |
|---|---|
| The dial reads solo from origin's default branch | not built, because this is a read-back after the merge: `sparkwright doctor` on origin's main, carried by board card PROTECT-SOLO |
| `solo-go-bound` declaration is well-formed | not built, because no new test is added: CI's existing `branch-protection.sh --declared-only` leg (through `verify.sh --require`) grades it |
| The hand merge is on the trail | not built, because it is read back after the merge: `promotion-verify.sh trace --recent` in the trunk-trace job binds it |

## Security review

ran — seat security, verdict: PASS-WITH-NOTES

## Task status

| task (from the plan) | state (`done` \| `pending`) | commit |
|---|---|---|
| 1. Create `.kit/dials.conf` | done | 952929f |
| 2. Add `solo-go-bound` to REQUIRED-CHECKS.md | done | 952929f |
| 3. Review and security seats; this record | done | 59c5de9563e6 |
| 4. prepush, push, PR, In Review | pending | — |
| 5. Merge GO, record, owner's hand merge | pending | — |

---

## Agent-review findings

- **Reviewer seat, round 1 (952929f): NEEDS-FIXES.** It confirmed the dial bytes are exact, the REQUIRED-CHECKS line passes the charset rule, and the workflow and verb markers that `--replace` needs are present. The findings are R1–R8 above.
- **Security seat (952929f): PASS-WITH-NOTES.** It found no Critical or High issue, confirmed the dial parsers fail closed, and found no lock-out or bypass from declaring `solo-go-bound` early. The notes are S1–S5 above.

## Human ratification
- **Ratified by:** SeaBrad72 (accountable maintainer, solo) — at the merge GO
- **Date:** ___
- **Ratification note:** recorded with the merge GO.

## High-risk acknowledgments
- Finding S2 → the window between the hand merge and `--replace` is closed by card PROTECT-SOLO. The conductor runs no `land`/`actuate` until `--replace` has run and `conformance/branch-protection.sh` reads it.
- Control-plane obligation → the agent may merge on the owner's recorded GO after this lands. Acknowledged at lens 7; the after-the-fact check is `trunk-trace`.
