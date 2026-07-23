---
protocol: "0.3.1"
id: readme-note-repo-instructions-norm
created_at: 2026-07-22
status: completed
phase: complete
task_type: review
risk: routine
current_role: reviewer
next_role: human-operator
updated_at: 2026-07-22
---

# Note the AGENTS.md + CLAUDE.md adoption suggestion in root README

## Objective

Root `README.md` tells an adopter, once and in the right section, that the first Spartan task in a repository lacking `AGENTS.md`/`CLAUDE.md` emits a one-time advisory line suggesting they create those instruction files — framed as recommend-only, so the note reinforces (not weakens) the non-invasive promise.

## Context

- The recommend-only norm shipped in task 0015 (`agent-skill/SKILL.md` intro paragraph; `agent-skill/references/protocol.md:168`). Task 0015 deliberately deferred the root `README.md` mention to keep that contract change tight, and closed by noting the README "MAY later mention" it. This task is that deferred doc round. Do not reopen or edit 0015.
- The behavior is now visible to any adopter: on a fresh repo's first task, the round appends one advisory line about `AGENTS.md` + minimal `CLAUDE.md`. Without a README note, an adopter meets that line with no expectation set and could read it as Spartan trying to touch files outside `spartan/`.

## Scope

- Planning only: decide whether the README should carry the note (decided: yes), pick its home section, and fix the exact wording and placement for the implementation round. No files are edited in this round.
- Target file for the implementation round: root `README.md` only.

## Out of Scope

- Editing `agent-skill/SKILL.md` or `agent-skill/references/protocol.md`: the norm already ships there (task 0015); this is a README-only doc note.
- Editing `spartan/README.md` or `agent-skill/assets/spartan-readme.md`: those describe the `spartan/` task namespace and were kept minimal in 0013; unrelated concern.
- Restating the full norm or its no-hidden-state rationale in the README: the README is adopter-facing prose, not the behavioral contract. One short, faithful mention only.
- Auto-generating any `AGENTS.md`/`CLAUDE.md` content.

## Constraints

- Faithful to the shipped norm: advisory / recommend-only, one-time, tied to the repository's first Spartan task, root lacking **both** `AGENTS.md` and `CLAUDE.md`, and Spartan creates no file outside `spartan/`.
- Adopter-facing README voice: declarative, concise, second person ("you"), matching the surrounding sections. Introduce no controlled vocabulary and no MUST/SHOULD normative language in the prose.
- Minimal churn: one short paragraph in one existing section; do not restructure the README.
- English. `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill` must still pass.

## Acceptance Criteria

- [x] Root `README.md` contains one short paragraph noting the first-task `AGENTS.md` + minimal `CLAUDE.md` suggestion, placed in the "Use the Agent Spartan Protocol inside a project" section (immediately after the paragraph ending "explains this durable task namespace.", now `README.md:125`).
- [x] The paragraph states the suggestion is advisory, fires once (tied to the repository's first task, no hidden flag), and that Spartan never writes those files itself.
- [x] No other section of `README.md` changes; `agent-skill/SKILL.md`, `agent-skill/references/protocol.md`, `spartan/README.md`, and `agent-skill/assets/spartan-readme.md` are unchanged by this round.
- [x] `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill` pass.

## Decisions

- **Yes, the README should carry the note.** The behavior is adopter-visible and easy to misread as Spartan reaching outside `spartan/`; a one-line expectation-setter turns it into evidence of the recommend-only promise instead. Cost is one short paragraph, so churn does not outweigh the clarity. (Planner, 2026-07-22)
- **Home: the "Use the Agent Spartan Protocol inside a project" section**, right after the `spartan/README.md` sentence at `README.md:124`. That section already covers adoption mechanics in a target repo, so the note sits with related material rather than in the handoff/authorities sections. The `Non-goals` section stays about what Spartan never does at all; this is a positive-behavior note, so it does not belong there. (Planner, 2026-07-22)
- **Exact wording to insert** (new paragraph after `README.md:124`):

  > When Spartan creates that first task in a repository whose root has neither an `AGENTS.md` nor a `CLAUDE.md`, the round also adds one advisory line suggesting you create an `AGENTS.md` (stack, dev/build/lint/test commands, conventions) plus a minimal `CLAUDE.md` that points to it, so later hosts start with shared instructions to read. The suggestion is tied to the repository's first task — it fires once, with no hidden flag — and Spartan never writes those files for you. You decide whether to add them.

  This mirrors the SKILL.md phrasing ("stack, dev/build/lint/test commands, and conventions"), states the one-time first-task condition without implying a suppression marker, and closes on the recommend-only boundary. (Planner, 2026-07-22)

- Cockpit planning round in Claude Code (Opus 4.8, high effort), 2026-07-22, executing the follow-up 0015 suggested.
- Read `README.md`, the shipped 0015 diff (`agent-skill/SKILL.md`, `agent-skill/references/protocol.md`), `assets/task-template.md`, and `spartan/tasks/0015-repo-instructions-suggestion.md` to fix faithful wording and the correct section.
- Confirmed the README currently has no `AGENTS.md`/`CLAUDE.md` mention, so this is an addition, not an edit of existing prose.
- Resolved the prompt's question (add the note: yes) and scoped exact placement and wording above.

### Implementation round (Claude Code, Opus 4.8, medium effort), 2026-07-22

- Inserted the exact advisory paragraph from Decisions into `README.md` after the "... explains this durable task namespace." paragraph (now `README.md:125`), verbatim. No other README change.
- Verified the four named sibling files were not touched by this round; ran both strict validations.

### Review round (Codex, GPT-5.6 Sol, high effort), 2026-07-22

- Reviewed the README paragraph against the shipped task-0015 norm and confirmed its condition, advisory framing, one-time/no-hidden-flag behavior, and non-writing boundary are faithful.
- Confirmed the paragraph sits immediately after the durable-task-namespace paragraph within "Use the Agent Spartan Protocol inside a project."
- Distinguished the pre-existing task-0015 changes in `agent-skill/SKILL.md` and `agent-skill/references/protocol.md` from the README-only implementation round; the two named `spartan/` README files remain unchanged.
- Re-ran the relevant repository checks and approved the change.

## Evidence

- `grep -ni "AGENTS.md\|CLAUDE.md" README.md`: no matches — README says nothing about the norm today.
- `ls spartan/tasks/`: existing tasks run `0001`–`0015`; next unused creation number is `0016`.
- `git diff HEAD -- agent-skill/SKILL.md agent-skill/references/protocol.md`: shows the shipped 0015 wording this note must stay faithful to (SKILL.md intro paragraph; protocol.md new SHOULD sentence after line 165).
- Target insertion point: `README.md:124`, end of the paragraph "... The `spartan/README.md` file explains this durable task namespace."
- Implementation: `git diff --stat -- README.md` → `1 file changed, 2 insertions(+)` (one new paragraph + its blank line); diff confirms verbatim insertion of the Decisions wording.
- `git diff --name-only` over the four named sibling files shows only `agent-skill/SKILL.md` and `agent-skill/references/protocol.md`, both carrying pre-existing task-0015 changes present at session start — untouched by this round; `spartan/README.md` and `agent-skill/assets/spartan-readme.md` do not appear.
- `claude plugin validate --strict .` → `Validation passed` (exit 0); `claude plugin validate --strict agent-skill` → `Validation passed` (exit 0).
- Review inspection of `README.md:100-145` confirms the new paragraph is in the required section and immediately follows the paragraph ending "explains this durable task namespace."
- Review comparison with completed task 0015 confirms `agent-skill/SKILL.md` and `agent-skill/references/protocol.md` are its pre-existing shipped changes; `git diff -- spartan/README.md agent-skill/assets/spartan-readme.md` remains empty, and the current diff isolates the implementation-round addition to `README.md`.
- Review re-ran `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`; both returned `Validation passed` (exit 0).

## Review

Verdict: APPROVE

Findings:

- The paragraph is explicitly advisory ("advisory line" and "You decide"), is tied to the repository's first task, states that it fires once with no hidden flag, and says Spartan never writes `AGENTS.md` or `CLAUDE.md` for the adopter.
- Its root condition (neither file exists), suggested content, and minimal `CLAUDE.md` pointer match the shipped norm without introducing stronger behavior.
- Placement and implementation scope satisfy the plan. The four protected sibling files were unchanged by the README implementation round; the visible SKILL/protocol diffs belong to completed task 0015.
- No findings require changes.

## Blockers

None.

## Next Action

None. Review returned `APPROVE`, all acceptance criteria and checks are satisfied, and no blocker remains.

## Next Handoff

Completed — no binding handoff. No follow-up work is identifiable from this task.
