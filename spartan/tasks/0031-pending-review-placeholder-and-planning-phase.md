---
protocol: "1.1.0" # x-release-please-version
id: pending-review-placeholder-and-planning-phase
created_at: 2026-09-01
status: completed
phase: done
task_type: implementation
risk: material
current_role: human-operator
next_role: none
updated_at: 2026-09-01
handoff_id: HX-007
next_handoff_id: none
---

# Keep PENDING Review as the shipped placeholder; keep planning phase until verdict persistence

## Objective

A producer round that finishes a planning artifact leaves `## Review` as the shipped `Verdict: PENDING` placeholder and leaves `phase: planning`. The protocol states that ownership in `protocol.md`, with one conformance row and a template that no longer embeds instructional prose in the findings line. A reader who never heard of Spartan Bridge can follow the same artifact by hand.

## Context

Writable persistence already assigns who writes `## Review` and who updates `phase`: the explicitly authorized writable round that persists an adopted verdict. Canonical envelope already requires a precise `## Next Handoff` shape while a proposal is outstanding. `## Review` has no equivalent producer-side rule, so a planning round can add prose under `Verdict: PENDING` and set `phase: reviewing` while `task_type` is still `planning`.

Those two edits are independently wrong as handoff truth: producer notes belong in Work Completed, Evidence, Blockers, or Next Action, and `phase` is descriptive rather than a state machine (`protocol.md` Normative language). They also happen to be the shapes a Bridge `review` dispatch refuses (`task_invalid` on the planning/`reviewing` pair; `composition_failed` when `## Review` is not the short placeholder). The Bridge already fails closed; this task does not change it. The hole is that the portable skill does not tell the producer to emit a well-formed artifact.

The shipped template is part of the hole. `assets/task-template.md` currently appends one instructional sentence to the findings line after `- None recorded.` (the verbatim current line is quoted in Evidence). A task copied from the template is born with a `## Review` body that a persistence write (and a Bridge compose) cannot treat as the placeholder.

Bridge repository task `0057` already deferred the producer-skill edit to this package: the companion that keeps the artifact canonical coming out of the producer round is owned by the protocol skill, not the Bridge.

## Scope

- `agent-skill/skills/spartan/references/protocol.md` § Review and completion: one new normative paragraph immediately after the atomic-persistence sentence (the paragraph that currently ends by pointing at `review-persistence.md`). That single paragraph carries both the D1 `## Review` placeholder rule and the D2 `phase` rule; its exact wording is fixed in D4.
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

While a task the producer created or is carrying shows `Verdict: PENDING` in `## Review`, no producer round adds content there. The body stays the shipped placeholder, which the new paragraph states literally: a `Verdict: PENDING` line, a `Findings:` line, and a single `- None recorded.` bullet, and nothing else. Producer prose stays in Work Completed, Evidence, Blockers, and Next Action. The protocol states this the same way Canonical envelope states `## Next Handoff`: a required section shape, not a byte-identity predicate for a runtime splice. The rule reaches only a PENDING section the current round is responsible for; it requires no change to any pre-existing PENDING body, and a legacy identifier-less section that already carries extra prose is out of its reach (D5). An external tool MAY refuse a non-placeholder body; that refusal is outside this protocol.

### D2 - A planning artifact keeps `phase: planning` until plan-review persistence; `phase` does not track `task_type`

A `task_type: planning` artifact keeps `phase: planning` until the writable round that persists the plan-review verdict. Setting `phase: reviewing` while `task_type` is still `planning` is malformed. An implementer MAY set `phase: reviewing` on a `task_type: implementation` artifact before implementation review. The protocol adds no pairing table and no “producer MUST NOT advance `phase`” rule that would forbid that implementation declaration. `phase` remains descriptive.

### D3 - Load-bearing paragraph in `protocol.md`; one conformance row; no third copy

The contract is a single new paragraph in `protocol.md` § Review and completion, immediately after the atomic-persistence sentence. That one paragraph — its exact text fixed in D4 — carries both the D1 `## Review` placeholder rule and the D2 `phase` rule; it is the only new normative paragraph in the change. `review-persistence.md` gains exactly one conformance row, `PLACEHOLDER`, mapping an identified PENDING section the current round is responsible for to “producer writes nothing in `## Review`; body remains the shipped placeholder.” The Reviewer checklist is unchanged. `SKILL.md` is unchanged. The existing `LEGACY` row is unchanged. Neither the new paragraph nor the `PLACEHOLDER` row names Spartan Bridge, dispatch, compose, or any reason code: the contract is stated in protocol vocabulary only, so D7 holds by construction and a reader with no Bridge installed loses nothing.

### D4 - The shipped template carries the short placeholder

`assets/task-template.md` gets exactly one edit: the single findings line under `## Review` becomes exactly `- None recorded.` — the trailing instructional clause is deleted, leaving no other change to the file. The template's `## Review` then carries exactly three content lines and no HTML comment markers: `Verdict: PENDING`, `Findings:`, `- None recorded.` (comment markers appear only after an external tool writes a marked region, which is out of this protocol's scope).

The guidance the deleted clause carried is not relocated verbatim; it is restated in the single new `protocol.md` paragraph in that paragraph's own vocabulary, so it does not import the words `compose`, `splice`, or byte/exact matching that AC (D1) forbids. That one paragraph carries the D1 rule and the D2 rule together. Its exact text is:

> A producer round MUST NOT write into a `## Review` section it is responsible for while that section shows `Verdict: PENDING`; the section is filled only by the explicitly authorized writable round that persists an adopted verdict, and until then its body stays the shipped placeholder — the three lines `Verdict: PENDING`, `Findings:`, and `- None recorded.` and nothing else. This requires no change to any pre-existing PENDING body. Relatedly, `phase` is descriptive and does not track `task_type`: a `task_type: planning` artifact keeps `phase: planning` until the writable round that persists the plan-review verdict, so `phase: reviewing` while `task_type` is still `planning` is malformed; an implementer MAY set `phase: reviewing` on a `task_type: implementation` artifact before its review.

The placeholder's three lines are named literally in that paragraph, so `protocol.md` and `assets/task-template.md` agree on the same three strings without the paragraph reproducing them as a fenced block. New tasks are then born with a `## Review` body a writable persistence round can replace. Existing tasks are not retrofitted (D5).

### D5 - LEGACY `## Review` is not rewritten

No round rewrites or re-attributes an old `## Review`. Upgrading a legacy artifact’s handoff identifier fields does not canonicalize `## Review`. Identifier-less PENDING sections that already carry extra prose stay as they are.

### D6 - The implementing commit type is `fix`, not `docs` or `feat`

When the human authorizes the implementing commit, its Conventional Commit type is `fix` with optional scope `spartan` or `protocol`. The change closes an underspecification that produced malformed artifacts. It adds no field, role, or verdict. `docs:` would skip the package release; `feat:` would overstate contract growth. The `protocol` birth-stamp remains a passive SemVer copied into newly created tasks only.

### D7 - The portable protocol does not depend on Bridge

No Bridge file is edited. Compatibility with a Bridge consumer is a consequence of a correct artifact, not a new protocol dependency. A reader who uses only `/spartan` loses no producer surface: the forbidden `## Review` prose had somewhere else to go, and `phase: planning` on a planning artifact was already the descriptive value.

## Acceptance Criteria

- [x] (D1) `protocol.md` § Review and completion states that while `Verdict: PENDING`, a producer round MUST NOT add content to `## Review` and the body stays the shipped placeholder.
- [x] (D1) That paragraph does not contain any of `byte-identity`, `byte-identical`, `byte-exact`, `byte-exact matching`, `splice`, `compose`, `composition`, or `runtime predicate`, checked against the added lines of the diff (`git diff -U0 -- agent-skill/skills/spartan/references/protocol.md | rg '^\+' | rg -i 'byte-identity|byte-identical|byte-exact|splice|compos|runtime predicate'` returns nothing).
- [x] (D1) That paragraph states the placeholder body literally — a `Verdict: PENDING` line, a `Findings:` line, and one `- None recorded.` bullet — so a reader can reconstruct it from `protocol.md` alone without opening the template.
- [x] (D1) That paragraph scopes the rule to a PENDING section the current round is responsible for and states that it requires no change to any existing PENDING body; it does not declare pre-existing PENDING artifacts non-conforming (no conflict with D5).
- [x] (D2) `protocol.md` states that a `task_type: planning` artifact keeps `phase: planning` until the writable round that persists the plan-review verdict, and that `phase: reviewing` with `task_type: planning` is malformed.
- [x] (D2) `protocol.md` does not say that `phase` tracks `task_type`, and does not forbid `phase: reviewing` on a `task_type: implementation` artifact.
- [x] (D3) Exactly one new normative paragraph is added to `protocol.md`, in § Review and completion immediately after the atomic-persistence sentence, and it is the D4 fixed text carrying both the D1 and D2 rules (no second new paragraph); `review-persistence.md` Conformance set has a `PLACEHOLDER` row; its Reviewer checklist is unchanged; `agent-skill/skills/spartan/SKILL.md` is unchanged.
- [x] (D3) The new `PLACEHOLDER` row is not resolvable the same way as `LEGACY`: `PLACEHOLDER` applies to an identified PENDING section; `LEGACY` still forbids rewriting an old `## Review`.
- [x] (D4) `assets/task-template.md` `## Review` block is exactly three content lines — `Verdict: PENDING`, `Findings:`, and `- None recorded.` — with no trailing clause on the bullet, and `git diff --stat` shows `assets/task-template.md` changed by that one line only.
- [x] (D4) The template's former trailing clause does not appear verbatim anywhere in `protocol.md`; the D1/D3 paragraph instead states the producer-untouchable rule in its own vocabulary and satisfies the (D1) string check above.
- [x] (D4) Semantic identity, not line-for-line: the three placeholder strings the new `protocol.md` paragraph names (`Verdict: PENDING`, `Findings:`, `- None recorded.`) are exactly the three content lines of `assets/task-template.md`'s `## Review` block after the edit.
- [x] (D7) By reading: neither the new `protocol.md` paragraph nor the `PLACEHOLDER` row names Spartan Bridge, dispatch, an adapter, or any reason-code token (e.g. `task_invalid`, `composition_failed`, `reviewer_output_unconstrained`). Mechanical aid only, not the whole check: `git diff -U0 -- agent-skill/skills/spartan/references/protocol.md agent-skill/skills/spartan/references/review-persistence.md | rg '^\+' | rg -i 'bridge|dispatch|adapter|_[a-z]+ed\b|_unconstrained|_invalid'` returns nothing.
- [x] (D5) The `LEGACY` row text in `review-persistence.md` is unchanged. `git diff --name-only` does not include `spartan/tasks/0001` through `spartan/tasks/0030`.
- [x] (D6) This artifact still names `fix` as the implementing commit type. The implementing round, when the human authorizes a commit, uses `fix(spartan):` or `fix(protocol):`.
- [x] (D7) `git diff --name-only` for the implementation is a subset of `agent-skill/skills/spartan/references/protocol.md`, `agent-skill/skills/spartan/references/review-persistence.md`, `agent-skill/skills/spartan/assets/task-template.md`, and this artifact. The existing sentence that a handoff MAY be executed by an external tool the protocol MUST NOT depend on remains in `protocol.md`.
- [x] Repository check: `git diff -U0 -- agent-skill/skills/spartan/references/protocol.md agent-skill/skills/spartan/references/review-persistence.md | rg '^\+' | rg -i 'byte-identity|byte-identical|byte-exact|splice|compos|runtime predicate|phase tracks|phase track'` returns no line — the check is over added lines only, so it needs no separate baseline.

## Work Completed

- 2026-09-01: planner round created this artifact and wrote the plan (D1–D7). The artifact’s own frontmatter is `phase: planning` / `task_type: planning`. Its `## Review` uses the short placeholder this plan ships in D4, so a later persistence write (or Bridge compose) can replace it; that is a newly created identified task, not a LEGACY retrofit.
- Planner execution: Cursor, Cursor Grok 4.6, vendor joint Cursor/SpaceXAI. No product files edited.
- 2026-09-01: planner confirm round, human-started through `/spbridge` (handoff HX-002) and authorized by that invocation to "update the same task file only where a correction is needed"; executed on Claude Code / claude-sonnet-5 in a session separate from the later plan review. It adopted `HX-002` and verified the plan is internally consistent and Bridge-consumable. Correction applied: `next_role` was `planner` with a planner-targeted envelope; set `next_role: reviewer` and regenerated `## Next Handoff` as the read-only plan-review envelope (`HX-003`) so the Bridge could dispatch the review. `reviewer.plan` is bound to Claude Code / claude-opus-5 / high (`spartan-bridge doctor`: `binding reviewer.plan: adapter available`); the earlier Cursor reviewer binding was refused `reviewer_output_unconstrained`. No product files edited. Open item for the human: AGENTS.md maps planner to Cursor, so confirm this Claude Code confirm round was the intended host or re-run the confirm on Cursor.
- 2026-09-01: plan review (Bridge-dispatched, Claude Code / claude-opus-5 / high) returned `CHANGES_REQUESTED` (run `run-739bd748`, cycle 1/3); verdict and seven findings persisted in `## Review`.
- 2026-09-01: planner revision round (Claude Code / claude-sonnet-5, human-started through `/spbridge`) addressed the findings without changing scope: resolved the one-vs-two-sentence contradiction in Context and quoted the template line verbatim in Evidence (`TEMPLATE_SENTENCE_COUNT`); D4 now states the guidance is restated in D1 vocabulary, not moved verbatim, and quotes the intended `protocol.md` wording (`MOVED_PROSE_VS_AC_D1`); D1 and a new AC pin the placeholder body literally (`PLACEHOLDER_UNDEFINED`); D1, D3, and a new AC scope the rule to a PENDING section the current round owns and forbid any change to an existing PENDING body (`LEGACY_CARVEOUT_UNSCOPED`); the D1 string check and the repository check are now expressed over added diff lines with the full forbidden vocabulary (`VERIFICATION_UNDERCOVERS_D1`); D3 plus a new AC forbid any Bridge concept in the added text (`BRIDGE_MENTION_UNCHECKED`); the off-binding note is recorded above for the human (`PLANNER_ROUND_OFF_BINDING`). Regenerated `## Next Handoff` as the read-only plan-review envelope `HX-004`. No product files edited.
- 2026-09-01: plan review cycle 2 (Bridge-dispatched, Claude Code / claude-opus-5 / high) returned `CHANGES_REQUESTED` (run `run-3e589588`, cycle 2/3); verdict and five findings persisted in `## Review`.
- 2026-09-01: planner revision round 2 (Claude Code / claude-sonnet-5, human-started through `/spbridge`), scope unchanged: the single new `protocol.md` paragraph now carries both the D1 placeholder rule and the D2 `phase` rule, its exact text is fixed in D4, and Scope / D3 / AC (D3) say so — resolving `D2_RULE_HAS_NO_PARAGRAPH`; AC (D4) line-for-line identity replaced with semantic identity on the three named placeholder strings, and D4 states the template `## Review` carries no comment markers — resolving `PLACEHOLDER_IDENTITY_UNCHECKABLE`; Evidence creation-number line reworded to "highest existing / next unused" — resolving `CREATION_NUMBER_INCONSISTENT`; AC (D7) reason-code check demoted to a read-by-a-human criterion with the grep as a mechanical aid only — resolving `D7_REASON_CODE_CHECK_PARTIAL`. `PLANNER_ROUND_OFF_BINDING` remains an open item for the human (see the confirm-round entry above); this revision round is likewise Claude Code / claude-sonnet-5, human-started. Regenerated `## Next Handoff` as `HX-005`. No product files edited.
- 2026-09-01: plan review cycle 3 (Bridge-dispatched, `run-a468e0d2`) returned `human_required` / `review_human_required` at the 3-cycle ceiling; `task_write_state: skipped_human_gate`, so no verdict was written — `## Review` still shows the cycle-2 `CHANGES_REQUESTED` region.
- 2026-09-01: **owner override — plan approved, implementation authorized.** Across three plan-review cycles no finding contested the substance (D1–D7); the twelve findings were all prose precision, internal AC consistency (the two genuine ones — `D2_RULE_HAS_NO_PARAGRAPH`, `PLACEHOLDER_IDENTITY_UNCHECKABLE` — resolved in revision round 2), or the process/host question (`PLANNER_ROUND_OFF_BINDING`). A cross-vendor second opinion (Grok via Cursor, 2026-09-01) confirmed: substance sound and implementable as-is, the two AC contradictions resolved, the D4 paragraph fit for `protocol.md`, no remaining content blocker — verdict "implement now, owner override". The owner accepts D1–D7 and authorizes the implementation round despite cycle 3 not writing a verdict. Envelope regenerated as `HX-006` for the mapped `implementer` (Cursor), human-started (not a Bridge auto-chain — there is no persisted `reviewer.plan: pass`). `PLANNER_ROUND_OFF_BINDING` is accepted: all producer rounds were human-started via `/spbridge`, the human is the authority, and this next round returns to the bound implementer host.
- 2026-09-01: implementer round (Cursor / cursor-grok-4.6-high-fast, human-started via `/spbridge`, handoff HX-006). Made exactly the three Scope edits per D1–D7: inserted the D4 paragraph in `protocol.md` § Review and completion; added the `PLACEHOLDER` conformance row in `review-persistence.md`; shortened the template `## Review` findings line to `- None recorded.`. Ran all acceptance-criteria checks that write nothing; all passed. Adopted `HX-006` into `handoff_id`; set `task_type: implementation`, `phase: reviewing`, `current_role: implementer`, `next_role: reviewer`; reset `## Review` to the shipped PENDING placeholder; regenerated `## Next Handoff` as the read-only `reviewer.implementation` envelope (`HX-007`). No commit (human authorizes `fix(spartan):` after implementation review passes).
- 2026-09-01: implementation review **APPROVED**, Bridge-dispatched `reviewer.implementation` (`run-95ed1d3d-511a-4cf2-99fc-4b322a0f9d28`, `exec-5763621e`, Claude Code / claude-opus-5 / high, vendor Anthropic; read-only, no findings), cycle 1/3. The runtime wrote the `## Review` implementation region and set `next_role: human-operator`, `handoff_id: HX-007`. Two earlier dispatch attempts failed on machine-local wrapper bugs, not the code: `run-dd3da641` `capability_denied` (wrapper anchored `resolve-profile` to a relocated `$HOME`) and `run-8f32bff5` `adapter_error` in `collect` ("Not logged in" — `claude -p` reads login from `$HOME`, not `CLAUDE_CONFIG_DIR`). Both fixed in `agent-spartan-protocol-bridge` (`eb9e96c`, `5730147`: wrapper restores `HOME`, adapters forward `AGENT_PROFILES_REAL_HOME`); this pass ran from the same Cursor session once the wrapper was corrected.
- 2026-09-01: **task completed** (human-operator). Plan owner-approved (three plan-review cycles, all findings prose/consistency, cross-vendor Grok second opinion); implementation is the three Scope edits; implementation review APPROVED. Human authorizes the `fix(spartan):` commit.

## Evidence

- `agent-skill/skills/spartan/references/protocol.md` Normative language: descriptive phase values do not create an executable state machine. § Review and completion atomic-persistence paragraph updates `phase` in the persisting round. Handoff contract Canonical envelope already requires a precise `## Next Handoff` shape. Handoff contract: a handoff MAY be executed by an external tool the protocol MUST NOT depend on.
- `agent-skill/skills/spartan/references/review-persistence.md` Writable persistence step 5 already updates `phase`; Conformance `LEGACY` already forbids rewriting an old `## Review`.
- `agent-skill/skills/spartan/assets/task-template.md` `## Review` findings line currently reads, verbatim: `- None recorded. A writable round persists an adopted verdict here; a technically read-only review returns its verdict in the response and writes nothing.` D4 deletes everything after `- None recorded.` on that line. That is the file's only edit.
- `agent-skill/skills/spartan/SKILL.md` “Read protocol.md before acting”; `review-persistence.md` is required when the round is a review or verifier, or when issuing a prompt whose permission shape matters — not at producer step 6.
- Paths named in Scope exist in this checkout: `agent-skill/skills/spartan/references/protocol.md`, `agent-skill/skills/spartan/references/review-persistence.md`, `agent-skill/skills/spartan/assets/task-template.md`.
- Highest existing task number is `0030` (Out of Scope lists `0001`–`0030`); this artifact takes the next unused number, `0031`.
- Implementation checks (2026-09-01, write nothing): `git diff -U0 -- agent-skill/skills/spartan/references/protocol.md | rg '^\+' | rg -i 'byte-identity|byte-identical|byte-exact|splice|compos|runtime predicate'`: no matches. `git diff -U0 -- agent-skill/skills/spartan/references/protocol.md agent-skill/skills/spartan/references/review-persistence.md | rg '^\+' | rg -i 'bridge|dispatch|adapter|_[a-z]+ed\b|_unconstrained|_invalid'`: no matches. Combined repository check over added lines (`phase tracks`, etc.): no matches. `git diff --stat -- agent-skill/skills/spartan/assets/task-template.md`: 1 file, 1 insertion, 1 deletion. `rg -F 'A writable round persists an adopted verdict here' agent-skill/skills/spartan/references/protocol.md`: no match. `LEGACY` row diff: unchanged. `git diff --name-only`: only the three Scope files plus this artifact; no `0001`–`0030` retrofit. `wc -l agent-skill/skills/spartan/SKILL.md`: 93 (unchanged). External-tool independence sentence remains at `protocol.md` line 286.

## Review

<!-- spartan-bridge:review:implementation:begin -->
Verdict: APPROVED

Findings:

- None recorded.

Bridge run: run_id=run-95ed1d3d-511a-4cf2-99fc-4b322a0f9d28 execution_id=exec-5763621e-6d45-4253-a71e-b26dbae82ce6 review_kind=implementation verdict=pass reason_code=review_passed host=claude launcher=claude-plan-reviewer-v1 model=claude-opus-5 effort=high model_observed=declared_unobserved policy_digest=sha256:e363264f72a848d870898b8d1d1abe453a4f1e519f622f4415d9531d867023f6 task_hash=sha256:9ae75e26896485fcbb927fdf1b979de3707af93672d58d99a9b33bcfb16ea57d agents_hash=sha256:1b634e55c5355f5c54b2c61fc084c475e816a3e5edc59a2f12516e93cfc9dc42 timestamp=2026-09-01T14:43:32.874Z
<!-- spartan-bridge:review:implementation:end -->

## Blockers

None.

## Next Action

None — task completed. Plan review (3 cycles, owner override) and
implementation review (`run-95ed1d3d`, APPROVED) both ran through the Bridge.
The `fix(spartan):` commit carries the three Scope edits and this artifact.

## Next Handoff

No outstanding handoff. Task completed on the Bridge implementation-review pass
and human-operator sign-off.
