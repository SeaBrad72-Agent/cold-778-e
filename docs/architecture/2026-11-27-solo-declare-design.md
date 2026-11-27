# Design — declare the solo merge relationship (`SOLO-DECLARE`)

**Backlog item:** `SOLO-DECLARE` · **Class:** control-plane (derived: `promotion-readiness.sh --class`) · **Size:** XS
**Provenance:** P1, originated in this slice, following `docs/adoption/vc-hosts.md` §Declaring solo and §Making the forge enforce it.
**Lane:** small control-plane lane (`skills/design/SKILL.md` §When to use): about 2 non-test lines, no new mechanism; this short file is the design GO's only basis.

## Design note

This repository is worked from one forge account. Until `.kit/dials.conf` on `main` reads `MERGE_RELATIONSHIP=solo`, the merge verbs (`actuate`, `land`) read **team** and every merge is by hand. This slice makes two edits:

1. It creates `.kit/dials.conf` with the single line `MERGE_RELATIONSHIP=solo`.
2. It adds `solo-go-bound` to the fenced block in `REQUIRED-CHECKS.md`, so one later `sh scripts/branch-protection-apply.sh --replace` (a person, at a terminal, after this merges) writes the **solo-agent** profile: zero required approvals, `enforce_admins:true`, `solo-go-bound` required. The owner chose this order on 2026-11-27 (one `--replace`, after the merge).

The owner's GO is recorded on the head sha with `promotion-verify.sh record` (class control-plane), and the owner merges this PR by hand. It is the one hand merge; from then on the agent merges on the owner's recorded GO.

**Approaches considered.**
(a) Dial only, then a second PR for `solo-go-bound` and a later `--replace`. That is two PRs and two control-plane cycles.
(b) **Dial plus `solo-go-bound` in one PR, then one `--replace` (chosen).** One PR, one human terminal act.
(c) `--replace` now, and again after the merge. Two human terminal acts, and `main` holds the ordinary solo profile in between.

## Lens review

| # | Lens | Answer |
|---|---|---|
| 1 | Project principles | Builder ≠ ratifier holds: the agent builds, and the owner gives the recorded GO and makes the hand merge. Nothing bends. |
| 2 | Friction | It removes friction for the owner: from this merge on, their per-PR act is the GO only, and no more hand merges. Cost to the owner: one hand merge now and one `--replace`. |
| 3 | Software practice | This is the kit's documented path (vc-hosts.md). The alternative, a team setup with a second account, was considered and ruled out: there is one account. |
| 4 | Agentic practice | The agent can check the dial's bytes, the class, and CI. It must trust the owner's GO words (self-asserted by name, vc-hosts.md "Honest cost"). After the merge, `doctor` reads the relationship back. |
| 5 | CI | No new job. The installed `solo-go.yml` starts reading `solo` instead of a green `na`. On this PR, `ceremony-binding` and `loop-state` pass only with the design GO and the trailers; both are carried. |
| 6 | DevOps and operations | Rollback is a PR that deletes the dial, in the same step as `--replace --team` (vc-hosts.md §Leaving solo). Signals: the `doctor` line `merge relationship: solo`, and `conformance/branch-protection.sh` after `--replace`. |
| 7 | Security, privacy and guardrails | Capability: **yes**. After the merge the agent may merge on the owner's recorded GO with no second approver. The GO is self-asserted, and a fabricated note would merge; the after-the-fact control is `trunk-trace` (observe mode). Data: none. Obligation: none. Security pass owed on the built diff. |
| 8 | Cost and efficiency | Two lines in config. Estimate: one CI run of about 4–10 min (the baseline `ci` run measured 3m57s). Owner time: one GO, one hand merge, one terminal command. Adopter-facing config, so it ships in this repo. |
| 9 | Product | Need: the solo owner should not have to hand-merge every PR. Metric: after the merge `doctor` prints `merge relationship: solo`, and the next PR merges through `land` or `actuate` on a recorded GO. |
| 10 | Design | Smaller shape: the dial alone (approach a). It falls short because it leaves the forge with no wall until a second PR lands. |
| 11 | Architecture | Seam: the `.kit/dials.conf` contract (parsed, never sourced, read from origin's default branch) and the REQUIRED-CHECKS declaration. Both are used as documented; no contract changes. |
| 12 | UX and accessibility | N/A — no user-facing surface. The owner meets the verbs' one line `merge relationship: solo (origin/main <sha>) — the recorded GO is the control`. |

## Design-promised controls

| Control | Discharged by |
|---|---|
| The dial reads solo from origin's default branch | `sh scripts/sparkwright doctor` after the merge prints `merge relationship: solo` |
| `solo-go-bound` declaration is well-formed | `sh conformance/branch-protection.sh --declared-only` green in CI on this PR |
| The hand merge is on the trail | `promotion-verify.sh record` on the head sha before the merge; `trace --recent` binds it |
