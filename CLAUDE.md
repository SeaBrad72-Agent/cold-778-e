# cold-778-e — Claude Project Guide

## 1. Entry contract — do this BEFORE any mutating action

⚠️ **Start the session in this repository's root, not a parent or a subfolder:** Claude Code loads the guard hooks only from the launch directory. If your launch directory (it is in your context) is not this root, stop before any edit and tell the human, who can confirm with `/hooks`. **Five acts, every slice, before the first edit / commit / PR.** The skills in `skills/` govern process work here (index: `skills/using-skills/SKILL.md`).

1. **Derive the change class.** Put the changed paths in a file, one per line, and run `sh conformance/promotion-readiness.sh --class --changed <listing>`. Pass the listing file, never a path — a path argument gets its CONTENTS classified. `conformance/agent-boundary.sh --state` is not a classifier; without `--changed` it answers `NONE`. Never self-assert the class.
2. **Read the governing skill** — `skills/<name>/SKILL.md`. Design before code, evidence before "done"; search `docs/governance/DECISIONS.md` for a ruling on this surface; a design answers the twelve lenses in `skills/design/SKILL.md`.
3. **Claim the row by its id** — the backticked `ROW-ID` that leads its Item cell (`[A-Z0-9][A-Z0-9-]*`), with `sh scripts/board.sh claim <ROW-ID>`; that id is your `Kit-Row` value. No row, no build.
4. **Carry the Entry Declaration as commit trailers** — `Kit-Row` + `Kit-Class`, every class. `conformance/loop-state.sh` checks them on your PR's final commit: present once each, row on the board, class matching. A `Kit-Plan`, `Kit-Review` or `Kit-Scope` you carry is checked too; any other `Kit-*` field is ignored. Enforced by default; `LOOP_STATE_MODE: observe` in `adopter-gates.yml` opts out.
5. **State the ceremony budget in one line**, derived from the class and size, so the owner can veto it in a sentence. Push board edits before you ask for review; seek approval only on the final diff. **Seats:** the conductor dispatches each build task to a **builder** seat, each review to a **reviewer** seat, and a separate **security** seat on the built diff. Each is typed by its `agents/*.agent.md` definition and dispatched through the harness's native binding where one exists (`START-HERE.md` step 7, `docs/operations/harness-adapters.md`). The conductor prefers a typed builder seat; when it builds, the Builder line says `conductor` (conduct, not enforced). The review record names each seat's role; `review-lane` grades the names and cannot see the dispatch.

**One command:** `sh scripts/sparkwright start <ROW-ID> [--changed <listing>]` runs acts 1 and 3 and prints this trailer block; the printed class is provisional — the gate re-derives it.

> ⚠️ **The trailer block must be the LAST paragraph of the commit message, and contiguous.** A blank line inside it truncates it — git reads only the paragraph after the blank, so every `Kit-*` field above it is lost.

> **Guard refused a command?** See `docs/operations/runtime-guards.md` §*The escape card*: one sanctioned retry per the escape card, else stop and escalate.

## The entry contract above is kit-owned

> **Do not edit, reword or renumber the section above.** It is the kit's entry contract, held **byte-identical in every adapter's declared `contextFile`** (`adapters/*/adapter.json`) so that every harness reads the same one. A generated project charter carries it because `claude-code` declares `CLAUDE.md` — and after Inception that name belongs to *this* file, not the kit's principles doc (which is renamed `ENGINEERING-PRINCIPLES.md`). The project's **own** numbered sections are a separate sequence and begin at *§1 Overview* below; the `(§N)` labels elsewhere in this file refer to that sequence.
>
> **This heading is what CLOSES the contract.** The locked region runs from the `##` above to the next `##` — this one — so everything below is yours to fill in, and anything inserted *above* it (even a blank line) breaks byte-equality. `conformance/agents-brief.sh` compares the region, not a marker: keeping the heading while appending to the section is exactly the case it exists to refuse.
>
> **Three placement rules go with it**, because comparing the section is not enough — each of these was measured green before it was closed:
> 1. §1 must be this document's **first** `## ` section. Only blank lines and the single `# ` title may precede it. Prose, a status line or a `### 0. …` heading above it renders *first*, and an entry contract an agent reads second is not an entry contract.
> 2. Nothing between §1 and this heading — the region runs to here.
> 3. **This heading must render visible text.** `## <!-- -->`, `## &nbsp;` and a zero-width-space heading are refused: they render as no heading at all, putting whatever follows flush under act 5.
>
> **Inception (Phase 0) is exempt from §1.** The five acts govern *loop* work — the slices that change a repo that already exists. `incept` is the one-time bootstrap that **creates** the repo and its control plane, so at that moment there is no board to claim a row on, no branch to carry trailers on, and no change class to derive. §1 binds from the **first feature branch** onward. See the "Inception exception" in `skills/design/SKILL.md` and `skills/build/SKILL.md`, and "Bootstrap order (incept-first)" in `DEVELOPMENT-PROCESS.md` §3.

---

**Project:** cold-778-e
**Intent owner:** SeaBrad72
**Status:** Inception
**Created:** 2026-11-27
**Kit version adopted:** v3.250.0

---

## Inherited standards (do not duplicate)

This project follows Sparkwright (the agentic SDLC kit). Read these (paths relative to wherever the kit lives in/by your repo), do not restate them here:

- **Principles + Definition of Done:** `ENGINEERING-PRINCIPLES.md`
- **Process (the flow):** `DEVELOPMENT-PROCESS.md`
- **Standards (the universal bar):** `DEVELOPMENT-STANDARDS.md`
- **Your stack profile (the concrete how):** `profiles/<your-stack>.md`

This file holds only what is **specific to this project**.

---

## 1. Overview

**Problem:** Developers want a quick word / line / character count of text from a file or stdin, without platform quirks.
**Vision / success metrics:** Counts that match `wc`, correct on UTF-8, under 100 ms on a 1 MB file.
**Scope boundaries:** In: a Node CLI, `textstat`, that reads files or stdin. Out: GUI, network, persistence.

## 2. Tech stack (ADR-000)

> Chosen at Inception via a spike; full rationale + alternatives in `docs/architecture/ADR-000-*.md`.

- **Language(s) / runtime:** TypeScript on Node.js 24
- **Framework(s):** none (plain Node CLI on the typescript-node scaffold)
- **Data store:** none (scope excludes persistence)
- **Testing:** the profile's test runner (see `profiles/typescript-node.md`)
- **Deploy target / hosting:** none; npm-installable CLI
- **Key libraries / services:** Node built-ins only (`Intl.Segmenter`, `TextDecoder`, streams)

## 3. Per-project process configuration

> These are the knobs `DEVELOPMENT-PROCESS.md` says are set per project.

- **Backlog backend** (§6): md — [link] (mapping: `docs/work-tracking/adapters.md`)
- **Stack profile** (§2): typescript-node — the kit profile this project was incepted with (`incept --stack`). It determines the scaffold, the emitted CI, and which `profiles/*` survived the export prune. Recorded because it was the **one inception input nothing wrote down**, and a kit update must prune a new export to the same shape to compare it against `kit-base` (`docs/operations/kit-base.md`).
- **CI platform** (§14): github — the CI platform this project was incepted with (`incept --ci`); github wires `.github/workflows/ci.yml`, gitlab wires `.gitlab-ci.yml`. The contract is the gate-ids, not the platform (`docs/operations/ci-platforms.md`). Recorded because it is an inception **input**: a kit update replays incept over `kit-base` and must wire the *same* platform, or every CI file reads as a conflict (`docs/operations/kit-base.md`).
- **DB archetype** (§ archetype): db-backed — whether inception kept the reference DB archetype (default) or ran `incept --no-db`, which strips the `kit:db-backed` CI region, the `.db-backed` marker, the DB/Redis `.env.example` lines and `scripts/dr-drill.sh`. Recorded for the same reason as the CI platform: it is an inception **input**, and after the fact the tree can only be *guessed* at (`docs/operations/kit-base.md`).
- **Autonomy-tier defaults** (§12): the kit's standard action→tier matrix, no deltas.
- **SLO / error-budget posture** (§9): soft track-and-guide; no target SLOs defined.
- **Data classification** (§privacy): Public — the highest tier this project handles. Confidential/Restricted ⇒ a privacy review (`docs/enterprise/data-governance.md`; verified by `conformance/readiness.sh privacy-ready`).
- **Operator fluency** (§onboarding): Practitioner — the human operator's enterprise-SDLC experience; the agent adapts its assistance accordingly (the "New to enterprise SDLC?" section of `START-HERE.md`; behaviour in `docs/operations/operator-fluency.md`).
- **Process mode** (§ ceremony): lean — how much ceremony incept scaffolds + surfaces (ceremony only; solo-vs-team governance is the separate `enforce_admins` / `docs/operations/review-lane.md` axis). Does **not** change which controls are ENFORCED: every gate keys on its detected trigger (Dockerfile, `evals/`, data surface, classification), never on this field.
- **Governance** (§ solo/team): solo — solo = one account: the declaring merge is the owner's, and from then on the agent merges on the owner's recorded GO (`enforce_admins:false` until `branch-protection-apply.sh --replace` writes the solo-agent profile); team = non-author approval + second-reviewer SoD (`enforce_admins:true`). Enforced server-side (branch protection), not by this field; this records the fork chosen at Inception (`docs/operations/review-lane.md`).
- **Branch protection** (§branch-protection): [github-verified | attested: <host + mechanism>] — how `main` is protected. GitHub is **verified live** at Inception-Done (`conformance/inception-done.sh`); other hosts the kit can't yet query require this **attestation** (`docs/adoption/vc-hosts.md`). Fill deliberately — do not auto-stamp.
- **Target harness(es)** (§harness-neutrality): claude-code — the LLM/agent harness(es) this project runs; each is verified against the boundary contract at Inception (`docs/operations/harness-adapters.md`). Record *why* this harness fits in the Harness fit rationale field below.
- **Review routing / ownership** (§11): the agent's reviewer and security seats review every diff; the owner (SeaBrad72) gives the recorded GO on every merge.
- **WIP limits:** 1
- **Environments** (§ "Environments & promotion"): Dev → Prod only; a CLI with no hosted tiers, so no deploy. Production promotion is human-gated.
- **Agentic** *(does this project run autonomous agents?)*: no — **yes** ⇒ declare the agent-run trace posture in RUNBOOK §8 (`Agent-ops:`); see `docs/operations/agentic-ops.md`. Verified by `conformance/readiness.sh agentops-ready`.

### Harness neutrality

> The harness (§harness-neutrality) is a concretization axis — choose it by *fit*, not by "it's the default." Record the fit reason in the `#### Harness fit rationale` field below; `conformance/harness-decision-integrity.sh` rejects bias-appeal and requires a cited fit dimension. Only `claude-code` is a **verified** harness (the kit self-hosts on it); `codex` is **floor-verified** (the universal-layer floor exercised end-to-end in a cold field test — no native bonus); `gemini` / `cursor` are **experimental** — declared against the boundary contract, not exercised end-to-end (unproven), not "supported." Maturity cards + fit rubric: `docs/operations/harness-adapters.md`.

#### Harness fit rationale

claude-code — fit dimension: native-hooks (the kit's guard runs as a Claude Code `PreToolUse` hook, so enforcement is native rather than piped) and multi-agent (native subagent bindings for the builder, reviewer and security seats).

### Model neutrality

> The model is the fourth concretization axis (stack, deploy target, harness, model). Each seat runs on the model your project judges most appropriate for it, mapped in `.kit/model-map.conf` (every value `unset` until you decide; `unset` is the honest single-model case and nothing goes red). Once you bind a model, record the fit reason below and a `docs/governance/DECISIONS.md` entry; `conformance/decision-integrity.sh model` rejects bias-appeal and an unfilled field, and `conformance/model-tiering.sh` checks the entry resolves. Detail: `docs/adoption/model-tier-binding.md`.

#### Model fit rationale

[why these models fit THIS project — cite a fit dimension: reasoning / long context / tool use / code generation / review depth / cost / latency / availability / residency / a measured A/B …]

## 4. Roles (this project)

> Fill each function from `DEVELOPMENT-PROCESS.md` §2 with a human or agent. One may hold several; builder ≠ reviewer; humans ratify. For a role-oriented view (PO/BA · Designer · QA · DevOps/SRE mapped to these functions), see the **Personas** table in §2.

| Function | Who/what |
|----------|----------|
| Intent owner | SeaBrad72 |
| Lead / integrator | SeaBrad72 |
| Builder(s) | the agent (conductor / engineer seat) |
| Reviewer(s) | the agent's reviewer seat, plus the owner's GO |
| On-call / operator | SeaBrad72 |
| Security owner | SeaBrad72 |

## 5. Quickstart

> Stack-appropriate, copy-paste-ready. Keep current (artifact-flow owner: building agent).

```bash
# install
[...]
# run locally
[...]
# test
[...]
# build
[...]
```

## 6. Project conventions & gotchas

> Only non-obvious, project-specific things. Patterns that generalize go to the pattern library, not here.

- [convention or gotcha]
- [convention or gotcha]

## 7. Key references

- **Repo:** [link]
- **Live / staging URLs:** [links]
- **Backlog / board:** [link]
- **Roadmap:** [link]
- **RUNBOOK:** `./RUNBOOK.md`
- **Running more than one session?** Read `agents/orchestrator.agent.md` (the conductor): checkout custody, the push slot, GO relays, pinned review checkouts.

---

**Last Updated:** 2026-11-27

## Owed from Inception

Written by sparkwright new: what it chose for you, and what a person still owes.
- Intent owner: stamped as [owed] in README.md and CLAUDE.md (it seeds no CODEOWNERS rule); a person names the real owner.
- CI platform: github, the default; change it only on purpose.
- Mode: lean, the default (incept --mode).
- Operator fluency: undeclared; the agent drafts it in docs/inception-draft/PROJECT-CLAUDE.md (CLAUDE.md §3) once the owner states it; a person pastes it into CLAUDE.md.
- Database archetype: the stack's default; strip or keep the database to match the project.
- Deploy target: choose one (docs/adoption/DEPLOYMENT-ENVIRONMENT.md).
- Charter: the agent writes docs/inception-draft/PROJECT-CLAUDE.md from the owner's words (problem, vision, success metrics, scope); a person pastes what is right into CLAUDE.md §1 and deletes docs/inception-draft/. The guard refuses the agent's own write to CLAUDE.md; later edits go through a dev clone and a PR.
- ADR-000: record the real stack decision and its fit rationale in docs/architecture/ADR-000-stack.md.
- Harness fit: record why the chosen harness fits.
- Roles: the agent drafts them in docs/inception-draft/PROJECT-CLAUDE.md (CLAUDE.md §4); a person pastes them into CLAUDE.md.
- Autonomy tiers: the agent drafts them in docs/inception-draft/PROJECT-CLAUDE.md (CLAUDE.md §3); a person pastes them into CLAUDE.md.
- SLO: the agent drafts the SLO and error-budget posture in docs/inception-draft/PROJECT-CLAUDE.md (CLAUDE.md §3); a person pastes it into CLAUDE.md.
- SECURITY.md: add the real disclosure channel once the repository exists (there is no origin yet).
- Branch protection: once the repository and its first CI run exist, a person runs sh scripts/branch-protection-apply.sh --replace on an unprotected main, in a real terminal, with an admin credential (docs/operations/forge-identity-switch.md, step 5).
- Relaunch: start the agent session with the line incept printed above ("Start your agent session here").
- Agent forge account: give the agent its own limited forge account (docs/operations/forge-identity-switch.md).
- First push: a person creates the project's empty repository on your forge and adds it as origin; the agent commits the baseline after the person's paste, pushes it with git push origin main and reads it back with git ls-remote --heads origin main (the guard admits this first push). Until then, while any agent-boundary part is off, preflight reports the declaration UNTRUSTED (no origin remote).
- Agent boundary: if sh scripts/preflight.sh prints any FAIL boundary or FAIL egress line, follow its Fix or Remedy line (a person acts, for example merging the user-level block into ~/.claude/settings.json, or declares an opt-out in .kit/boundary-optout.conf), then re-run it to confirm.
- Declare solo: the repository reads team until this merges. (1) Open a PR that creates .kit/dials.conf with the line MERGE_RELATIONSHIP=solo. (2) Run sh scripts/promotion-verify.sh record on that PR's head sha: class control-plane, your own words in --token, your name in --go-by. (3) Merge that PR yourself, by hand: it is the last merge by hand, because once it has merged sh scripts/sparkwright doctor prints the handover line and from then on actuate and land merge on your recorded GO. See docs/adoption/vc-hosts.md, Solo or team.
