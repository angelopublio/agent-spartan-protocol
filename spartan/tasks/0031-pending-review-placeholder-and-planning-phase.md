---
protocol: "1.1.0" # x-release-please-version
id: pending-review-placeholder-and-planning-phase
created_at: 2026-09-01
status: active
phase: planning
task_type: planning
risk: material
current_role: planner
next_role: planner
updated_at: 2026-09-01
handoff_id: none
next_handoff_id: HX-002
---

# Keep PENDING Review as the shipped placeholder; keep planning phase until verdict persistence

## Objective

A producer round that finishes a planning artifact leaves `## Review` as the shipped `Verdict: PENDING` placeholder and leaves `phase: planning`. The protocol states that ownership in `protocol.md`, with one conformance row and a template that no longer embeds instructional prose in the findings line. A reader who never heard of Spartan Bridge can follow the same artifact by hand.

## Context

Writable persistence already assigns who writes `## Review` and who updates `phase`: the explicitly authorized writable round that persists an adopted verdict. Canonical envelope already requires a precise `## Next Handoff` shape while a proposal is outstanding. `## Review` has no equivalent producer-side rule, so a planning round can add prose under `Verdict: PENDING` and set `phase: reviewing` while `task_type` is still `planning`.

Those two edits are independently wrong as handoff truth: producer notes belong in Work Completed, Evidence, Blockers, or Next Action, and `phase` is descriptive rather than a state machine (`protocol.md` Normative language). They also happen to be the shapes a Bridge `review` dispatch refuses (`task_invalid` on the planning/`reviewing` pair; `composition_failed` when `## Review` is not the short placeholder). The Bridge already fails closed; this task does not change it. The hole is that the portable skill does not tell the producer to emit a well-formed artifact.

The shipped template is part of the hole. `assets/task-template.md` currently puts two instructional sentences on the findings line after `- None recorded.` A task copied from the template is born with a `## Review` body that a persistence write (and a Bridge compose) cannot treat as the placeholder.

Bridge repository task `0057` already deferred the producer-skill edit to this package: the companion that keeps the artifact canonical coming out of the producer round is owned by the protocol skill, not the Bridge.

## Scope

- `agent-skill/skills/spartan/references/protocol.md` § Review and completion: one new paragraph immediately after the atomic-persistence sentence (the paragraph that currently ends by pointing at `review-persistence.md`).
- `agent-skill/skills/spartan/references/review-persistence.md` Conformance set: one new row `PLACEHOLDER`.
- `agent-skill/skills/spartan/assets/task-template.md` `## Review` findings line: drop the instructional sentence so the line is exactly `- None recorded.`
- This artifact.

## Out of Scope

- `agent-skill/skills/spartan/SKILL.md`. Planners already read `protocol.md` before acting. No third copy.
- A new subsection in `review-persistence.md`, and any new item on its Reviewer checklist.
- Root `AGENTS.md` and `spartan-bridge/config.yaml`.
- Any file in `agent-spartan-protocol-bridge`, including diagnostics task `0057`, adapters, reason codes, and byte-matching predicates.
- Existing task artifacts `0001`–`0030`. No retrofit, no re-attribution of an old `## Review`.
- New frontmatter fields, roles, verdicts, phases, or `task_type` values.
- A pairing table that makes `phase` track `task_type`, a rule that a producer MUST NOT advance `phase` on an implementation artifact, and any byte-identity, splice, or runtime matching requirement.
- `src/`, package manifests, runtime dependencies, executable checkers.
- Commit, push, tag, pull request, merge, and release, unless the human authorizes that action for that round.

## Constraints

- Markdown instructions, references, and templates only.
- The protocol remains usable with the skill removed and with no Bridge installed (deletion test; handoff MAY be executed by an external tool the protocol MUST NOT depend on).
- `phase` stays descriptive: the new text MUST NOT create an executable state machine.
- `agent-skill/skills/spartan/SKILL.md` stays preferably below 200 lines and is not edited.
- Persisted repository content is English.
- Automatic implementation write scope already includes `agent-skill/` and `spartan/`. This plan does not require a human implementer.

## Decisions

### D1 - `## Review` while PENDING is producer-untouchable placeholder, stated as section ownership

While `## Review` shows `Verdict: PENDING`, no producer round adds content there. The body stays the shipped placeholder. Producer prose stays in Work Completed, Evidence, Blockers, and Next Action. The protocol states this the same way Canonical envelope states `## Next Handoff`: a required section shape, not a byte-identity predicate for a runtime splice. An external tool MAY refuse a non-placeholder body; that refusal is outside this protocol.

### D2 - A planning artifact keeps `phase: planning` until plan-review persistence; `phase` does not track `task_type`

A `task_type: planning` artifact keeps `phase: planning` until the writable round that persists the plan-review verdict. Setting `phase: reviewing` while `task_type` is still `planning` is malformed. An implementer MAY set `phase: reviewing` on a `task_type: implementation` artifact before implementation review. The protocol adds no pairing table and no “producer MUST NOT advance `phase`” rule that would forbid that implementation declaration. `phase` remains descriptive.

### D3 - Load-bearing paragraph in `protocol.md`; one conformance row; no third copy

The contract is the new paragraph in `protocol.md` § Review and completion, immediately after the atomic-persistence sentence. `review-persistence.md` gains exactly one conformance row, `PLACEHOLDER`, mapping a PENDING identified artifact to “producer writes nothing in `## Review`; body remains the shipped placeholder.” The Reviewer checklist is unchanged. `SKILL.md` is unchanged. The existing `LEGACY` row is unchanged.

### D4 - The shipped template carries the short placeholder

`assets/task-template.md` findings line under `## Review` is exactly `- None recorded.` The instructional sentence that currently follows it on the same line moves into the D1/D3 paragraph in `protocol.md` (skill instruction, not task-file state). New tasks are born with a `## Review` body a writable persistence round can replace. Existing tasks are not retrofitted (D5).

### D5 - LEGACY `## Review` is not rewritten

No round rewrites or re-attributes an old `## Review`. Upgrading a legacy artifact’s handoff identifier fields does not canonicalize `## Review`. Identifier-less PENDING sections that already carry extra prose stay as they are.

### D6 - The implementing commit type is `fix`, not `docs` or `feat`

When the human authorizes the implementing commit, its Conventional Commit type is `fix` with optional scope `spartan` or `protocol`. The change closes an underspecification that produced malformed artifacts. It adds no field, role, or verdict. `docs:` would skip the package release; `feat:` would overstate contract growth. The `protocol` birth-stamp remains a passive SemVer copied into newly created tasks only.

### D7 - The portable protocol does not depend on Bridge

No Bridge file is edited. Compatibility with a Bridge consumer is a consequence of a correct artifact, not a new protocol dependency. A reader who uses only `/spartan` loses no producer surface: the forbidden `## Review` prose had somewhere else to go, and `phase: planning` on a planning artifact was already the descriptive value.

## Acceptance Criteria

- [ ] (D1) `protocol.md` § Review and completion states that while `Verdict: PENDING`, a producer round MUST NOT add content to `## Review` and the body stays the shipped placeholder.
- [ ] (D1) That paragraph does not mention byte-identity, byte-exact matching, splice, composition, or any runtime predicate.
- [ ] (D2) `protocol.md` states that a `task_type: planning` artifact keeps `phase: planning` until the writable round that persists the plan-review verdict, and that `phase: reviewing` with `task_type: planning` is malformed.
- [ ] (D2) `protocol.md` does not say that `phase` tracks `task_type`, and does not forbid `phase: reviewing` on a `task_type: implementation` artifact.
- [ ] (D3) The only new normative paragraph is in `protocol.md` § Review and completion, immediately after the atomic-persistence sentence; `review-persistence.md` Conformance set has a `PLACEHOLDER` row; its Reviewer checklist is unchanged; `agent-skill/skills/spartan/SKILL.md` is unchanged.
- [ ] (D3) The new `PLACEHOLDER` row is not resolvable the same way as `LEGACY`: `PLACEHOLDER` applies to an identified PENDING section; `LEGACY` still forbids rewriting an old `## Review`.
- [ ] (D4) `assets/task-template.md` line under Findings is exactly `- None recorded.` The instructional sentence no longer appears in the template body and does appear in the new `protocol.md` paragraph.
- [ ] (D5) The `LEGACY` row text in `review-persistence.md` is unchanged. `git diff --name-only` does not include `spartan/tasks/0001` through `spartan/tasks/0030`.
- [ ] (D6) This artifact still names `fix` as the implementing commit type. The implementing round, when the human authorizes a commit, uses `fix(spartan):` or `fix(protocol):`.
- [ ] (D7) `git diff --name-only` for the implementation is a subset of `agent-skill/skills/spartan/references/protocol.md`, `agent-skill/skills/spartan/references/review-persistence.md`, `agent-skill/skills/spartan/assets/task-template.md`, and this artifact. The existing sentence that a handoff MAY be executed by an external tool the protocol MUST NOT depend on remains in `protocol.md`.
- [ ] Repository check: `rg -n "byte-exact|byte-identical|phase tracks" agent-skill/skills/spartan/references/protocol.md agent-skill/skills/spartan/references/review-persistence.md` reports no new hit in the added text.

## Work Completed

- 2026-09-01: planner round created this artifact and wrote the plan (D1–D7). The artifact’s own frontmatter is `phase: planning` / `task_type: planning`. Its `## Review` uses the short placeholder this plan ships in D4, so a later persistence write (or Bridge compose) can replace it; that is a newly created identified task, not a LEGACY retrofit.
- Planner execution: Cursor, Cursor Grok 4.6, vendor joint Cursor/SpaceXAI. No product files edited.

## Evidence

- `agent-skill/skills/spartan/references/protocol.md` Normative language: descriptive phase values do not create an executable state machine. § Review and completion atomic-persistence paragraph updates `phase` in the persisting round. Handoff contract Canonical envelope already requires a precise `## Next Handoff` shape. Handoff contract: a handoff MAY be executed by an external tool the protocol MUST NOT depend on.
- `agent-skill/skills/spartan/references/review-persistence.md` Writable persistence step 5 already updates `phase`; Conformance `LEGACY` already forbids rewriting an old `## Review`.
- `agent-skill/skills/spartan/assets/task-template.md` `## Review` findings line currently reads `- None recorded.` plus an instructional sentence on the same line.
- `agent-skill/skills/spartan/SKILL.md` “Read protocol.md before acting”; `review-persistence.md` is required when the round is a review or verifier, or when issuing a prompt whose permission shape matters — not at producer step 6.
- Paths named in Scope exist in this checkout: `agent-skill/skills/spartan/references/protocol.md`, `agent-skill/skills/spartan/references/review-persistence.md`, `agent-skill/skills/spartan/assets/task-template.md`.
- Next unused creation number before this file: `0030`. This artifact is `0031`.

## Review

Verdict: PENDING

Findings:

- None recorded.

## Blockers

None.

## Next Action

A planner confirm round through `/spbridge`: verify the plan is internally
consistent and Bridge-consumable (the `## Review` body is already the short
placeholder; the advisory and prompt sit in their own fences), keep the
reviewer envelope, then let the Bridge dispatch the plan review. On a passing
plan review the Bridge auto-chains the mapped implementer — every Scope path is
inside the *Automatic implementation write scope* (`agent-skill/`, `spartan/`),
no `AGENTS.md` / `config.yaml` edit, so no human implementer is required (D7).

## Next Handoff

```text
Recommended execution (human decides):
- Host: Cursor (AGENTS.md binds planner to Cursor; fresh session)
- Model and effort: cursor-grok-4.6-high-fast at high effort (fallback: any current reasoning model at high)
- Role: planner
- Handoff: HX-002
- Permission: writable
- Invocation: /spbridge, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/0031-pending-review-placeholder-and-planning-phase.md` (handoff HX-002).

Act as planner. The plan is settled through D1–D7 and its scope is fixed. Do not redesign it. Confirm it is internally consistent and Bridge-consumable, and leave the reviewer envelope in place. Update the same task file only where a correction is needed.

Then let the Bridge dispatch the plan review (reviewer.plan is now Claude Code / claude-opus-5 / high; the Cursor reviewer binding was refused reviewer_output_unconstrained).
```
