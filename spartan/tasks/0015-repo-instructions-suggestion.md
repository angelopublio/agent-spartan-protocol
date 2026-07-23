---
protocol: "0.3.1"
id: repo-instructions-suggestion
created_at: 2026-07-22
status: completed
phase: complete
task_type: review
risk: material
current_role: independent-reviewer
next_role: human-operator
updated_at: 2026-07-22
---

# Recommend, never auto-create, repository instruction files

## Objective

When a host first adopts Spartan in a repository that has no `AGENTS.md` or `CLAUDE.md`, the round recommends — exactly once, as an advisory note the human may ignore — creating an `AGENTS.md` (stack, dev/build/lint/test commands, conventions) plus a minimal `CLAUDE.md` that points to it. Spartan never creates those files by itself.

## Context

- Round lifecycle step 1 already has each host "read its applicable instructions" before acting (`references/protocol.md:53`). Repos with no `AGENTS.md`/`CLAUDE.md` give the host nothing to read, and Spartan currently says nothing about it, so cross-host handoffs start context-poor and the owner is never prompted to fix it.
- Owner intent (chat, 2026-07-22): Spartan should *surface* the gap, not close it — creating repo-root instruction files is the repo owner's territory, and auto-writing them would contradict the protocol's non-invasive promise ("The human is the orchestrator", no material action as a default consequence).
- Precedent from `spartan/tasks/0013-consumer-spartan-readme-clarity.md`: bootstrap *mechanics* (copying an asset into `spartan/`) live in `SKILL.md`; `references/protocol.md` stays the host-neutral behavioral contract. A recommendation *norm*, by contrast, is behavioral and belongs (tersely) in `protocol.md` too.
- The proposal arrived as two triggers. This planning round resolves which files change and whether trigger 2 survives.

## Scope

- Decide and record the rule's home files, the exact firing condition, the emitted form, and acceptance criteria for the implementation round. No behavior files are edited in this planning round; this task authors the plan only.
- The rule to implement next: on the round that first creates `spartan/` in a repo (equivalently, the repo's first task — observable from `spartan/tasks/` being otherwise empty) where repo root has no `AGENTS.md` and no `CLAUDE.md`, the round appends **one advisory line** recommending an `AGENTS.md` + minimal `CLAUDE.md` follow-up. The line is advisory guidance, not a second binding handoff, and does not displace the round's single next-action handoff.

## Out of Scope

- **Trigger 2 as specified is dropped** (see Decisions) — no per-round "still missing" reminder.
- Editing `spartan/README.md` or `agent-skill/assets/spartan-readme.md`: those describe the `spartan/` task namespace for readers and were deliberately kept minimal in 0013; an onboarding suggestion is a different concern and would add churn.
- Root `README.md` changes: optional documentation nicety, deferred to keep this contract change tight; may be a later doc round.
- Auto-generating the `AGENTS.md`/`CLAUDE.md` *content* — that is the human-initiated follow-up task's job, not this rule's.

## Constraints

- Recommend-only: Spartan MUST NOT create, edit, or scaffold any file outside `spartan/` as a consequence of this rule. The human decides.
- No new durable state: the "once" property MUST come from an observable repository condition (repo's first task), never from a suppression flag, marker file, or a status field added to a task artifact — invariant 6 (no hidden operational state) and the task-artifact-as-projection prohibition both forbid that.
- The recommendation is a single advisory line, not a competing two-block handoff; the round still returns exactly one binding handoff for its own next action (or a completion notice).
- All persisted content in English. Introduce no new controlled vocabulary. Keep `agent-skill/SKILL.md` below 200 lines.

## Acceptance Criteria

- [x] `agent-skill/SKILL.md` instructs: on the round that first creates `spartan/` in a target repo (repo's first task) where repo root has neither `AGENTS.md` nor `CLAUDE.md`, append one advisory recommendation to create an `AGENTS.md` (stack, dev/build/lint/test commands, conventions) plus a minimal `CLAUDE.md` pointing to it.
- [x] The instruction states the recommendation is advisory and that Spartan creates no file outside `spartan/`.
- [x] The instruction ties the "once" to the observable first-task/bootstrap moment and introduces no suppression state (no marker file, no task-artifact status field).
- [x] `references/protocol.md` carries one SHOULD-level, host-neutral sentence for the norm (sibling to the existing "SHOULD suggest a documentation round" guidance), so a host reading only `protocol.md` honors it.
- [x] The implementation records, in-task, that trigger 2 was evaluated and dropped, with rationale.
- [x] `spartan/README.md`, `agent-skill/assets/spartan-readme.md`, and root `README.md` are unchanged.
- [x] `agent-skill/SKILL.md` stays below 200 lines; `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill` pass.

## Decisions

- **Files that change: `agent-skill/SKILL.md` (operational instruction) and `agent-skill/references/protocol.md` (one SHOULD norm).** SKILL.md is where the sibling first-creation instruction (copy `spartan-readme.md`) already lives, so the recommendation sits beside it. protocol.md gets one host-neutral SHOULD sentence because a recommendation is a behavioral norm, unlike the pure asset-copy mechanic 0013 kept out of protocol.md; without it, a non-Claude host reading only protocol.md would not honor the norm. (Planner, 2026-07-22)
- **Consumer README and asset excluded**, root README deferred: keeps the change to the behavior contract only and avoids re-opening the reader-first minimalism settled in 0013. (Planner, 2026-07-22)
- **Trigger 2 dropped as specified.** "Mention the absence once per repo, without nagging on every round" requires remembering whether it was already mentioned. The only places to hold that are a repo marker (hidden operational state → violates invariant 6) or a task-artifact field (turns the task file into a cross-task status projection → forbidden by the task-artifact contract). Neither is admissible, and without such state the "no nagging" half is unenforceable. (Planner, 2026-07-22)
- **Trigger 2's legitimate intent is absorbed into trigger 1 by scoping the fire condition to "the repo's first Spartan task," not "the round that literally creates the directory."** That condition is read from repository state (`spartan/tasks/` otherwise empty), so it is stateless, fires exactly once by construction, and never nags. A repo that already created a first task before this rule shipped (e.g. an early-adopter repo) simply gets the suggestion from its owner directly rather than retroactively — an acceptable, state-free boundary. (Planner, 2026-07-22)
- **The recommendation is a single advisory line, not a second handoff.** The round's binding output stays "exactly one handoff (or completion notice)"; the instruction-file suggestion rides alongside as one sentence the human may act on independently. This preserves the handoff contract. (Planner, 2026-07-22)

## Work Completed

- Authored in Claude Code (cockpit planning round, Opus 4.8, high effort) after owner discussion on 2026-07-22.
- Read `SKILL.md`, `references/protocol.md`, `references/routing.md`, `assets/task-template.md`, `assets/spartan-readme.md`, root `README.md`, and `spartan/tasks/0013-consumer-spartan-readme-clarity.md` to fix the file split and the no-hidden-state boundary against existing precedent.
- Resolved both open questions the prompt posed: file set (SKILL.md + protocol.md; consumer README/asset out; root README deferred) and trigger 2 (dropped, intent folded into a stateless first-task condition).
- Implemented in Codex (GPT-5 Codex, high reasoning) by adding the first-task advisory instruction to `agent-skill/SKILL.md`.
- Added the host-neutral SHOULD norm to `agent-skill/references/protocol.md` beside the existing documentation-round completion guidance.
- Did not implement trigger 2: the implementation uses only the observable first-task/bootstrap condition and introduces no suppression marker, hidden state, or task-artifact status field.

## Evidence

- `ls spartan/tasks/`: existing tasks run `0001`–`0014`; next unused creation number is `0015`.
- `references/protocol.md:44,46` (invariant 4 human-initiated transfer; invariant 6 no hidden operational state) and `:80-88` (task artifact MUST NOT become a projection/ledger): the basis for rejecting any suppression-state design behind trigger 2.
- `references/protocol.md:166` ("SHOULD suggest a documentation round ... SHOULD NOT make it a ritual"): the existing SHOULD-norm pattern the new sentence mirrors.
- `spartan/tasks/0013-...md:59`: precedent that asset-copy mechanics live in `SKILL.md`, not `protocol.md` — informing the split that keeps only the *norm* (not the mechanic) in `protocol.md`.
- `wc -l agent-skill/SKILL.md`: 83 lines, below the 200-line constraint.
- `git diff -- spartan/README.md agent-skill/assets/spartan-readme.md README.md`: no output, confirming those files remain unchanged.
- `git diff --check`: passed after implementation.
- `claude plugin validate --strict .`: passed after implementation.
- `claude plugin validate --strict agent-skill`: passed after implementation.
- Review round re-ran all checks: `wc -l agent-skill/SKILL.md` → 83 (< 200); `git diff -- spartan/README.md agent-skill/assets/spartan-readme.md README.md` → empty; `git diff --check` → clean; `claude plugin validate --strict .` → passed; `claude plugin validate --strict agent-skill` → passed.
- `git diff -- agent-skill/SKILL.md agent-skill/references/protocol.md`: confirms only the two intended one-sentence additions.

## Review

Verdict: APPROVE

Reviewed in Claude Code (independent-reviewer, Opus 4.8, high effort), fresh context, a different vendor than the Codex implementation.

Findings:

- Scope is exactly as planned: only `agent-skill/SKILL.md` (one sentence appended to the intro paragraph beside the existing `spartan-readme.md` bootstrap mechanic) and `agent-skill/references/protocol.md` (one SHOULD-level sentence) changed. `spartan/README.md`, `agent-skill/assets/spartan-readme.md`, and root `README.md` are untouched (empty `git diff`).
- Wording is faithful and consistent across the two files. Both fire on "the repository's first Spartan task" with root lacking both `AGENTS.md` and `CLAUDE.md`, both frame the output as advisory, and both forbid creating files outside `spartan/`. SKILL.md additionally spells out the no-suppression-state boundary ("no marker, suppression file, or task-artifact status field"); protocol.md states it host-neutrally ("without creating those files or tracking suppression state").
- The "once" property is derived from observable repository state (first task) with no hidden operational state, satisfying invariant 6 and the task-artifact-projection prohibition. Trigger 2's drop is recorded with rationale in Decisions and Out of Scope.
- The protocol sentence is SHOULD-level and placed as a sibling to the "SHOULD suggest a documentation round" guidance, exactly as the acceptance criterion requested, so a host reading only `protocol.md` honors the norm.
- All acceptance criteria are met; no blocker remains.
- Observation (non-blocking): the protocol sentence lives in the completion-notice area, yet the rule can fire on a non-completion round that creates the first task. This placement is what the criterion explicitly asked for (sibling to the documentation-round SHOULD), so it is correct as specified; a future doc pass could relocate it if the section grows, but no change is warranted now.

## Blockers

None.

## Next Action

None. Independent review returned `APPROVE`, all acceptance criteria are satisfied, checks recorded outcomes, and no blocker remains. Task `completed`.

## Next Handoff

Completed — no binding handoff. Optional follow-up below (human decides).

The change altered durable protocol behavior (a new recommend-only norm), so a documentation round is identifiable but out of scope here: root `README.md` was deliberately deferred by this task and MAY later mention that adopting a repo suggests an `AGENTS.md`/`CLAUDE.md`. This is a suggestion only; the owner may decline or start no further round.

```text
Recommended execution (human decides):
- Host: Claude Code (cockpit/docs round; owns README prose)
- Model and effort: Opus, medium effort (routine doc wording)
- Invocation: `/spartan`, passing the prompt block below as the argument
```

```text
Create a new uniquely numbered task from `assets/task-template.md` in `spartan/tasks/`.

Act as planner. Decide whether root `README.md` should note that adopting Spartan in a repo prompts the human to create an `AGENTS.md` + minimal `CLAUDE.md`, per the norm shipped in task 0015; if yes, scope the exact wording. Do not reopen or edit `spartan/tasks/0015-repo-instructions-suggestion.md`.
Run the relevant repository checks and update the new task file.

Return only the next handoff, or a completion notice if no work remains.
```
