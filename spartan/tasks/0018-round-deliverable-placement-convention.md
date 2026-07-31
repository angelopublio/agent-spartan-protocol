---
protocol: "0.3.1"
id: round-deliverable-placement-convention
created_at: 2026-07-31
status: completed
phase: complete
task_type: investigation
risk: material
current_role: investigator
next_role: human-operator
updated_at: 2026-07-31
---

# Investigate where a round deliverable such as a design brief belongs

## Objective

Answer three questions with evidence: whether the protocol documents agent-created directories such
as `spartan/design/` in an adopting repository; how a round arrived at that path without a rule; and
whether such deliverables belong inside `spartan/` or outside it alongside the repository's own
`docs/`.

## Context

In the adopting repository `na5-orbit`, task `0007-prepare-users-screen-design-brief.md` created
`spartan/design/orbit-users-screen-brief.md` as the deliverable of a planning round, to be handed to
a design agent outside Spartan. The owner approves of the outcome and wants the underlying choice to
be a documented protocol position rather than an unrepeatable inference.

## Scope

- Evidence on whether `agent-skill/` names any adopting-repository path other than `spartan/tasks/`
  and `spartan/README.md`.
- The reconstructed basis for the `spartan/design/` choice.
- A recommendation on placement, on what qualifies as such a deliverable, and on whether the
  protocol should name `design/` specifically.

## Out of Scope

- Editing `SKILL.md`, `protocol.md`, `routing.md`, or any asset in this round.
- Any change to the `na5-orbit` repository, including moving the existing brief.
- A design-discipline template, checklist, or vocabulary.

## Constraints

- Read-only investigation; no normative text changed.
- Persisted content stays English and ASCII-only.
- Any proposal must pass the scope admission test and the deletion test in `protocol.md`.

## Acceptance Criteria

- [x] The "is it documented" question is answered with a concrete search result.
- [x] The mechanism by which the path was chosen is reconstructed from the artifacts.
- [x] A placement recommendation is stated with its rationale and its rejected alternatives.
- [x] The criteria separating a Spartan deliverable from repository-owned documentation are stated.

## Decisions

**F1 - The protocol does not document it, and does not forbid it.** `agent-skill/` names exactly two
adopting-repository paths: the task artifact at `spartan/tasks/NNNN-<slug>.md` and `spartan/README.md`.
No file mentions `design/`, a deliverable directory, or any other subdirectory of `spartan/`. The
directory was therefore neither compliant nor non-compliant; it fell in an unspecified area.

**F2 - The choice was a defensible inference from three existing signals**, not an arbitrary act:

1. `SKILL.md:10` says "create no file outside `spartan/`". That clause is written for the
   first-task advisory about `AGENTS.md`/`CLAUDE.md`, but it reads as a territorial rule, and the
   safe generalization is that Spartan writes only under `spartan/`.
2. The task artifact contract forbids the artifact from becoming a transcript or dump and requires
   it to stay concise, so a 99-line brief cannot live inside the task file and needs a sibling.
3. `0006`'s handoff asked for a document to hand to a design agent; `0007`'s planner named
   `spartan/design/orbit-users-screen-brief.md` in its own Scope, and the owner authorized it by
   running the round.

The defect is not the outcome but its repeatability: a different host, or the same host in another
repository, could equally have chosen `docs/design/`, `design/`, or an oversized task file.

**F3 - The brief is a category the protocol has no name for.** The protocol recognizes two
authorities: implementation truth (repository files, diff, checks) and handoff truth (the task
artifact). A design brief is neither. It is a *round deliverable*: an output too large for the task
artifact, produced by one round, consumed by a later round or by a host outside Spartan, and true as
of the task that produced it rather than maintained thereafter.

**F4 - Recommended position: keep such deliverables inside `spartan/`, and document the generic
category rather than `design/` specifically.** The proposed convention:

- A round deliverable lives at `spartan/<kind>/<slug>.md`, where `<kind>` is chosen by the producing
  task, not enumerated by the protocol.
- The producing task MUST name the deliverable path in its Scope and reference it from Work Completed.
- The deliverable is never handoff state. The task artifact remains handoff truth, and the deletion
  test still applies: remove every deliverable and the task files must stay understandable.
- A deliverable handed to a host outside Spartan MUST be readable without conversation history and
  MUST state settled decisions as settled. This is the same property already required of the task
  artifact, applied to the deliverable.
- A deliverable is point-in-time. When content must remain true after the task closes and be
  maintained by the repository, it does not stay here; it moves into the repository's own
  documentation in an explicitly authorized implementation round.

**F5 - Rejected: naming `design/` in the protocol.** Blessing one discipline invites a catalogue
(`design/`, `research/`, `security/`, `test-plans/`) and makes a host-neutral, role-based protocol
carry discipline-specific vocabulary. `<kind>` chosen per task gives the same practical result with
no growth in normative surface.

**F6 - Rejected as the default: placing deliverables outside `spartan/`, beside `docs/`.** Three
reasons. The "no file outside `spartan/`" boundary is what makes Spartan safe to adopt into a
repository whose layout it does not own; `docs/` carries an implicit obligation to stay true, which
a point-in-time brief cannot honor; and separating the deliverable from the decision trail that
justifies it loses the reason the brief says what it says. This remains available as an explicit
choice: a round MAY write into the repository's own `docs/` when the human authorizes it for that
round, which is the correct route for content the repository will maintain.

**F7 - What a design agent gains is not a template.** The brief works for an external design host
because it carries the two properties the protocol already demands of the task artifact:
self-containment without conversation history, and settled decisions marked as settled and closed to
reopening. Documenting exactly that as the deliverable contract is the whole benefit; a design-brief
template would add discipline-specific scope for no additional guarantee.

## Work Completed

- Searched `agent-skill/` for any adopting-repository path convention beyond `spartan/tasks/` and
  `spartan/README.md`; none exists.
- Reconstructed the `spartan/design/` decision from `na5-orbit` tasks `0006` and `0007` and from
  `SKILL.md`'s territorial clause.
- Recorded F1-F7 above as the investigation's findings and recommendation.
- Round executed in Claude Code (Claude Opus 5) as the cockpit host.

## Evidence

- `grep -rn "design" agent-skill/`: only `routing.md:31` ("redesigning the work") and
  `protocol.md:206` ("manual by design"); no path convention.
- `agent-skill/SKILL.md:10`: names `spartan/README.md` and `spartan/tasks/NNNN-<slug>.md`, and
  states "create no file outside `spartan/`" in the first-task advisory clause.
- `agent-skill/references/protocol.md:33,63-92`: the two authorities and the artifact contract, with
  no third category for a round output.
- `agent-skill/assets/spartan-readme.md`: describes `spartan/` as durable handoff state and excludes
  runtime state, queues, logs, credentials, and automation; it does not restrict subdirectories.
- `na5-orbit spartan/tasks/0007-prepare-users-screen-design-brief.md` Scope: "One design brief
  document at `spartan/design/orbit-users-screen-brief.md`" - the path was decided in that task's
  planning round, not by protocol.
- `spartan/tasks/0017-external-handoff-execution-clarification.md`: precedent for acknowledging a
  category (external execution) with minimal normative text instead of new machinery.
- No repository checks run: no file under `agent-skill/` changed this round.

## Review

Verdict: APPROVE

Findings:

- Read-only investigation; scope held to evidence and a recommendation, with no normative text
  changed and no adopting repository touched.
- The recommendation adds one Markdown-expressible convention, no controlled value, and no
  enforcement, so it passes the scope admission and deletion tests. Adopting it still requires an
  owner decision, which is why this task ends without a normative change.

## Blockers

None.

## Next Action

None for this task. The owner decides whether to adopt F4 as normative text; a follow-up planning
task would specify the exact wording and its placement in `protocol.md`.

## Next Handoff

```text
Recommended execution (human decides):
- Host: Claude Code - the protocol's normative text is authored and reviewed in the cockpit host.
- Model and effort: Claude Opus 5, high effort.
- Invocation: `/spartan`, passing the prompt block below as the argument
```

```text
Create a new task in `spartan/tasks/` from `assets/task-template.md` with the next unused four-digit number.

Act as planner. Specify the exact normative wording for the round-deliverable convention recommended
as F4 in `spartan/tasks/0018-round-deliverable-placement-convention.md`, and where it belongs in
`agent-skill/references/protocol.md`. Keep `SKILL.md` unchanged unless the wording cannot work
without it. Add no controlled value, no directory catalogue, and no discipline-specific template.

Return only the next handoff, or a completion notice if no work remains.
```
