---
protocol_version: "0.1"
id: completion-notice-next-step
created_at: 2026-07-20
status: completed
phase: complete
task_type: review
risk: material
current_role: human-operator
next_role: human-operator
updated_at: 2026-07-20
---

# Completion notices must propose the next step

## Objective

A completed round never leaves the human without direction: every completion notice either recommends the next step in the standard two-block handoff shape, or states explicitly that no follow-up work is identifiable.

## Context

The Codex review round for `handoff-execution-metadata` correctly completed its task but returned only "no work remains", forcing the owner to decide the next move without guidance. The owner requested that completion notices carry the same two-block guidance as handoffs.

## Scope

- Extend the handoff contract in `agent-skill/references/protocol.md` with a completion-notice contract.
- Update `agent-skill/SKILL.md` to match.

## Out of Scope

- Any runtime, host invocation, or model routing behavior.
- Initializing Git or committing.

## Constraints

- The suggested next step must come from repository state, the roadmap (for example `docs/DOGFOODING.md`), or explicit owner direction, never invented scope.
- Suggesting a next step does not start it; the human still initiates every round.
- All persisted content remains English.

## Acceptance Criteria

- [x] protocol.md defines the completion-notice contract: completion statement plus either a two-block next-step suggestion or an explicit "no follow-up work identified" statement.
- [x] SKILL.md reflects the same rule.
- [x] Static checks pass: English-only content, no runtime artifacts, SKILL.md under 200 lines.
- [x] A fresh-context review issues an explicit verdict on the change.
- [x] Completion-suggestion prompts distinguish new-task creation from an explicitly named active-task continuation and never update the completed artifact. The template-path defect was corrected during task 0004; its independent review remains the required final confirmation.

## Decisions

- A suggested next step in a completion notice is a recommendation for a possible new task, not a continuation of the completed task; the new task file is created only when the human starts that round.
- The completion-notice "same two-block shape" means only its advisory execution envelope and pasteable prompt block. It does not carry over the incomplete-task same-file rule.

## Work Completed

- Added the completion-notice contract to protocol.md and SKILL.md.
- Completed one read-only review of the scoped protocol and skill changes; the implementation files were not modified.
- Clarified both canonical instructions: a suggested new task creates a unique artifact from the template in a human-started round, while an explicitly named active task may be referenced and updated instead. Neither path opens, reopens, or updates the completed artifact.
- Re-ran repository-local static checks after the clarification; the canonical skill and both project-local discovery links expose a readable `SKILL.md`.
- Ran an independent, read-only fresh-context review of the clarification. The three review semantics hold, but the review surfaced a concrete template-path defect (see Evidence and Review). No protocol or skill file was modified.
- Task 0004 corrected the template source to `assets/task-template.md` while restructuring the package; its independent review will also confirm this folded finding.

## Evidence

- `quick_validate.py` against the skill source and both `.agents` and `.claude` discovery links: `Skill is valid!` with exit code 0 in all three runs.
- Static scans: non-ASCII matches are limited to README tree glyphs; no unresolved initializer marker, runtime source, executable, package manifest, dependency file, or state artifact was found outside editor metadata.
- `wc -l agent-skill/SKILL.md`: 77 lines, below the preferred 200-line limit.
- Contract cross-reference: `protocol.md` requires incomplete handoffs to open and update the same task file (lines 112-119 and 135-140), while the new completion clause reuses the two-block shape for a follow-up that is explicitly not continuation of the completed task (lines 145-150). `SKILL.md` has the same unresolved overlap between lines 63-68 and 73.
- Repository-state limitation: `git status --short` reports that this directory is not a Git repository, consistent with the out-of-scope decision not to initialize Git; the review evaluated the current scoped files rather than a baseline diff.
- Post-change checks: `wc -l agent-skill/SKILL.md` returned 79; non-ASCII and forbidden runtime/artifact scans returned no matches; completion-specific assertions found the new-task, existing-active-task, advisory, and human-started rules in both canonical instructions. `quick_validate.py` is not available in this environment, so no external validator was run.
- Independent-review checks (2026-07-20): `wc -l agent-skill/SKILL.md` = 79, `agent-skill/references/protocol.md` = 211; the skill source had no non-ASCII content and no runtime or manifest artifacts; `git status` confirms this is not a Git repository, so the review evaluated the current scoped files rather than a diff.
- Template-path defect confirmed: the suggested-new-task clause formerly named a nonexistent task-directory template. The canonical template is `assets/task-template.md` in `agent-skill/`; task 0004 corrected the canonical references and awaits independent review.

## Review

Verdict: APPROVE

Accepted by the owner in a Claude Code (Opus) cockpit round; the formal independent cross-vendor review was waived by the owner (acceptance criterion explicitly waived per protocol). The template-path defect this task surfaced was corrected under task 0004.

Findings (independent-reviewer, fresh context, read-only):

- PASS - Suggested-new-task path does not reopen or update the completed task (`protocol.md`, `SKILL.md`).
- PASS - An explicitly named active task remains usable and is updated instead of the completed one.
- PASS - The two-block envelope stays advisory and human-started.
- RESOLVED - The suggested-new-task clause once named a nonexistent template source; task 0004 corrected both canonical references to `assets/task-template.md`, and the owner accepted the correction.

## Blockers

None.

## Next Action

None - task completed.

## Completion Notice

Completed: the completion-notice contract is in place in `agent-skill/references/protocol.md` and `agent-skill/SKILL.md` (a completion statement plus either a two-block suggestion or an explicit "no follow-up work" statement), with the new-task / active-task / advisory distinctions. The template-path defect this task surfaced was corrected under task 0004. Owner-accepted; no follow-up work is identified for this task.
