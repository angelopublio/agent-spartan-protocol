---
protocol_version: "0.1"
id: task-slug
created_at: YYYY-MM-DD
status: active
phase: planning
task_type: planning
risk: routine
current_role: planner
next_role: implementer
updated_at: YYYY-MM-DD
---

# Task title

Task filenames use `NNNN-slug.md`: choose the next unused four-digit number by creation order, keep it immutable, and use `created_at` for the creation date.

## Objective

State the observable outcome.

## Context

Record only the context that a new session cannot safely infer from the repository.

## Scope

- Included path, behavior, or deliverable.

## Out of Scope

- Explicitly excluded work.

## Constraints

- Technical, product, safety, language, or authorization boundary.

## Acceptance Criteria

- [ ] Observable completion condition.

## Decisions

- Decision and brief rationale.

## Work Completed

- Current relevant result, not a turn-by-turn transcript.

## Evidence

- Command or observation: outcome.

## Review

Verdict: PENDING

Findings:

- None recorded.

## Blockers

None.

## Next Action

Describe exactly one bounded action and its success condition.

## Next Handoff

```text
Recommended execution (human decides):
- Host: <Codex | Claude Code | other authenticated host, with a one-phrase reason>
- Model and effort: <one concrete model, one concrete effort level, optional fallback phrase>
- Invocation: <`$spartan` | `/spartan` | direct prompt>, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/NNNN-task-slug.md`.

Act as implementer. Perform the next bounded action and satisfy its recorded acceptance criteria.
Run the relevant repository checks and update the same task file.

Return only the next handoff, or a completion notice if no work remains.
```
