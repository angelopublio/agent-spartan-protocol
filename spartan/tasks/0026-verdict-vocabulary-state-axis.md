---
protocol: "0.6.1" # x-release-please-version
id: verdict-vocabulary-state-axis
created_at: 2026-08-17
status: completed
phase: complete
task_type: implementation
risk: material
current_role: reviewer
next_role: none
updated_at: 2026-08-17
handoff_id: HX-001
next_handoff_id: none
---

# Align review-verdict vocabulary on a resulting-state axis

## Objective

The published review-verdict controlled values name a resulting state, not the reviewer's action: `PENDING` and `BLOCKED` stay as they are; `APPROVE` becomes `APPROVED` and `CHANGES` becomes `CHANGES_REQUESTED`. Existing `spartan/tasks/` artifacts are not rewritten.

## Context

A verdict names the state that remains after the review, which is why `PENDING` and `BLOCKED` already fit and the two action-form tokens did not.

Note for the human, not acted on this round: the companion Bridge project pins the protocol version with strict equality and also writes the old verdict literals into task artifacts. Releasing this change makes every newly created task unparseable there until that project is updated in the same wave.

The creating prompt carried no handoff identifier. This artifact did not exist yet, so no compare-before-change mismatch applied.

## Scope

- `agent-skill/skills/spartan/SKILL.md` (the "Use only" verdict sentence).
- `agent-skill/skills/spartan/references/protocol.md` (controlled-values list, review-round verdict sentence, completion-criteria item, plus one compatibility note at the controlled-values definition).
- `docs/DOGFOODING.md` (the "Claude records" sentence and the "If the verdict is" sentence).

## Out of Scope

- Rewriting any existing file under `spartan/tasks/`. Those artifacts record what past rounds actually wrote.
- Editing `agent-skill/skills/spartan/assets/task-template.md`, which already carries only `Verdict: PENDING`.
- Commit, tag, push, pull request, or any other external action.
- Updating the companion Bridge project.

## Constraints

- Change every normative occurrence of the two renamed values and nothing else.
- `RELEASING.md` forbids rewriting existing `spartan/tasks/` files.
- Repository content stays English.
- No commit, tag, push, or pull request in this round.

## Acceptance Criteria

- [x] The six named normative locations use `APPROVED` and `CHANGES_REQUESTED` in place of `APPROVE` and `CHANGES`.
- [x] `references/protocol.md` states that pre-this-version artifacts using `APPROVE` and `CHANGES` remain valid historical record and that a reader MUST accept both spellings.
- [x] No existing file under `spartan/tasks/` was modified.
- [x] The Bridge downstream consequence is recorded for the human and was not acted on.
- [x] A reviewer records an explicit `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED` verdict on this diff.

## Decisions

- Use `CHANGES_REQUESTED` rather than a shorter form. `CHANGES` alone does not say who requested what, and the runtime verdict in the companion Bridge project is already `changes_requested`, so the longer name removes a lossy translation rather than adding length for its own sake.
- This is a breaking change to a published controlled vocabulary. The commit MUST use `feat!:` or a `BREAKING CHANGE:` footer, which under `RELEASING.md` produces a major release rather than a minor one.

## Work Completed

- Created this artifact as `spartan/tasks/0026-verdict-vocabulary-state-axis.md`.
- Replaced the two action-form tokens in all six named locations. Added the compatibility note immediately after the controlled-values list in `references/protocol.md`. Left `assets/task-template.md` and every existing `spartan/tasks/` file untouched.
- Recorded the Bridge pin-and-literal coupling as a human note; did not change that project.
- This round ran in Cursor on Grok 4.6 (high effort; vendor attribution joint Cursor/SpaceXAI).

## Evidence

- Named locations after the edit: SKILL.md "Use only `PENDING`, `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED`"; protocol.md verdict list `PENDING`, `APPROVED`, `CHANGES_REQUESTED`, `BLOCKED`; protocol.md review-round sentence `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED`; protocol.md completion item `every required review is \`APPROVED\``; DOGFOODING.md "Claude records `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED`"; DOGFOODING.md "If the verdict is `CHANGES_REQUESTED`".
- Compatibility note at protocol.md immediately after the verdict list: artifacts created before this version use `APPROVE` and `CHANGES`; they remain valid as historical record; a reader MUST accept both spellings.
- `git diff --stat` on the three intended files: `SKILL.md` 2 +-; `protocol.md` 8 +++++---; `DOGFOODING.md` 4 ++--. `git diff --name-only -- spartan/tasks/`: empty (existing task files untouched; this artifact is new).
- Repo-wide search for `` `APPROVE` `` and `` `CHANGES` ``: the only non-`spartan/tasks/` hit is the new compatibility note. Remaining `APPROVE` / `CHANGES` hits are inside `spartan/tasks/`. Template still reads `Verdict: PENDING`.
- `wc -l agent-skill/skills/spartan/SKILL.md`: 87. `git diff --check`: clean.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both `✔ Validation passed` (claude 2.1.233).

Review round (read-only, 2026-08-17), re-run independently of the implementer's record:

- `git diff --name-only`: four files, not three. Three are the intended scope; the fourth is `AGENTS.md`, whose 14 added lines are an unrelated "Agent hosts" binding table (see Finding 1).
- `git diff --name-only -- spartan/tasks/`: empty. `git status --short` shows `0026` as `??` (new, so not a modification of an existing artifact).
- Word-boundary search for `APPROVE` and `CHANGES` (excluding the new spellings) across `*.md`: outside `spartan/tasks/`, the single hit is the new compatibility note at `references/protocol.md:78`. Every other hit is historical text inside `spartan/tasks/`.
- Search for `APPROVED` / `CHANGES_REQUESTED` outside `spartan/tasks/`: exactly the six named locations (`SKILL.md:47`; `protocol.md:76,181,191`; `DOGFOODING.md:39,43`).
- Search for `verdict` across the package, `docs/`, and root: no further normative occurrence was missed. `assets/task-template.md:58` still reads `Verdict: PENDING`; `assets/spartan-readme.md` and root `README.md` name no verdict token.
- `git diff --check`: clean. `wc -l agent-skill/skills/spartan/SKILL.md`: 87 (under the 200-line guidance). `LC_ALL=C grep -rnP '[^\x00-\x7F]'` over `agent-skill/` and `docs/DOGFOODING.md`: no matches.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both "Validation passed" (claude 2.1.233), reproducing the implementer's result.

## Review

Verdict: APPROVED

Reviewed in Claude Code (Claude Opus 5) on 2026-08-17, read-only, against the implementing round run in Cursor on Grok 4.6. The human ran this round in Claude Code rather than the recommended Cursor/Composer 2.5; the substitution strengthens rather than weakens independence, because the reviewing vendor (Anthropic) differs from the implementing attribution (joint Cursor/SpaceXAI), where the recommendation would have kept both rounds in Cursor.

Findings:

- Finding 1 (non-blocking, commit hygiene; actionable by a later round). The working tree carries a fourth modified file outside this task's scope: `AGENTS.md` gains a 14-line "Agent hosts" section (binding table plus the `personal` client-context alias paragraph and a context-separation note). No active or completed task in `spartan/tasks/` claims it, and the implementer's evidence reports only "the three intended files", so it is unowned working-tree state rather than scope drift by this round. It does not affect any acceptance criterion here, but this task's Decisions require the verdict rename to land as `feat!:` (a major release under `RELEASING.md`). A whole-tree `git commit -a` would sweep the unreviewed `AGENTS.md` section into that breaking release. The commit for this change must be path-scoped to `agent-skill/skills/spartan/SKILL.md`, `agent-skill/skills/spartan/references/protocol.md`, `docs/DOGFOODING.md`, and this artifact; the `AGENTS.md` section needs its own task and its own commit.
- Finding 2 (non-blocking, second-order to Finding 1). The prior handoff's advisory reason, "this repository binds implementation review to Cursor", derives from that same uncommitted `AGENTS.md` table. Routing recommendations are advisory and the human overrode this one, so nothing here is wrong; recorded so a later reader knows the binding was not committed repository instruction at the time it was cited.
- Finding 3 (observation, no change recommended). The compatibility note says "before this version" without naming a version, and `references/protocol.md` embeds no version literal anywhere. Naming the release would require the note to be corrected on every version bump, which the passive birth-stamp design deliberately avoids; the task frontmatter `protocol` field already dates each artifact. Leaving the wording as-is is the better trade.
- Checked and correct, not a finding: `SKILL.md` states only the writing rule ("Use only ...") while the reader-side both-spellings rule lives in `references/protocol.md`, which `SKILL.md` requires be read before acting. The two do not contradict, and duplicating the note into `SKILL.md` would add length without adding a rule.
- No contract growth. The diff adds no frontmatter field, role, phase, status, or handoff-contract clause; it renames two members of one existing controlled vocabulary and adds one compatibility sentence.

## Blockers

None.

## Next Action

None. All five acceptance criteria are satisfied, the relevant checks have recorded outcomes, the review verdict is `APPROVED`, and no blocker remains. The three package files and this artifact are uncommitted in the working tree; committing is the owner's explicit call under `RELEASING.md`, not a completion requirement, and Finding 1 states the path scope that commit must use.

## Next Handoff

No outstanding handoff. This task is complete.

Non-binding suggestion for a separate new task, from Finding 1: the unowned `AGENTS.md` "Agent hosts" section needs an artifact of its own before it is committed, so that the `feat!:` release for this task stays path-scoped.

```text
Recommended execution (human decides):
- Host: Claude Code (cockpit round: authoring a new task artifact and framing the decision with the human)
- Model and effort: Claude Opus, high effort (fallback: Sonnet 5 if Opus is unavailable)
- Role: planner
- Invocation: `/spartan`, passing the prompt block below as the argument
```

```text
Create a new uniquely numbered task from `assets/task-template.md` in `spartan/tasks/`.

Act as planner. The working tree carries an uncommitted "Agent hosts" section in `AGENTS.md` (binding table, the `personal` client-context alias paragraph, and a context-separation note) that no task owns. Define that change's scope, constraints, and acceptance criteria, and record whether it should become committed repository instruction as written. Success is a new artifact that a later round can act on without conversation history, with the `AGENTS.md` change kept out of the verdict-vocabulary release commit. Do not reopen or update `spartan/tasks/0026-verdict-vocabulary-state-axis.md`, and do not commit in this round.
Run the relevant repository checks and update the new task file.

Return only the next handoff, or a completion notice if no work remains.
```
