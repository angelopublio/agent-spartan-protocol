---
protocol: "0.3.1"
id: reserved-directories-and-design-brief
created_at: 2026-07-31
status: completed
phase: complete
task_type: implementation
risk: material
current_role: independent-reviewer
next_role: human-operator
updated_at: 2026-07-31
---

# Reserve the Spartan directory names and define design material

## Objective

`agent-skill/references/protocol.md` states which directory names under `spartan/` are reserved,
defines `spartan/design/` as the home of a task's point-in-time design material, and states the rule
by which a settled interface standard graduates out of `spartan/` into the repository's own design
documentation, so that an agent in any adopting repository reaches the same layout without inferring
it.

## Context

Task `0018` established that the protocol names only `spartan/tasks/` and `spartan/README.md`, and
declined to name `design/` (finding F5). The owner asked for `tasks/`, `design/`, and `docs/` to be
settled by the protocol itself.

Two earlier framings of `design/` were wrong and are superseded here. `0018` F5 treated it as a
discipline label. An intermediate draft of this task treated it as a handoff form for a host that
cannot read the task artifact. The owner corrected both: `design/` exists because a task raises and
settles things that govern how information is presented - typography, sizing, markup and class
conventions, base layout, individual screens and webparts.

That correction exposes a larger defect than the one being fixed. The working example,
`na5-orbit spartan/design/orbit-users-screen-brief.md`, carries an entire "Existing product context
to match" section restating the app's sidebar, page header, list card, avatar treatment, and status
badge vocabulary. It restates them because those standards had no home, so every future brief would
have to restate them again and each copy would drift.

Owner decision (2026-07-31): separate the two natures. Point-in-time design material of a task stays
in `spartan/design/`; a settled interface standard that must stay true as the product changes is
maintained documentation and leaves `spartan/`. The owner also set the rule for where it lands - if
the repository already has a home for it, such as a component workshop or an existing design
document, the round points there and does not invent a second one.

That separation is the same test that settles `docs/`. Maintained documentation under `spartan/`
would make Spartan the owner of durable product documentation, would invert the deletion test
(removing the skill and `spartan/` would strip the repository of documentation its code depends on),
and would create a competing home in any repository that already documents its interface. So
`spartan/docs/` is standardized as a prohibition rather than as a directory.

## Scope

- One new section in `agent-skill/references/protocol.md` reserving the directory names, defining
  the round deliverable, defining design material, and stating the graduation rule, with the wording
  drafted below.
- One sentence in `agent-skill/assets/spartan-readme.md` so an adopting repository's `spartan/README.md`
  explains `design/` when it appears.

## Out of Scope

- Any change to `SKILL.md` (precedent: tasks `0005` and `0017` keep detail in the references).
- A design-brief template asset, a design vocabulary, a token schema, or a checklist.
- Naming a concrete design-documentation path for adopting repositories to use.
- Enumerating any directory name beyond `tasks/`, `design/`, and the forbidden `docs/`.
- Migrating or editing the existing brief in the `na5-orbit` repository.

## Constraints

- Persisted content stays English and ASCII-only.
- No new controlled value, frontmatter field, role, or enforcement mechanism.
- The result must keep passing the scope admission test and the deletion test.
- `spartan/design/` stays optional: a repository whose rounds settle nothing about presentation
  never creates it.

## Acceptance Criteria

- [x] `protocol.md` reserves `spartan/tasks/`, permits `spartan/design/`, forbids `spartan/docs/`,
      and leaves any other deliverable to `spartan/<kind>/<slug>.md` named by the producing task.
- [x] `protocol.md` defines design material by its lifecycle - point-in-time, not maintained - and
      states its required and forbidden content.
- [x] `protocol.md` states the graduation rule, including that a round MUST use the repository's
      existing design-documentation home when one exists and MUST NOT create a second one.
- [x] `protocol.md` states that design material references settled standards rather than restating
      them.
- [x] `protocol.md` states the flat-directory and supersession rules, and that design material is
      reached from its task rather than by browsing.
- [x] `protocol.md` states when a presentation decision stays in the task artifact and when it
      becomes a file in `spartan/design/`.
- [x] `protocol.md` requires a round to obtain explicit human authorization before creating or
      updating any file outside `spartan/`, and requires the request to name the proposed path,
      whether it is a creation or an update, what graduates, and what stays in `spartan/design/`.
- [x] `protocol.md` states that `spartan/design/` is outbound only and requires the consuming task's
      Evidence to record where a returned design landed, without naming a location for it.
- [x] `protocol.md` requires the link in both directions: the producing task names its design
      material, and the design material names the task that produced it.
- [x] `agent-skill/assets/spartan-readme.md` mentions `design/` in one sentence.
- [x] `SKILL.md` is unchanged and `agent-skill/` remains ASCII-only.
- [x] A fresh-context review in a host other than the implementing one issues an explicit verdict.

## Decisions

**D1 - Three names are settled, two as directories and one as a prohibition.** `tasks/` is required,
`design/` is permitted and defined, `docs/` is forbidden under `spartan/`. Anything else follows the
`spartan/<kind>/` rule from `0018` F4, which stays in force for cases the protocol should not
anticipate.

**D2 - `design/` is defined by lifecycle, not by subject and not by reader.** It holds what a task
raises and settles about how information is presented, while that material is still tied to the task
that produced it: the brief for a screen, a layout or component choice, a state vocabulary, an open
question left to a designer. The defining property is that it is true as of its task and is not
maintained afterwards.

**D3 - A settled interface standard graduates out of `spartan/`.** Typography, sizing, colour,
markup and class conventions, base layout, and component patterns must stay true as the product
changes, and the code implements them. That makes them maintained documentation, which by D1 cannot
live under `spartan/`. When a round settles one, it belongs in the repository's own design
documentation.

**D4 - Graduation uses the repository's existing home and never creates a competitor.** If the
repository already documents its interface - a component workshop such as Storybook, a design-token
file, a style configuration, or an existing design document - the round writes there. Only when no
such home exists does the round create one, and only in an explicitly authorized round. The protocol
names no path, because naming one would put Spartan in the position of dictating layout it does not
own, which is the same overreach D1 rejects for `spartan/docs/`.

**D5 - Design material references settled standards rather than restating them.** This is the
concrete payoff of D3 and D4: the "Existing product context to match" section of a brief becomes a
pointer, so a standard has one copy that can be corrected once and cannot drift between briefs.

**D6 - One flat file per piece of design material, never edited after its task completes.**
`spartan/design/<slug>.md`, no subdirectories, for the same reason `spartan/tasks/` is flat: a flat
list is scannable and needs no navigation convention. Revised material is a new file with its own
slug, produced by a new task. No version suffix, index, or status field is needed, because design
material is not discovered by browsing the directory - it is always reached from the task that
produced or consumed it. That keeps the "no hidden operational state" invariant intact, since the
directory carries no currency signal a reader could misread.

**D7 - The returned design deliberately has no protocol-defined home.** What an external design host
returns is product material governed by the target repository's own conventions, so the protocol
states one negative rule and one positive obligation instead of a path: the returned design MUST NOT
be stored under `spartan/`, and the task that consumes it MUST record in its Evidence where it
landed, or how to reach it when it lives outside the repository. The obligation is what makes the
negative rule safe - it keeps the trail from brief to design to implementation followable without
conversation history, as invariant 3 requires, at the cost of one Evidence line.

**D8 - The default is the task artifact; `spartan/design/` is the exception.** A presentation
decision a round makes stays in that task's Decisions, like any other decision. It becomes a file in
`spartan/design/` only when it is too large for the artifact to stay concise, or when it is
addressed to a reader outside the round, such as a designer or a later implementation round. Without
this threshold, one agent records a layout choice in the task and another creates a file for the
same thing. It is the round-deliverable rule applied to presentation, stated explicitly because this
is the case where a round is most tempted to create a file it does not need.

**D9 - Graduation requires an explicit authorization request, and the request must show the split.**
Graduation is the first case in which a Spartan round would write outside `spartan/`, so without a
ceremony D3 and D4 would silently authorize crossing the boundary that makes Spartan safe to adopt.
A round that has settled a standard MUST NOT create or update the outside file on its own
initiative. It records the proposal in the task artifact and asks the human, using the existing
routes for a missing authority - a blocking human question, or a handoff with `human-operator` as
next role - so no new state or mechanism is introduced.

The request MUST state four things: the proposed path outside `spartan/`; whether it is a creation
or an update, and for an update which existing home was found; what content graduates; and what
stays in `spartan/design/`. The last pair is the point of the request. The human is being asked to
approve a split, not just a file write, and cannot judge it without seeing both sides.

This also keeps the `SKILL.md` first-task clause "create no file outside `spartan/`" correct in its
own context. `protocol.md` becomes the authority on the boundary and states the one authorized
exception; the implementer MUST NOT alter that clause to accommodate this.

**D10 - No template ships.** What made the working example usable were properties the protocol
already demands of the task artifact - self-containment without conversation history, and settled
decisions stated as settled. A design-brief template would add discipline-specific scope for no
additional guarantee.

**D11 - Scope admission check.** Markdown-expressible; finishes in the foreground; the human still
carries design material to any external host; the task artifact remains the only durable *protocol*
state, since nothing in the protocol reads design material to decide behavior; manual continuation
is unaffected; no keys, daemons, or subprocesses; no claimed enforcement. Deletion test: remove the
skill and both the task files and the design material remain readable plain documents, and the
graduated standards were never in `spartan/` to begin with.

## Work Completed

- Superseded `0018` F5 and this task's own earlier "host that cannot read the task artifact"
  framing, both recorded in Context with the reason each was wrong.
- Settled the two-nature separation with the owner (D2, D3), the graduation target rule (D4), and
  the anti-restatement rule (D5).
- Settled the placement questions left open: flat file and supersession (D6), the absence of a
  protocol-defined home for the returned design, replaced by an Evidence obligation (D7), and the
  threshold separating a decision recorded in the task artifact from one that becomes a file (D8).
- Closed the boundary hole the graduation rule opened, by requiring an explicit authorization
  request that shows both sides of the split before any file outside `spartan/` is written (D9).
- Drafted the exact normative wording below for the implementer round.
- Round executed in Claude Code (Claude Opus 5) as the cockpit host.
- Inserted the drafted "Repository layout" and "Design material" sections immediately after the
  task-artifact contract in `agent-skill/references/protocol.md`, and updated the table of contents
  to keep the section numbering accurate.
- Added the drafted one-sentence explanation of point-in-time design material to
  `agent-skill/assets/spartan-readme.md`.
- Implementation round executed in Codex.
- Resolved review finding 1 by qualifying the `spartan/` boundary with the single explicitly
  authorized graduation exception.
- Resolved review finding 2 by removing the inaccurate cross-reference to `Authorization and
  mutations`; the graduation section now states its own authorization requirement directly.
- Resolved review finding 3 by requiring the producing task to name and reference its design
  material, and the design-material file to name its producing task; it may also cite decision
  tasks.
- Fix round executed in Codex.

## Proposed wording

Implemented and superseded. The normative text is now the authority in
`agent-skill/references/protocol.md`, sections "Repository layout" and "Design material" including
the "Graduating a settled standard" subsection. The draft is not reproduced here: keeping a second
copy of normative text in a completed task is the same drift risk D5 removes from design material,
and the wording as shipped is recoverable from this task's commit history.

## Evidence

- `spartan/tasks/0018-round-deliverable-placement-convention.md`: F1 (no documented convention), F4
  (the `spartan/<kind>/` rule this task keeps), F5 (superseded here, with the reason recorded).
- `na5-orbit spartan/design/orbit-users-screen-brief.md:29-46`: the "Existing product context to
  match" section restating sidebar, page header, list card, avatar, and badge conventions - the
  concrete instance of the restatement problem D5 removes.
- `agent-skill/references/protocol.md:212-231`: the scope admission and deletion tests applied in D9.
- Owner decision (2026-07-31, this round): separate maintained standards from point-in-time design
  material, and point graduation at whatever home the repository already uses.
- `git diff --check`: passed.
- `git diff --exit-code -- agent-skill/SKILL.md`: passed; `SKILL.md` is unchanged.
- `git diff --name-only -- agent-skill`: reported only `assets/spartan-readme.md` and
  `references/protocol.md`.
- `LC_ALL=C rg --pcre2 -n '[^\x00-\x7F]' agent-skill`: no matches; the package is ASCII-only.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both passed.
- Focused protocol scan found the qualified boundary, direct graduation authorization, and both
  required task/design-material links; the obsolete authorization cross-reference is absent.

Review round (Claude Code, Claude Opus 5), read-only:

- `git diff -- agent-skill/`: both sections present at `protocol.md:96` and `:113`, with the
  `Graduating a settled standard` subsection at `:145`; the readme sentence added at
  `spartan-readme.md:7`. Text matches the drafted wording.
- `grep -n '^## ' agent-skill/references/protocol.md`: fourteen sections in the order the updated
  table of contents lists; `Repository layout` is 7 and `Design material` is 8, as renumbered.
- `git diff --stat -- agent-skill/SKILL.md`: empty; `wc -l` reports 83 lines, unchanged.
- `LC_ALL=C grep -rn '[^\x00-\x7F]' agent-skill/`: no matches. `git diff --check`: clean.
- `claude plugin validate --strict .`: validation passed.
- `agent-skill/references/protocol.md:98` versus `:151`: the contradiction recorded as finding 1.
- `agent-skill/references/protocol.md:255-259`: the `Authorization and mutations` section, whose
  enumerated list does not include the action the graduation text cross-references (finding 2).
- Second review round (Claude Code, Claude Opus 5): `git status --short` and `git diff -- agent-skill/`
  return the same two modified files and the same content as the first review; `protocol.md:98` and
  `:255-259` are unchanged, so neither finding was addressed. `SKILL.md` still 83 lines,
  `agent-skill/` still ASCII-only, `git diff --check` clean.
- `agent-skill/references/protocol.md:107-109`: the Scope and Work Completed obligation is scoped to
  "Any other output", which excludes design material (finding 3, forward link).
- `agent-skill/references/protocol.md:113-137`: the Design material section contains no requirement
  that the file name its producing task (finding 3, backward link).
- `na5-orbit spartan/design/orbit-users-screen-brief.md:3`: cites `0006`, the decision task, not
  `0007`, the producing task - the reference exists by authorial judgment and is not the link the
  owner needs.

## Review

Verdict: APPROVE

Findings:

- All three findings from the CHANGES verdict are resolved in `agent-skill/references/protocol.md`.
  Finding 1: the boundary sentence at `:98` now reads "except for the one explicitly authorized case
  in Graduating a settled standard", so the layout section and `:155` no longer contradict each
  other. Finding 2: the inaccurate cross-reference was removed rather than the target section
  extended; the graduation text now states its authorization requirement directly, which is the
  tighter of the two options offered. Finding 3: `:118-120` requires the link in both directions -
  the producing task names the path in Scope and references it from Work Completed, and the file
  names the task artifact that produced it, with decision tasks allowed as an addition.
- No new defect introduced. "Design material is a round deliverable" coexists with "Any other output
  ... is a round deliverable" without contradiction, because the latter states a sufficient
  condition rather than an exclusive definition, and the design-material section restates the
  obligation locally. The table of contents matches the fourteen sections in document order.
- The task template needs no change: Scope and Work Completed, where the forward link is recorded,
  already exist in `assets/task-template.md`.

Independence limitation, recorded rather than claimed away: this review ran in Claude Code, a
different host and vendor than the Codex rounds that implemented and fixed the change, but the same
host that drafted the proposed wording and raised the findings. Independence covers the
implementation, the fixes, and the checks, not the drafted text itself.

## Blockers

None.

## Next Action

None for this task. The reserved-directory, design-material, graduation, and bidirectional-link
rules are in `agent-skill/references/protocol.md` and approved; the change is uncommitted, and
committing it is the owner's call.
