---
protocol_version: "0.1"
id: handoff-consistency-and-supersession
status: completed
phase: complete
task_type: review
risk: material
current_role: independent-reviewer
next_role: human-operator
created_at: 2026-07-20
updated_at: 2026-07-20
---

# Handoff consistency and task supersession

## Objective

Close the two protocol gaps that let a handoff become internally contradictory or point at another task's work, so the class of tangle observed in task 0003 cannot recur.

## Context

While closing the earlier batch, the board surfaced two real defects rooted in the protocol, not the tooling:

1. Task 0003's handoff was self-contradictory: its frontmatter `next_role` and advisory block said "implementer / implementation round" while the prompt said "Act as independent-reviewer". The "who is next" value is encoded in three places that drifted apart when one half was edited.
2. Tasks 0003 and 0004 both pointed at reviewing 0004. Task 0003's remaining work had been absorbed by 0004, but 0003 was left `active` handing off to another task's file instead of being closed.

## Scope

- `agent-skill/references/protocol.md` Handoff contract: require the three encodings of the next role (frontmatter `next_role`, advisory block role, prompt `Act as <role>`) to agree, regenerated as a unit; require a handoff to point at the current task's own continuation and forbid handing off to work a different active task tracks.
- `agent-skill/references/protocol.md` Review and completion: allow closing a task as `completed` when its work is fully absorbed by another task (supersession).
- `agent-skill/SKILL.md`: add a concise mirror of both rules.
- Restore ASCII-only content: replace non-ASCII dashes introduced by earlier rounds (0005) in `protocol.md` and `routing.md`, and the one added here in `SKILL.md`.

## Out of Scope

- Any enforcement mechanism; the rules stay advisory/normative text, not runtime checks.
- Runtime, host invocation, or model-routing behavior.
- Editing the already-completed tasks 0001-0005 to retrofit the new rules.

## Constraints

- All persisted content remains English and ASCII-only.
- `SKILL.md` stays under 200 lines.
- No new controlled values, roles, or state artifacts.

## Acceptance Criteria

- [x] `protocol.md` Handoff contract requires the frontmatter `next_role`, advisory block role, and prompt `Act as <role>` to agree and be regenerated as a unit.
- [x] `protocol.md` Handoff contract requires a handoff to reference the current task's own continuation and forbids handing off to another active task's work.
- [x] `protocol.md` Review and completion permits supersession closure, recording which task absorbed the work.
- [x] `SKILL.md` carries a concise mirror of both rules.
- [x] `agent-skill/` is ASCII-only; `SKILL.md` under 200 lines.
- [x] A fresh-context independent review, run in a host other than the authoring one, issues an explicit verdict. (APPROVE in Codex; see Review.)

## Decisions

- The next role is encoded in three places that MUST agree; when the next step changes, the whole handoff is regenerated as a unit rather than edited in halves.
- A handoff MUST point at the current task's own continuation; it MUST NOT hand off to work a different active task already tracks.
- A task MAY be closed as `completed` by supersession when another task fully absorbs its remaining work, recording which task absorbed it.
- Rules are normative in `protocol.md` and mirrored concisely in `SKILL.md`; no runtime enforcement is added.

## Work Completed

- Implemented all three protocol edits and the SKILL.md mirror in one round (Claude Code cockpit, Claude Opus).
- Restored ASCII-only content in `protocol.md`, `routing.md`, and `SKILL.md` by replacing non-ASCII dashes with hyphens.
- Completed a fresh-context, read-only independent review in Codex; no protocol-package files were modified by the review.

## Evidence

- `protocol.md` Handoff contract now contains the three-place agreement rule and the own-continuation / no-cross-task rule; Review and completion contains the supersession clause.
- `SKILL.md` "Produce the handoff" now mirrors both rules; `wc -l agent-skill/SKILL.md` = 81 (under 200).
- `grep -rlP '[^\x00-\x7F]' agent-skill/` returns no matches (ASCII-only).
- Independent-review checks: `git diff --check` passed; `LC_ALL=C rg --pcre2 -n '[^\x00-\x7F]' agent-skill` returned no matches; `file --mime-encoding` reported `us-ascii` for all five files under `agent-skill/` and this task artifact.
- Skill validation: the available `quick_validate.py` returned `Skill is valid!` with exit code 0 for `agent-skill/` and for both `.agents/skills/spartan` and `.claude/skills/spartan` discovery links.

## Review

Verdict: APPROVE

Findings (Codex independent-reviewer, fresh context, read-only):

- PASS - `protocol.md` line 147 requires the frontmatter `next_role`, the advisory block's role and reason, and the prompt's `Act as <role>` value to agree, and requires the whole handoff to be regenerated as a unit.
- PASS - `protocol.md` lines 116 and 149 require the handoff to reference the current task's own artifact and continuation and prohibit handing off work tracked by a different active task.
- PASS - `protocol.md` line 110 permits `completed` supersession closure, requires the absorbing task to be recorded, and assigns the folded acceptance criteria to that task.
- PASS - `SKILL.md` line 73 concisely mirrors role-unit consistency, own-task continuation, and supersession closure with the absorbing task noted; its completion-suggestion exception remains correctly limited to completed tasks at line 77.
- PASS - Static checks and the available skill validator passed; `agent-skill/` is ASCII-only and `SKILL.md` is 81 lines, below 200.
- No blocking or material finding was identified. The duplicate own-task wording in `protocol.md` lines 116 and 149 is acceptable normative emphasis.

## Blockers

None.

## Next Action

None. All acceptance criteria are satisfied, relevant checks have recorded outcomes, the independent review verdict is APPROVE, and no blocker remains.

## Completion Notice

Completed: the handoff-consistency and supersession additions are independently APPROVED. `protocol.md` requires the three next-role encodings to agree as a regenerated unit, keeps handoffs on their own task rather than another active task, and permits recorded supersession closure; `SKILL.md` mirrors the rules concisely. Diff hygiene, ASCII, size, and skill-validation checks all pass. No follow-up work is identifiable for this task.
