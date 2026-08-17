---
protocol: "0.6.1" # x-release-please-version
id: reconcile-agent-hosts-heading-collision
created_at: 2026-08-17
status: completed
phase: complete
task_type: implementation
risk: material
current_role: reviewer
next_role: none
updated_at: 2026-08-17
handoff_id: HX-005
next_handoff_id: none
---

# Reconcile the two published meanings of `## Agent hosts`

## Objective

A reader of this repository can tell, from either file alone, which `## Agent hosts` applies to them:
the portable protocol's advisory host preference or the Spartan Bridge's machine-parsed binding
table. This artifact settles the recommendation, its cost, and what stays unchanged, so a later
implementation round can edit `README.md` without conversation history.

## Context

Two published meanings now share one heading in this repository.

- `README.md` teaches `## Agent hosts` as a `| Role | Host |` table of Spartan roles, closing with
  "These are routing preferences, not execution authority. The human starts every round." Nothing
  parses it; it is a human-facing example (settled in tasks `0020` and `0021`, which fixed the
  content of the examples and never fixed the heading name).
- The committed root `AGENTS.md` carries `## Agent hosts` as a `| Binding | Host | Client context |`
  table of Bridge bindings (`planner`, `reviewer.plan`, `implementer`, `reviewer.implementation`),
  plus `## Spartan Bridge automation authority`. This is machine-parsed by the companion runtime at
  `~/Documents/DEV/agent-spartan-protocol-bridge`, `src/policy/agents-policy.ts` (task `0027`).

The collision was raised as Findings 1-3 of task `0027`, which is complete and must not be reopened.

The heading is not free text on the Bridge side. All four parser facts below were established by
reading `src/policy/agents-policy.ts` and by running it against real inputs (see Evidence), not
taken on trust:

1. `## Agent hosts` is matched by exact string equality against the whole heading line
   (`AGENT_HOSTS` constant, line 21; comparison at line 66; heading captured as the raw line at line
   175). Zero matches returns `reviewer_binding_missing`.
2. Two or more `## Agent hosts` level-2 sections return `agents_policy_invalid`, which the caller
   maps to run state `failed` — the whole policy, not merely that section.
3. `collectLevel2Sections` is a plain line scan for `^## ` with no fenced-code-block awareness. A
   fenced illustrative example inside `AGENTS.md` counts as a real second section.
4. The two tables are structurally incompatible: the parser requires exactly three headers
   `Binding | Host | Client context` and a `reviewer.plan` row. A two-column `| Role | Host |` table
   is `agents_policy_invalid`. Separately, `The human starts every round.` alone on a trimmed line is
   a hard denial of automatic review (`automatic_review_not_authorized`).

The runtime reads only `<repoRoot>/AGENTS.md` (`src/core/review.ts:176`). It never reads `README.md`,
which is why the two fenced examples there are harmless today.

## Scope

- `README.md` only, in the section "Use the Agent Spartan Protocol inside a project": rename the
  heading used in both fenced examples and the prose around them, and add one short cross-reference
  paragraph about the Bridge.
- This task artifact.

## Out of Scope

- Root `AGENTS.md`. Not one byte changes, including the `## Agent hosts` heading, its table, the
  `personal` alias paragraph, the context-separation note, and `## Spartan Bridge automation
  authority`.
- The companion Bridge repository. The recommendation below deliberately requires no change there.
- Anything under `agent-skill/`, including `SKILL.md`, `references/`, and
  `assets/spartan-readme.md`. None of them uses the heading literal.
- `spartan/tasks/0027-adopt-bridge-consumer-configuration.md` and every other task artifact.
- Commit, push, tag, pull request, merge, or release.
- Redesigning this repository's host bindings, cycle count, or write grant. Those are `0027`'s
  settled decisions.

## Constraints

- No fenced block added to `AGENTS.md` may contain a line beginning `## `. Fact 3 makes such an
  example a real second section that silently invalidates this repository's own configuration. The
  reconciliation is therefore documented in `README.md`, never demonstrated inside `AGENTS.md`.
- The sentence `The human starts every round.` must not be split onto a line of its own anywhere in
  a `README.md` example. It stays as the second half of the existing closing line.
- The README must not lead a no-runtime adopter to believe any host declaration grants execution
  authority. The Bridge cross-reference is clearly labelled optional and external, and is placed
  after the protocol guidance, never before it.
- The two table shapes must be presented as separate things on separate axes, never as versions of
  each other, and the README must not suggest merging or converting one into the other.
- Persisted repository content is English.
- No commit in the implementation round unless the human authorizes it for that round.

## Acceptance Criteria

- [x] `README.md` uses `## Spartan host preferences` as the heading inside both fenced host examples,
      and the surrounding prose names the same heading.
- [x] `README.md` states, in the same section, that a repository running the Spartan Bridge also
      carries a differently shaped `## Agent hosts` section in `AGENTS.md`, that the Bridge is an
      optional, separately installed runtime, that the two sections are separate and must not be
      merged or converted, and that this repository's own `AGENTS.md` is such a configuration.
- [x] `README.md` states that a Bridge binding table does not satisfy Spartan's no-self-review
      preference: binding the same host to producer and reviewer buys context separation, not
      cross-host or cross-vendor independence.
- [x] `README.md` states, in one sentence, that the Bridge treats `The human starts every round.`
      alone on a line as a denial of automatic review, so the coupling stops being discovered by
      failure.
- [x] The protocol guidance appears before the Bridge cross-reference in reading order, and neither
      example gains a host-authority claim.
- [x] The only files this task changes are `README.md` and this artifact,
      `spartan/tasks/0028-reconcile-agent-hosts-heading-collision.md`. `AGENTS.md`, everything under
      `agent-skill/`, and every other task artifact stay byte-identical — including
      `spartan/tasks/0027-adopt-bridge-consumer-configuration.md`, which already carries uncommitted
      edits from an earlier round. That `0027` modification is pre-existing working-tree state, not
      this task's work: it must not be reverted, staged, committed, or counted as an in-scope change,
      and the tree does not have to be clean for this criterion to pass. Check it by capturing
      `git status --short` and `git diff --stat` immediately before the round's first edit and again
      after its last. The only permitted differences between the two captures are `README.md`
      appearing as modified and this artifact's own entry changing; `0027`'s status letters and its
      `git diff --stat` line must be identical in both, and no path absent from the first capture may
      appear in the second except `README.md`. `git diff --name-only` alone does not satisfy this
      check: it omits this artifact while the file is still untracked and it lists the pre-existing
      `0027` modification, so on its own it can neither confirm this task's edits nor separate them
      from state the round did not create.
- [x] Root `AGENTS.md` still parses as `ok: true`, `host: cursor`, `client_context: personal`,
      `automatic_review_authorized: true`, `task_artifact_write_authorized: true`,
      `max_review_cycles: 3`, byte-identical to the current file.
- [x] `README.md` contains no standalone-line occurrence of `The human starts every round.`
- [x] A reviewer records an explicit `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED` verdict on this
      plan before any implementation round.

## Decisions

- **The Bridge table keeps the `## Agent hosts` heading.** The heading is a parser contract on the
  `AGENTS.md` side and free prose on the `README.md` side. Disambiguation moves the unenforced side.
  This is the whole recommendation in one line: rename the documentation, never the contract.
- **`README.md` renames its documented heading to `## Spartan host preferences`.** It names the
  system it configures, so a reader knows which of the two it belongs to, and it is far enough from
  `## Agent hosts` not to be misread. It is not a literal the Bridge matches, so it can never
  collide. The literal `## Agent hosts` appears in this repository only in `README.md` (two example
  blocks) and `AGENTS.md`; no file under `agent-skill/` uses it, so the change is contained.
- **The rename improves the mixed-adoption failure mode.** An adopter who follows today's README and
  later installs the Bridge has a two-column `## Agent hosts` in their `AGENTS.md`, which parses as
  `agents_policy_invalid` and terminates the run as `failed`. Under the new heading they have no
  `## Agent hosts` at all, which parses as `reviewer_binding_missing` and terminates as `blocked` —
  an accurate signal ("you declared no Bridge binding") instead of a misleading one ("your policy is
  broken"). Both fail closed; the second is diagnosable.
- **Rejected: renaming the heading in `AGENTS.md`.** This is a coordinated two-repository change, not
  a documentation decision, and its cost is recorded here as required. It would touch six files in
  the Bridge repository that carry the literal — the `AGENT_HOSTS` constant at
  `src/policy/agents-policy.ts:21`, `tests/agents.test.ts`, `tests/helpers.ts`, `README.md`,
  `docs/ROUTING-AND-WORKFLOWS.md`, and the Bridge's own `AGENTS.md`, which is itself a consumer
  configuration — plus this repository's `AGENTS.md`. It must land in the Bridge first: in any window
  where this repository's `AGENTS.md` carries the new heading and the runtime still matches the old
  one, every Bridge run against this repository returns `reviewer_binding_missing` and terminates as
  `blocked`. Every other consumer `AGENTS.md` breaks the same way until updated. The Bridge being
  private at `0.1.0` makes this the cheapest moment it will ever be, which is the one honest argument
  for doing it; it is still a runtime change bought to fix a documentation ambiguity that a heading
  rename on the free side fixes for nothing.
- **Same heading: no. Same file: yes, and forced.** The runtime reads `<repoRoot>/AGENTS.md` and
  nothing else (`src/core/review.ts:176`), so the Bridge block cannot be relocated, and Spartan host
  preference is repository instruction, which is `AGENTS.md` by this project's own convention. A
  repository running both therefore carries two distinct headings in one file, with a one-line
  pointer between them. That is a property of the runtime, not a choice this plan makes.
- **Same repository: only a pointer.** The protocol README does not become Bridge documentation. It
  gains a short, clearly labelled paragraph — the Bridge is optional and separately installed, it
  parses its own `## Agent hosts` in `AGENTS.md`, this repository's `AGENTS.md` is an example of one,
  and the full configuration reference lives in the Bridge project. Everything beyond that stays in
  the Bridge repository.
- **What an adopter should see first: the portable protocol with no host authority.** The existing
  host-preference guidance keeps its position and its framing, and the Bridge cross-reference follows
  both examples. A reader who never installs a runtime must reach the end of the section without
  encountering anything that implies a host binding grants execution authority.
- **The two tables sit on different axes, and the README says so.** Spartan's is an advisory routing
  preference between hosts; the Bridge's is an execution binding between named contexts. A Bridge
  binding table neither satisfies nor overrides the no-self-review preference — which is why this
  repository, pinning Cursor to both `implementer` and `reviewer.implementation`, discloses in
  `AGENTS.md` that it buys context separation rather than independence. Stating this in the README
  keeps the reference repository's own shape from reading as advice to copy.
- Risk is `material`: the change is confined to `README.md` prose and is trivially reversible, but it
  edits the public reference and sits next to a live parser contract. The rejected alternative would
  have been `high-impact`; declining it is what keeps this round `material`.

## Work Completed

- Read `src/policy/agents-policy.ts` and `src/core/review.ts` in the companion Bridge repository and
  established all four parser facts against the source, then confirmed each by executing the parser
  against constructed inputs rather than by inspection alone.
- Established that the heading literal is contained in this repository: `README.md` lines 140 and
  155, and `AGENTS.md` line 21. Nothing under `agent-skill/` or `docs/` uses it, so the recommended
  change touches one file.
- Recorded the recommendation, the rejected two-repository alternative with its measured cost, and
  the scope, constraints, and acceptance criteria a later round can act on without this conversation.
- Changed no repository file. Did not open, reopen, or update
  `spartan/tasks/0027-adopt-bridge-consumer-configuration.md`, whose uncommitted review-round edits
  were left exactly as found. Did not commit.
- This round ran in Claude Code on Claude Opus, high effort (Anthropic vendor).
- Plan review (HX-001, matching `next_handoff_id`): read-only. Verdict `CHANGES_REQUESTED` on one
  acceptance criterion. Did not edit `README.md`, `AGENTS.md`, `agent-skill/`, or `0027`. Did not
  commit. Ran in Cursor on Grok 4.6, high effort (vendor attribution joint Cursor/SpaceXAI).
- Planning round HX-002 (matching `next_handoff_id`): restated the file-scope acceptance criterion to
  answer the review finding. It now names `README.md` and this artifact as the only files this task
  may change, states that every other task artifact including `0027` stays byte-identical, declares
  the pre-existing uncommitted `0027` edits out of scope and not to be reverted or staged, drops the
  clean-tree assumption, and replaces the `git diff --name-only` check with a before/after
  `git status --short` plus `git diff --stat` comparison the implementation round performs within its
  own round. Reopened no other criterion, decision, or cost. Changed no repository file other than
  this artifact; did not touch `0027`. Did not commit. Ran in Claude Code on Claude Opus, high effort
  (Anthropic vendor).
- Plan re-review (HX-003, matching `next_handoff_id`): read-only. Checked only the restated
  file-scope acceptance criterion. Verdict `APPROVED` on the plan as a whole. Did not reopen the
  recommendation, the rejected two-repository alternative, or any other criterion. Did not edit
  `README.md`, `AGENTS.md`, `agent-skill/`, or any other task artifact. Did not commit. Ran in
  Cursor on Grok 4.6, high effort (vendor attribution joint Cursor/SpaceXAI). The pasted prompt
  carried HX-003 and matched.
- Implementation round (HX-004, matching `next_handoff_id`): applied the approved plan to `README.md`
  only, in four edits. Renamed both fenced example headings from `## Agent hosts` to
  `## Spartan host preferences` (lines 140 and 155), named that heading in the prose that introduces
  them (line 135), and added the Bridge cross-reference as three paragraphs after the model-list
  paragraph that closes the protocol guidance (lines 169-173), before the "continue an existing task"
  block. The cross-reference labels the Bridge optional and separately installed, states that a
  Bridge repository carries a differently shaped `## Agent hosts` in the same `AGENTS.md`, puts the
  two on different axes and forbids merging or converting them, points at this repository's own
  `AGENTS.md` as an example, states that a binding table buys context separation rather than
  cross-host or cross-vendor independence, and states the `The human starts every round.` denial in
  one sentence. Neither example gained a host-authority claim, and neither closing line was split.
  Did not edit `AGENTS.md`, anything under `agent-skill/`, or any other task artifact; did not
  revert, stage, or commit the pre-existing `0027` edits; did not commit. Ran in Claude Code on
  Claude Opus, high effort (Anthropic vendor) — the human overrode the advisory Cursor
  recommendation for that round, which left the implementation and this review cross-vendor.
- Implementation review (HX-005, matching `next_handoff_id`): read-only. Verdict `APPROVED` on the
  uncommitted `README.md` diff. Did not edit `README.md`, `AGENTS.md`, `agent-skill/`, or any other
  task artifact. Did not revert, stage, or commit the pre-existing `0027` edits. Did not commit.
  Ran in Cursor on Grok 4.6, high effort (vendor attribution joint Cursor/SpaceXAI), independent of
  the Claude Code / Claude Opus implementation round.

## Evidence

- Parser probe, executed with `npx tsx` against
  `agent-spartan-protocol-bridge/src/policy/agents-policy.ts` and this repository's real `AGENTS.md`:
  committed file as-is → `ok: true, host: cursor, client_context: personal,
  automatic_review_authorized: true, task_artifact_write_authorized: true, max_review_cycles: 3`;
  heading renamed to `## Bridge bindings` → `reviewer_binding_missing`; one fenced markdown example
  containing `## Agent hosts` appended → `agents_policy_invalid`; the README's two-column `| Role |
  Host |` form used as the only host section → `agents_policy_invalid`; `The human starts every
  round.` inserted as its own line into the otherwise valid file → `automatic_review_not_authorized`.
- `README.md` fed to the parser returns `agents_policy_invalid` (its two fenced examples are seen as
  two sections). This is inert: `src/core/review.ts:176` joins `repoRoot` with `AGENTS.md` and the
  runtime opens no other path.
- Caller behaviour at `src/core/review.ts:189-192`: `agents_policy_invalid` terminates the run as
  `failed`; every other parse reason terminates it as `blocked`.
- Before this round's edits, `grep -rn "Agent hosts"` over this repository returned `README.md:140`,
  `README.md:155`, `AGENTS.md:21`, and task artifacts only, and both `README.md` occurrences of
  `The human starts every round.` (then lines 149 and 164) were the second sentence of a longer line.
  Over the Bridge repository the literal lives in `src/policy/agents-policy.ts`,
  `tests/agents.test.ts`, `tests/helpers.ts`, `README.md`, `docs/ROUTING-AND-WORKFLOWS.md`,
  `AGENTS.md`, and one of its own task artifacts. See the HX-004 content checks below for the
  post-edit state.
- Independent review of the parser source confirmed the four recorded facts (`AGENT_HOSTS` line 21,
  comparison line 66, heading capture line 175; `review.ts:176` reads only `AGENTS.md`;
  `review.ts:189-192` maps `agents_policy_invalid` to `failed`). Bridge heading-literal files are the
  six named above plus the Bridge task artifact; `docs/AUTHENTICATION-AND-SECURITY.md` mentions an
  "Agent hosts table" in prose, not the heading, and does not change the rejected-alternative
  conclusion. `grep` over `agent-skill/` for `## Agent hosts` and `Spartan host preferences`: no
  matches, before and after this round.
- HX-002 and HX-003 planning/review checks both found the same working tree — ` M ...0027...` plus
  `?? ...0028...`, with `0027` at `35 insertions(+), 16 deletions(-)` — and confirmed that
  `## Agent hosts` appeared only at `AGENTS.md:21`, `README.md:140`, and `README.md:155`, that
  `Spartan host preferences` was free everywhere, and that `README.md` had no standalone-line
  `The human starts every round.` Superseded as a working-tree record by the HX-004 captures below.
- HX-004 file-scope check, both captures taken in this repository within the implementation round.
  Before the first edit, `git status --short` was ` M spartan/tasks/0027-...md` and
  `?? spartan/tasks/0028-...md`, and `git diff --stat` was the single `0027` line,
  `35 insertions(+), 16 deletions(-)`. After the last edit, `git status --short` was
  ` M README.md`, ` M spartan/tasks/0027-...md`, `?? spartan/tasks/0028-...md`, and `git diff --stat`
  was `README.md | 12 +++--` plus the `0027` line, `35 insertions(+), 16 deletions(-)`, byte-for-byte
  the same `0027` figure. The only differences between the captures are `README.md` appearing as
  modified and this artifact's own entry; `0027`'s status letters and `git diff --stat` line are
  identical in both, and no other path appears in the second capture. `git diff --quiet HEAD --`
  confirms `AGENTS.md` and `agent-skill/` are unchanged from `HEAD`.
- HX-004 content checks. `grep -rn "## Agent hosts"` outside `spartan/` and `.git`: `AGENTS.md:21`
  and `README.md:169` only — the two example headings are gone from `README.md`, and the one
  remaining README occurrence is the Bridge cross-reference naming the Bridge's heading in inline
  code, not a heading line. `grep -rn "Spartan host preferences"`: `README.md:135` (prose),
  `README.md:140` and `README.md:155` (the two fenced headings), and `README.md:169`.
  `grep -n "^The human starts every round\.$" README.md`: no matches, so both example closing lines
  are still intact and the new sentence keeps the literal inline.
- HX-004 parser probe. `npx tsx` running `parseAgentsPolicy` from
  `agent-spartan-protocol-bridge/src/policy/agents-policy.ts` against this repository's unchanged
  `AGENTS.md` →
  `{"ok":true,"host":"cursor","client_context":"personal","automatic_review_authorized":true,"task_artifact_write_authorized":true,"max_review_cycles":3}`.
  The probe script was written to the session scratchpad and the copy used inside the Bridge
  repository was removed, leaving that repository's working tree as found.
- This project ships no lint or test suite; `.github/workflows/` contains only `release-please.yml`,
  which runs on push to `main` and is unaffected by an uncommitted documentation round. The grep
  audits and the parser probe above are the applicable checks.
- HX-005 independent re-check of the uncommitted `README.md` (read-only). Reading order: protocol
  host-preference prose, both fenced examples, and the model-list paragraph occupy lines 135-167;
  the Bridge cross-reference follows at 169-173, before the continue-an-existing-task block. Both
  example closings are still the second sentence of
  `These are routing preferences, not execution authority. The human starts every round.` The
  fenced-block diff is the heading rename only. The cross-reference labels the Bridge optional and
  separately installed, with `nothing above depends on it`, and does not attach a host-authority
  claim to either example. Axes: `## Spartan host preferences` is named an advisory routing
  preference between hosts; `## Agent hosts` is named an execution binding between named client
  contexts; they `stay separate and must not be merged, nor one converted into the other`.
  `grep -n "^The human starts every round\.$" README.md`: no matches. A trim-equality scan of every
  README line for that sentence: empty. `## Spartan host preferences` as a heading line: 140 and
  155 only; no README line is the heading `## Agent hosts`. `git hash-object AGENTS.md` equals
  `HEAD:AGENTS.md` (`24373b225cf9ef44144565db7a10b684fb420640`). `git diff --stat HEAD -- agent-skill`
  is empty. `git status --short` is ` M README.md`, ` M ...0027...`, `?? ...0028...`. `0027` remains
  ` M` with `35 insertions(+), 16 deletions(-)`, identical to the HX-004 after-capture. Parser
  probe, `npx tsx` `parseAgentsPolicy` against this repository's `AGENTS.md` from the Bridge
  repository: `{"ok":true,"host":"cursor","client_context":"personal","automatic_review_authorized":true,"task_artifact_write_authorized":true,"max_review_cycles":3}`.
  No file was written in the Bridge repository.

## Review

Verdict: APPROVED (implementation review of the HX-004 `README.md` diff; HX-005, matching
`next_handoff_id`; Cursor on Grok 4.6, high effort; vendor attribution joint Cursor/SpaceXAI).

The uncommitted `README.md` diff satisfies the acceptance criteria. Protocol guidance still leads:
the host-preference framing, both fenced examples, and the model-list paragraph come first, and the
Bridge cross-reference follows them. Neither example gained a host-authority claim — their only
edit is the heading rename, they still close as routing preferences rather than execution
authority, and the surrounding prose still says a host declaration never authorizes Spartan to
start a host or round. The cross-reference is labelled optional and external (`optional runtime,
installed separately`; `nothing above depends on it`) and does not recast the protocol table as
authority. The two shapes are stated as separate axes — advisory routing preference versus
execution binding — and `must not be merged, nor one converted into the other`; they are not
presented as versions of each other. `README.md` has no standalone-line occurrence of
`The human starts every round.` `AGENTS.md` is byte-identical to `HEAD`; `agent-skill/` is
unchanged from `HEAD`; the only other dirty task artifact is the pre-existing `0027` modification,
left with the same status letters and `git diff --stat` line this review found. No defect in the
diff; no change requested.

Plan verdict: APPROVED (HX-003 plan re-review, Cursor on Grok 4.6, high effort; vendor attribution joint
Cursor/SpaceXAI). The restated file-scope criterion names `README.md` and this artifact as the only
files this task may change, declares the pre-existing uncommitted `0027` edits out of scope and not
to be reverted, staged, committed, or counted, and does not require a clean tree; a later
implementation round can check it from the artifact alone by comparing `git status --short` and
`git diff --stat` captured before its first edit and after its last, allowing only `README.md` to
appear as a new path and this artifact's own status entry to change, while `0027`'s status letters
and `git diff --stat` line stay identical. `git diff --name-only` still lists only `0027` and omits
this untracked artifact, so the restated pair is the check that separates this task's work from
working-tree state it did not create. No other part of the plan was reopened.

Prior verdict: CHANGES_REQUESTED (HX-001), addressed by the HX-002 restatement.

## Blockers

None.

## Next Action

None. Every acceptance criterion is satisfied, the relevant checks have recorded outcomes, the
implementation-review verdict is `APPROVED`, and no blocker remains.

## Next Handoff

No outstanding handoff. This task is complete.

Non-binding suggestion: commit the approved `README.md` change together with this artifact; leave
the pre-existing uncommitted `0027` edits unstaged.

```text
Recommended execution (human decides):
- Host: Cursor (this repository's `AGENTS.md` binds `implementer` to Cursor; the change is already reviewed)
- Model and effort: Composer 2.5, no user-selectable effort (routine commit of approved documentation)
- Role: implementer
- Invocation: `/spartan`, passing the prompt block below as the argument
```

```text
Create a new uniquely numbered task from `assets/task-template.md` in `spartan/tasks/`.

Act as implementer. Commit only `README.md` and `spartan/tasks/0028-reconcile-agent-hosts-heading-collision.md` using Conventional Commits. Do not stage, revert, or commit `spartan/tasks/0027-adopt-bridge-consumer-configuration.md`, and do not push. Succeed when those two in-scope paths are on HEAD and the `0027` artifact remains unstaged. Do not reopen or update `spartan/tasks/0028-reconcile-agent-hosts-heading-collision.md`.
Run the relevant repository checks and update the new task file.

Return only the next handoff, or a completion notice if no work remains.
```
