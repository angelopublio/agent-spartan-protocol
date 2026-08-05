---
protocol: "0.4.0" # x-release-please-version
id: handoff-envelope-integrity-and-identifier
created_at: 2026-08-03
status: completed
phase: complete
task_type: review
risk: material
current_role: implementer
next_role: human-operator
updated_at: 2026-08-05
---

# Handoff envelope integrity and manual handoff identifier

## Objective

A reviewable, backward-compatible design that gives every task artifact exactly one canonical
current handoff envelope and a compact monotonic handoff identifier, so a stale `Next Handoff` or a
stale pasted prompt is detectable by the receiving round instead of being applied silently.

## Context

Task 0006 closed the "who is next" drift by requiring three encodings of the role (frontmatter
`next_role`, advisory block, prompt `Act as <role>`) to agree and be regenerated as a unit. Two gaps
remain and are the subject of this task:

1. Agreement is required but nothing in the artifact makes a violation *detectable*. A `Next Handoff`
   block left over from an earlier round can silently contradict `next_role` and `Next Action`.
2. The handoff a human actually pastes is a chat artifact. Chat is ephemeral, so a human can paste an
   older response's prompt against an artifact that has already moved on, and the receiving round has
   no way to notice.

Notation survey for collision avoidance (evidence below): the repository's only established
identifier notations are the task filename `NNNN-slug.md` / `task-NNNN`, semantic versions, model
names (`GPT-5.x`), and the host tokens `$spartan` and `/spartan`. No `<PREFIX>-<digits>` handoff
notation exists yet, and `H-`, `HX-`, `SH-`, `HO-`, `HD-`, and `R-` are all currently unused, so the
prefix is a free choice rather than an inherited one.

The design-producing round was planning only. No protocol, skill, template, script, or test file has
been modified by either the planning or independent-review round.

## Scope

- This artifact: a complete, independently reviewable design for the canonical handoff envelope and
  the handoff identifier, with acceptance criteria, risks, an example envelope, and exact future
  implementation, review, and verification steps.

## Out of Scope

- Any edit to `agent-skill/SKILL.md`, `agent-skill/references/*`, `agent-skill/assets/*`, the
  repository README, or any script or test in this round.
- Retrofitting identifiers onto tasks 0001-0022 or reconstructing their handoff history.
- Any mechanism that transfers, schedules, watches, or executes a handoff.
- A global cross-task identifier sequence, which would require shared mutable state outside a single
  task artifact.

## Constraints

- Persisted content is English and ASCII-only; `SKILL.md` stays under 200 lines.
- Backward compatible: an artifact or a pasted prompt without an identifier must remain usable.
- Every scope test in `references/protocol.md` must still pass: no automatic transfer, scheduler,
  agent coordination, hidden state, append-only event log, database, lock, queue, or runtime state
  machine, and the deletion test must still hold.
- The task artifact stays a current-state snapshot, not a history of past handoffs.

## Acceptance Criteria

- [x] The design names one canonical current handoff envelope and states what makes a contradiction
      detectable in the persisted Markdown.
- [x] The design recommends one identifier prefix and format, justified by a collision survey rather
      than by assuming a prefix is free.
- [x] Increment semantics are defined for task creation, a newly issued handoff, a regenerated but
      unchanged handoff, and a receiving round that completes or issues the next handoff.
- [x] The exact placement of the last-accepted and next-proposed identifier is defined for the
      frontmatter, the artifact's `Next Handoff` section, the advisory block, and the pasteable prompt.
- [x] The receiving round's compare-before-change rule and its stop-and-report behavior on mismatch
      are specified.
- [x] The design separates deterministically checkable Markdown invariants from what cannot be checked
      because the final chat response is ephemeral, and states the mitigation for each.
- [x] The design records an explicit recommendation on an optional read-only consistency checker,
      with its trade-offs.
- [x] Migration for identifier-less existing tasks and identifier-less pasted prompts is specified
      without historical reconstruction.
- [x] Identifier semantics distinguish a regeneration that changes the pasteable prompt from an
      advisory-only correction, so a stale prompt can never carry a current identifier.
- [x] The envelope invariants are scoped to an artifact that has an outstanding proposal, and the
      no-proposal case (completed, blocked, or completion-notice suggestion) is defined explicitly.
- [x] Every accepted handoff preserves a monotonic high-water mark, including a round started from a
      prompt that carried no identifier.
- [x] The deterministic checks classify every artifact state exhaustively, so a partial two-field
      upgrade is rejected rather than escaping every branch, and the identifier format admits exactly
      the values the increment rules can allocate.
- [x] A valid handoff-envelope example, the risk list, and the exact implementation, independent
      review, and verification steps are recorded.
- [x] An independent review in a different vendor records an `APPROVE` verdict on this design.

## Decisions

### D1 - One canonical envelope, scoped to an outstanding proposal

An artifact has an **outstanding proposal** exactly when its `next_handoff_id` is not `none`. The
envelope invariants apply to that state and only to it.

- **Outstanding proposal.** The `## Next Handoff` section is the single canonical current envelope.
  The artifact MUST contain exactly one such section, holding exactly two fenced blocks (advisory,
  then prompt). The section is always regenerated whole; a superseded envelope is overwritten, never
  kept beside the new one.
- **No outstanding proposal** (`next_handoff_id: none` - a completed task, or a blocked task whose
  next move belongs to the human): the artifact MUST NOT carry a handoff envelope. The round that
  clears the proposal MUST remove **both** fenced blocks in the same edit that sets `none`. The
  section may then be omitted entirely, or retained with prose and no fenced block at all - one line
  stating that no handoff is outstanding is the usual form, and is what this repository's completed
  tasks already do (`0020`, `0016`). Removing one block of the pair and keeping the other is not a
  cleared proposal.
- **Completion-notice suggestion.** A completion notice MAY still record a suggested next round in
  two blocks, as `0015` does. That is a suggestion for a *new* round or task, not a current envelope:
  it MUST keep the same advisory-then-prompt pair shape, MUST be introduced by a line beginning
  `Non-binding suggestion`, and MUST NOT carry a handoff identifier anywhere in the section. This
  keeps the reading "an identifier is present if and only if a proposal is outstanding" exactly true.

In the no-proposal state, therefore, `## Next Handoff` has exactly two admissible shapes: **zero**
fenced blocks, or **exactly two** fenced blocks forming a labeled non-binding suggestion that points
elsewhere. Two further rules bind the section in both shapes. Both are literal rather than semantic,
so that the prose rule and the mechanical check cannot diverge:

- **No identifier anywhere in the section.** No `HX-NNN` occurrence at all between the
  `## Next Handoff` heading and the next heading - not in a marker line, not in prose, not inside
  either fenced block. A suggestion never needs an identifier, so a blanket ban costs nothing and
  removes every judgment about which occurrence "counts" as a live one.
- **One exact label.** The two-block shape MUST be introduced by a line beginning
  `Non-binding suggestion`. Wording a reader would accept as equivalent - `Optional non-binding
  suggestion`, `Completed - optional follow-up` - is a violation, because a label that cannot be
  recognized by string comparison gives back the ambiguity the label exists to remove.

Section prose is otherwise unconstrained: any wording, or none, is admissible as long as the section
carries no identifier and introduces a retained pair with that exact label line. Any other
arrangement of the fenced blocks - one leftover block, a third block, or a pair in the wrong order -
is residue rather than a cleared proposal, whether or not it still carries an identifier. D7 states
these same rules mechanically; the two are one rule written twice, and a change to either MUST
change both.

Everything a reader needs to judge currency is inside that one section plus the frontmatter.

### D2 - Detectability comes from mechanical agreement between fixed places

Three logical values are written in eight places, and a contradiction becomes detectable because the
copies of each value must agree:

| Value | Where it is written | Agreement test |
|---|---|---|
| Next role | frontmatter `next_role`; advisory `- Role:`; prompt `Act as <role>` | the three strings are equal |
| Proposed handoff id | frontmatter `next_handoff_id`; advisory `- Handoff:`; prompt `(handoff <id>)` on the `Open` line | the three strings are equal |
| Task file | the artifact's own filename; prompt `Open spartan/tasks/NNNN-slug.md` | the prompt path resolves to this artifact |

The task file is not a frontmatter field: its authoritative copy is the filename, and the prompt path
is compared against it. Role and identifier are the two values with a frontmatter copy.

`Next Action` remains prose and its agreement with the prompt's bounded action stays a semantic
judgment, not a string comparison. That is deliberate: the plan buys mechanical detection for the two
values that actually drifted in practice (role, currency) without forcing the artifact toward a
machine-readable schema.

### D3 - Prefix and format: `HX-NNN`, task-scoped

Recommended notation: `HX-` followed by three digits, zero-padded, starting at `HX-001`.

- `HX` is unused anywhere in the repository and collides with no existing notation, no controlled
  value, no role name, no host token, and no ordinary English word, so it greps cleanly.
- `H-` was rejected as too generic; `HO-` was rejected because it reads as `human-operator`; `R-`
  was rejected because it reads as "role" or "round"; `SH-` was rejected for its shell association.
- The counter is scoped to one task artifact, not global. A global sequence would need a counter
  shared across files - exactly the hidden mutable state the protocol forbids. A pasted prompt always
  names its task file, so the pair (task file, `HX-NNN`) is unambiguous. Where a fully qualified form
  helps in prose, write `0023/HX-002`.
- Three digits is enough for any realistic task; a task that exceeds `HX-999` widens the number
  rather than wrapping.

### D4 - Two frontmatter fields only

- `handoff_id`: the **high-water mark** - the highest identifier this task has ever accepted. `none`
  before the task has accepted any identified handoff.
- `next_handoff_id`: the identifier of the envelope currently proposed in `Next Handoff`. `none` when
  no proposal is outstanding (D1).

Only these two current values are stored. No list of past identifiers is kept, so the artifact does
not become an event log. Reading `handoff_id` as a high-water mark rather than as "the last one
pasted" is what makes the never-reuse rule hold in every path through D5 and D6, including a round
that was started without an identifier.

### D5 - Increment semantics

- **Task creation.** `handoff_id: none`. If the creating round issues a handoff, it sets
  `next_handoff_id: HX-001`. If it completes the task, `next_handoff_id: none`.
- **Newly issued handoff.** `next_handoff_id` = the higher of `handoff_id` and the previous
  `next_handoff_id`, plus one. Identifiers within a task increase monotonically and are never reused,
  decremented, or renumbered.
- **Regeneration: the identifier names the pasteable prompt text.** A round that rewrites its own
  outstanding proposal keeps `next_handoff_id` **only while the prompt block stays textually
  identical** to the persisted one. Any change to the prompt block - its `Open` path, its
  `Act as <role>` line, its bounded action or success condition, or any other wording inside it -
  MUST issue a higher identifier, because the human may already be holding the older prompt text and
  D6 must be able to reject it. A correction confined to the **advisory** block (recommended host,
  model and effort, reason phrase, invocation) keeps the identifier, because the human pastes only
  the prompt block and nothing they hold has gone stale. Note that a role change is never
  advisory-only: it rewrites `Act as <role>`, so it always issues a higher identifier and the
  advisory `- Role:` and `- Handoff:` lines are regenerated with it.
- **Receiving round.** On a matching identifier the round sets `handoff_id` to the pasted identifier.
  If it issues another handoff it sets `next_handoff_id` to the next value; if it leaves no
  outstanding proposal it sets `next_handoff_id: none` and removes the envelope (D1).
- **Clearing a proposal never lowers the mark.** Before setting `next_handoff_id: none`, a round MUST
  have raised `handoff_id` to the identifier it accepted (D6). A cleared proposal therefore leaves
  `handoff_id` at or above every identifier ever issued, and the next allocation is strictly above it.

### D6 - Compare before change

A round started from a pasted prompt that carries an identifier MUST, before making any change,
compare it with the artifact's `next_handoff_id`.

- **Equal:** proceed with the round.
- **Different:** stop. Make no change to the repository or the artifact. Report the pasted
  identifier, the artifact's current `next_handoff_id`, and the artifact's current `Next Action`, and
  recommend re-running from the artifact's own current envelope. A stale handoff is never applied and
  is never "merged" with the current one.
- **Pasted identifier absent while the artifact proposes one:** do not hard-stop. The artifact is
  handoff truth, so the round proceeds under the artifact's current envelope. Hard-stopping here
  would break hand-written prompts for no integrity gain. Proceeding under the artifact's envelope
  **is acceptance of it**: the round sets `handoff_id` to the artifact's `next_handoff_id`, exactly as
  a matching paste would, and separately records in Work Completed that the pasted prompt carried no
  identifier. Without that acceptance the high-water mark would lag, and a round that then blocked
  and cleared the proposal would let a later round re-allocate an identifier a human already holds.
- **Both absent:** proceed as today (backward compatible), and leave both fields absent unless the
  round issues a newly generated handoff (D9).

The check is a reading rule for the current host. Nothing enforces it, and nothing blocks, queues, or
retries the round.

### D7 - Ephemeral chat versus persisted Markdown

Deterministically checkable in the persisted artifact. Which checks apply depends on the artifact's
state, so the state is decided first, and it is decided by the **presence** of the two frontmatter
fields rather than by their values. The three cases below are exhaustive and mutually exclusive:
every artifact lands in exactly one of them, and none escapes every check.

**Legacy artifact** (neither `handoff_id` nor `next_handoff_id` present): none of the new checks
apply. Only the pre-existing three-place role agreement from task 0006 does. See D9.

**Partial frontmatter upgrade** (exactly one of the two fields present, whatever its value): a
violation in itself, reported as "partial frontmatter upgrade" and naming the missing field. No
envelope branch is selected and no envelope check is run, because the artifact's state is
undetermined - one field alone cannot say whether a proposal is outstanding. D9 makes the upgrade a
single atomic edit precisely so this state never exists at the end of a round, so its only source is
an interrupted or partial edit. The repair is to finish that same edit - both fields plus the whole
`Next Handoff` section regenerated under the new rules - never to add the missing field alone and
leave the old envelope lines standing. The concrete case is `handoff_id: none` with
`next_handoff_id` absent and a legacy envelope still present: it is not legacy, because a field is
present, and not identified, because a field is missing, and this branch is what rejects it.

**Identified artifact** (both fields present): each field's value MUST be `none` or a canonical
identifier, and the branch is then selected by `next_handoff_id`.

**Canonical identifier form.** An identifier is `HX-` followed by a digit run that is either exactly
three digits or four-or-more digits whose first digit is not `0`, and whose numeric value is at least
1 - that is, `HX-([0-9]{3}|[1-9][0-9]{3,})` with `HX-000` excluded. This is D3's format stated
mechanically. It admits `HX-001` through `HX-999` and the widened `HX-1000` onward, and rejects
`HX-000`, which D5 never allocates because the sequence starts at `HX-001`; `HX-0001` and `HX-00001`,
which pad a value that is already canonical as `HX-001`; and `HX-1`, `HX-01`, `HX-1a` and any other
run shorter than three digits or not wholly digits. One value therefore has exactly one spelling,
which is what lets D2 compare the three copies of the identifier as strings.

Ordering is numeric on the digit run, not lexical, so `HX-1000` is above `HX-999`. Canonical padding
makes the two readings agree at every equal width; the widened form after `HX-999` is the only place
they diverge, and the numeric reading is the one D5 means by "monotonically".

**Identified artifact with an outstanding proposal** (`next_handoff_id` is not `none`):

- exactly one `## Next Handoff` section, with exactly two fenced blocks;
- `next_handoff_id` matches the identifier in both blocks;
- `next_role` matches the advisory `- Role:` and the prompt's `Act as <role>`;
- the prompt's `Open` path matches the artifact's own filename;
- `next_handoff_id` is numerically greater than `handoff_id`, or `handoff_id` is `none`;
- every identifier in the frontmatter and in both blocks is in canonical form.

**Identified artifact with no outstanding proposal** (`next_handoff_id: none`): these checks read
only the `## Next Handoff` section, from its heading to the next heading. The rest of the artifact is
prose, and an `HX-NNN` mention in Decisions, Evidence, Risks, Work Completed, or an example is never
a violation - this design's own "Example envelope" is exactly such a mention. What the checks
constrain is that one section, not the notation elsewhere.

- **Section shape: zero or exactly two fenced blocks.** Count the fenced blocks under
  `## Next Handoff`, counting each outermost fence as one block so that a wrapper fence around a
  quoted example counts once. Zero blocks is a cleared proposal and needs no further *shape* check;
  the section-wide identifier check below still applies to its prose, so a zero-block section that
  mentions an identifier is a violation. Exactly two is admissible only as a completion-notice
  suggestion, and the first block MUST
  begin with `Recommended execution` and the second with an `Open` or `Create` line; such a section
  MUST also pass the three checks below. Every other count or order - one leftover block, three or
  more, prompt before advisory - is a violation, reported as "partial or extra envelope residue".
  This check is what rejects a half-removed envelope, which carries no identifier marker and is not
  an unlabeled pair, and so passes every other check on this list.
- **No identifier anywhere in the section.** No occurrence of `HX-[0-9]{3,}` between the
  `## Next Handoff` heading and the next heading, including inside the fenced blocks. Under D5 a live
  envelope carries its identifier in a `- Handoff:` line and a `(handoff HX-...)` parenthetical, so
  those are the occurrences that arise in practice; banning the notation outright in this one section
  is a strict superset of rejecting those two, needs no judgment about which occurrence is live, and
  is D1's rule stated in the same words. It is also what forbids an identifier inside a non-binding
  suggestion.
- **A retained block pair must carry the exact label.** An advisory-and-prompt pair MAY remain under
  `## Next Handoff` only as D1's completion-notice suggestion, and the section MUST then introduce it
  with a line beginning `Non-binding suggestion`. The prefix is literal: a pair introduced by other
  wording fails this check even when a reader would accept the wording as non-binding, which is
  exactly what D1 requires. An unlabeled or differently labeled pair is the violation, and it is
  reported as "unlabeled block pair", never as "block pair present".
- **A suggestion points elsewhere.** The suggestion prompt's `Open` or `Create` line MUST NOT name
  this artifact's own filename, restating as a check the protocol's existing rule that a completion
  suggestion starts a new task or names another `active` one.

The four checks together admit exactly the two section shapes D1 allows and nothing else. The shape
check fixes the admissible block counts, and the identifier check reads the whole section in both
shapes, so it constrains a zero-block section exactly as it constrains a two-block one. Only the
label and points-elsewhere checks are specific to the two-block shape, where they separate the two
pairs that could otherwise occupy it - a current envelope carries an identifier and opens this file,
a suggestion carries neither and is labeled. Apart from the identifier ban and the label line, prose
is unconstrained in both shapes, so an identifier-free status line and a section reduced to nothing
are equally valid, and an `HX-NNN` mention outside `## Next Handoff` is still never read.

Not checkable, because the final chat response is ephemeral and unversioned:

- whether the response the human actually received matched the artifact at that moment;
- which of several past responses the human pasted.

Two mitigations, in order:

1. **Write, then copy.** A round MUST write the envelope into the artifact *first*, then reproduce
   that persisted envelope verbatim in its final response. The response quotes the artifact; it is
   never authored independently of it. This makes the artifact the source and the chat a copy, and it
   is what gives D5's regeneration rule a definite referent: the identifier names the prompt text as
   persisted, so "the prompt block changed" is decidable against the file rather than against memory
   of a past response.
2. **The receiving round is the practical detector.** Because the identifier travels inside the
   pasted prompt, a stale paste is caught at the only point where it matters - before any change is
   made. Detection is at consumption time, not at production time, which is the only place a
   human-mediated protocol can put it without adding runtime state.

### D8 - Optional checker: recommend against it, for now

Recommendation: implement the invariant purely in the protocol text and the task template; do not add
a checker script in this round or as part of this feature.

- `AGENTS.md` forbids executable task tooling without an explicit decision to build a separate
  product, and the repository has no manifest, runtime, or test harness to host it.
- The deletion test is decisive: with a script, the invariant would still hold if the script were
  deleted, so the script is not the invariant - it is at best convenience.
- Portability is weak: a checker binds the protocol to one language runtime that adopting
  repositories may not have.

If a checker is ever added, it belongs outside the protocol package and MUST be optional, read-only,
and snapshot-only: it reads one task file, prints findings, exits, and holds no state between runs. It
MUST NOT write files, resolve conflicts, schedule, watch, poll, or select the next round. The
identifier's mechanical shape (D2, D7) is deliberately simple enough that a human or a host can also
verify it by reading, which is the intended check.

### D9 - Migration without reconstruction: the legacy exception is the whole envelope shape

A **legacy artifact** is one whose frontmatter carries neither `handoff_id` nor `next_handoff_id`.
The exception covers the entire old envelope shape, not just the missing fields:

- A legacy artifact keeps its old envelope exactly as written: no advisory `- Role:` line, no
  `- Handoff:` line, no `(handoff HX-NNN)` parenthetical on the `Open` line. None of D1's, D2's, or
  D7's new invariants apply to it; only task 0006's three-place role agreement does, unchanged. A
  legacy artifact is therefore valid as it stands, and a reader must not report its missing lines as
  a violation.
- **The upgrade point is a single atomic edit.** The first round that issues a *newly generated*
  handoff on a legacy artifact adds, in that one edit, both frontmatter fields and every new envelope
  line, and regenerates the whole `Next Handoff` section under the new rules. It sets
  `handoff_id: none`, because the handoff it was started from carried no identifier, and
  `next_handoff_id: HX-001`. No prior handoff is reconstructed or back-numbered. There is no
  intermediate state in which an artifact has the fields but the old envelope lines, or the reverse.
- A round that only reads a legacy artifact, or that completes or blocks it without issuing a
  handoff, adds nothing. It stays legacy.
- Completed tasks are never retrofitted. A legacy completed artifact that still holds a full
  two-block envelope - `0018` in this repository is one - is left exactly as it is. D1's removal rule
  applies only to identified artifacts, so it never reaches back into legacy history, and the absence
  of an identifier there is precisely what marks the envelope as not current.
- The template gains the two fields with `none` defaults, so new tasks are identified from creation
  while old ones stay valid.

## Work Completed

- Read the applicable repository instructions (`AGENTS.md`), `agent-skill/SKILL.md`,
  `references/protocol.md`, `references/routing.md`, and the task template before planning.
- Surveyed existing identifier notation across the repository to choose a non-colliding prefix.
- Reviewed task 0006 (handoff consistency and supersession) to build on its three-place role rule
  rather than restate or contradict it.
- Produced the design above: canonical envelope, `HX-NNN` identifier, increment semantics, placement,
  compare-before-change rule, checkable/uncheckable split, checker recommendation, and migration.
- Iterated the design through six independent cross-vendor reviews in Codex with GPT-5.6 Sol, each
  read-only and each recording `CHANGES`, alternating with revision rounds in Claude Code with Claude
  Opus in the planning role. No round modified any file outside this artifact. The findings resolved
  along the way, and where each now lives: the identifier names the pasteable prompt text, so a
  changed prompt always issues a higher one (D5); the envelope invariants are scoped to an
  outstanding proposal, with the no-proposal and completion-suggestion cases defined (D1, D4, D7);
  `handoff_id` is a high-water mark that an identifier-less start also raises (D4, D6); the legacy
  exception covers the whole envelope shape and upgrades in one atomic edit (D9); D2's miscount and
  its placement of the filename were corrected; and four divergences between D1's prose and D7's
  mechanical restatement were closed in turn - block cardinality and ordering, section scope, the
  identifier ban widened from the two canonical markers to the notation itself, the exact
  `Non-binding suggestion` label, and the zero-block branch that had stopped short of the
  section-wide identifier check. D3 and D8 have passed review unchanged throughout.
- The sixth re-review confirmed the zero-block counterexample resolved with no regression, and raised
  two deterministic-validation gaps: D7 classified no artifact state for a partial two-field upgrade,
  and its format check admitted `HX-000`.
- Revised D7 and the verification plan against both, in Claude Code with Claude Opus, planning role.
  D7 now decides the artifact state first, by the presence of the two frontmatter fields rather than
  their values, in three exhaustive and mutually exclusive cases: legacy (neither present), partial
  frontmatter upgrade (exactly one present, a violation in itself that selects no envelope branch),
  and identified (both present, then branched on `next_handoff_id` as before). It also states D3's
  format mechanically as a canonical form that rejects `HX-000` and non-canonical padding while still
  admitting the widened run after `HX-999`, applies that form to the frontmatter values as well as
  the envelope copies, and reads the ordering comparison numerically. D1, D3, D5, and D9 were not
  changed: D7 now states mechanically what they already require. Verification steps 7 and 8 were
  added for the two counterexamples and their accepted neighbours, renumbering the legacy and
  scope-test steps to 9 and 10. Work Completed and Evidence were compacted, dropping the
  round-by-round revision log now that its findings are recorded as resolved. No file outside this
  artifact was modified.
- Completed the final independent re-review in Codex with GPT-5.6 Sol. The three-case state
  classification, canonical identifier form, verification steps 7 and 8, and the whole design are
  approved; no earlier finding regressed and no file outside this artifact was modified.
- Implemented the approved design, in Claude Code with Claude Sonnet 5, implementer role. All six
  future-implementation steps are complete: `agent-skill/assets/task-template.md` gained
  `handoff_id: none` and `next_handoff_id: none` in frontmatter, `- Role:` and `- Handoff:` in the
  advisory block, and the `(handoff HX-NNN)` parenthetical on the prompt's `Open` line;
  `agent-skill/references/protocol.md` documents the two fields and the canonical `HX-NNN` format in
  "Task artifact contract", and a new "Handoff identifier" subsection under "Handoff contract" states
  the canonical-envelope rule scoped to an outstanding proposal, the placement table, increment
  semantics (prompt-text regeneration rule and high-water mark), compare-before-change with
  identifier-less acceptance, write-then-copy, and the legacy exception with its atomic upgrade point;
  `agent-skill/SKILL.md` gained a concise "Handoff identifier" mirror (87 lines total, under the
  200-line limit); `agent-skill/assets/spartan-readme.md` and the repository `README.md` each gained
  one sentence on the identifier without restating the protocol; no checker script was added (D8).
- This task (0023) itself is a legacy artifact - neither `handoff_id` nor `next_handoff_id` was ever
  present in its frontmatter - and this round completes it without issuing a handoff, so per D9 it
  stays legacy and gains neither field; its `## Next Handoff` section is cleared to a status line with
  no envelope, matching the no-proposal rule for a task with no outstanding identifier state to carry.

## Evidence

- `grep -rnoE '\b[A-Za-z]{1,5}-[0-9]{1,4}\b' --include='*.md'` over `agent-skill`, `spartan`,
  `docs`, `README.md`, `AGENTS.md`: only `GPT-5` (22), `task-0015` (3), `SHA-256` (2). No handoff
  identifier notation exists.
- `grep -rocE '\b(H|HX|SH|HO|HD|R)-[0-9]'` over all Markdown: zero matches for every candidate
  prefix, so `HX-` is free.
- `grep -rniE 'handoff[- ]?(id|number|identifier|envelope)|round[- ]?(id|number)'`: only two prose
  uses of "envelope" in task 0003, none normative and none an identifier.
- `agent-skill/assets/task-template.md`: `Next Handoff` is the only handoff section and already holds
  exactly the two fenced blocks this design makes canonical; `wc -l agent-skill/SKILL.md` = 83, so the
  planned mirror has ample room under the 200-line limit.
- `references/protocol.md` "Handoff contract" already requires three-place role agreement and
  regeneration as a unit; this design adds detectability to that rule rather than replacing it.
- No automated test suite exists; the applicable checks for this documentation-only design are the
  static checks and plugin-manifest validation recorded here.
- Envelope-state survey supporting D1 and D9: over `spartan/tasks/*.md`, 22 of 23 tasks are
  closed, and their `## Next Handoff` sections are inconsistent - 12 omit the section, 8 keep it with
  a "None" line (`0016`, `0020`), `0015` keeps a labeled non-binding suggestion, and `0018` still
  holds a full two-block envelope under a completed task. That last case is the stale
  canonical envelope the review flagged, and it is exactly what D1 forbids going forward and D9
  leaves untouched in the past.
- Self-test of the section-scoped checks against existing history, re-run this round. An `awk` pass
  counting fence lines between `## Next Handoff` and the next `##` heading over `spartan/tasks/*.md`
  returns 4 fence lines (two blocks) for `0015`, `0018`, and `0023` and 0 for the other 20, so every
  artifact lands on an admissible shape - `0023` a current envelope, `0015` a completion suggestion,
  `0018` a legacy completed envelope covered by D9 - and none holds a one-block or three-block
  section. The same pass printing `HX-[0-9]` occurrences inside those sections returns no line, so
  the widened identifier ban adds no violation either, including to `0015`'s retained suggestion and
  `0018`'s legacy envelope. This artifact's own `HX-` mentions all sit in Context, Decisions, Work
  Completed, Evidence, the Example envelope, and the future implementation and verification steps;
  the pre-scoping check rejected it, and the section-scoped one does not.
- Known consequence of the exact label rule: `0015` introduces its retained pair with "Completed - no
  binding handoff. Optional follow-up below (human decides)", not with a line beginning
  `Non-binding suggestion`. `0015` is legacy under D9, so the rule does not reach it; the exact label
  wording binds only artifacts created after the implementation round updates the template.
- Resolution of the two re-review counterexamples, chosen so that D1 and D7 state one rule each in
  the same words. Identifier: both now forbid any `HX-NNN` occurrence anywhere under
  `## Next Handoff`, which is the wider of the two former rules and closes the case of an identifier
  written outside the two canonical markers. Label: both now require the literal `Non-binding
  suggestion` prefix, which is the narrower of the two former rules, and D1's "prose is
  unconstrained" sentence was scoped so that it no longer contradicts it. Verification step 6 now
  exercises both boundaries in each direction.
- Resolution of the zero-block counterexample (`next_handoff_id: none`, a `## Next Handoff` section
  with zero fenced blocks and the prose `Prior handoff HX-001.`). All three locations reject it for
  the same reason: D1 binds the identifier ban "in both shapes"; D7's shape branch exempts the
  zero-block case from a further *shape* check only, and its closing paragraph names the identifier
  check as binding both shapes; and verification step 6 requires "no `HX-NNN` occurrence at all" and
  exercises this artifact directly.
- Resolution of the partial-upgrade counterexample (`handoff_id: none`, `next_handoff_id` absent, the
  legacy envelope still present). D7 now selects the state by field *presence* before reading any
  value, so this artifact is neither legacy nor identified but lands in the third case and is
  rejected as a partial frontmatter upgrade. The three cases partition on the number of fields
  present (0, 1, 2), so they are exhaustive by construction and the mirrored artifact - only
  `next_handoff_id` present - is rejected identically. D9 is unchanged; D7 states its atomic-edit
  requirement as a check, the same relationship D7 already has with D1.
- Resolution of the `HX-000` counterexample (`handoff_id: none`, `next_handoff_id: HX-000`, matching
  copies in both blocks - which agree, so only a format check can reject it). Self-test of the
  canonical form `^HX-([0-9]{3}|[1-9][0-9]{3,})$` with `HX-000` excluded, run over boundary values:
  accepts `HX-001`, `HX-999`, `HX-1000`, `HX-1234`; rejects `HX-000`, `HX-0001`, `HX-00001`,
  `HX-0999`, `HX-01`, `HX-1`, `HX-1a`. The admitted set is exactly the set D5 can allocate from
  `HX-001` upward, and the widened form after `HX-999` survives.
- Self-test of the state classification against existing history: `grep -rln 'handoff_id'
  spartan/tasks/` matches only this artifact, and there only in prose, so no task carries either
  frontmatter field yet. Every existing task is legacy under D9, none is a partial upgrade, and the
  new branch adds no violation to history.
- The check set has passed unchanged in every round of this task since the design was drafted
  (revision rounds in Claude Code and independent re-reviews in Codex on 2026-08-04 and 2026-08-05):
  `git diff --check` clean, no non-ASCII content, `wc -l agent-skill/SKILL.md` = 83, and both plugin
  validations passing.
- Checks for this D7 revision on 2026-08-05, in Claude Code with Claude Opus: `git diff --check`
  clean; the untracked task's `git diff --no-index --check /dev/null ...` produced no whitespace-error
  output (exit 1 only because the file differs from `/dev/null`);
  `LC_ALL=C grep -rnP '[^\x00-\x7F]'` found no non-ASCII content in this artifact or `agent-skill/`;
  `wc -l agent-skill/SKILL.md` = 83; `claude plugin validate --strict .` and
  `claude plugin validate --strict agent-skill` both passed. `git status --short` still lists only
  this task and unrelated `.DS_Store` files as untracked, so per `AGENTS.md` no `git pull --ff-only`
  was run.
- Final independent-review checks on 2026-08-05, in Codex with GPT-5.6 Sol: `git diff --check` and
  the untracked task's `git diff --no-index --check` reported no whitespace errors; the ASCII scan
  returned no matches; `wc -l agent-skill/SKILL.md` = 83; and `claude plugin validate --strict .`
  plus `claude plugin validate --strict agent-skill` both passed. Direct boundary self-tests classify
  zero, one, and two present fields as legacy, partial-upgrade violation, and identified respectively;
  reject both one-field counterexamples; accept `HX-001`, `HX-999`, and `HX-1000`; reject `HX-000`,
  padded, short, and non-digit forms; and compare `HX-1000` above `HX-999` numerically.
- Implementation verification, 2026-08-05, in Claude Code with Claude Sonnet 5 (steps 1-10 of
  Verification steps, all passing):
  1. `wc -l agent-skill/SKILL.md` = 87, under 200.
  2. `grep -rlP '[^\x00-\x7F]' agent-skill/` returned no match.
  3. The template's frontmatter `next_role: implementer`, advisory `- Role: implementer`, and prompt
     `Act as implementer` agree; frontmatter `next_handoff_id: none` is the placeholder state and the
     advisory `- Handoff: HX-NNN` / prompt `(handoff HX-NNN)` placeholders agree with each other.
  4. Dry run on this task's own upgrade point: a scratch envelope with `handoff_id: none`,
     `next_handoff_id: HX-001` and matching advisory/prompt copies confirmed agreement in all three
     placement rows and that `HX-001` exceeds `none`.
  5. Dry run of the regeneration rule on a scratch prompt: an advisory-only host edit left the prompt
     block byte-identical (identifier unchanged); a one-word edit inside the prompt block changed the
     persisted text (identifier must rise in frontmatter, advisory, and prompt).
  6. Dry run of every no-proposal shape with `awk`/`grep` scoped to `## Next Handoff`: zero fences with
     no identifier (accepted); a labeled two-block suggestion pointing at another task (accepted); the
     same suggestion mislabeled `Optional non-binding suggestion` (0 matches for the literal prefix,
     rejected as unlabeled); the same suggestion with `HX-001` added to the prompt prose (rejected,
     1 occurrence); a zero-block section with `Prior handoff HX-001.` in prose (rejected on the
     identifier rule despite an admissible shape); a one-outermost-block residue (rejected, count is
     neither 0 nor 2); this task's own "Example envelope" mentions were confirmed outside the
     `## Next Handoff` scan and so never flagged.
  7. Dry run of frontmatter state classification: one field present (either `handoff_id` alone or
     `next_handoff_id` alone) is a partial-upgrade violation in both directions; zero present is
     legacy; two present is identified and branches on `next_handoff_id`, unchanged.
  8. Dry run of the canonical identifier regex `^HX-([0-9]{3}|[1-9][0-9]{3,})$` excluding `HX-000`
     over `HX-001, HX-999, HX-1000, HX-1234` (accept) and `HX-000, HX-0001, HX-00001, HX-0999, HX-01,
     HX-1, HX-1a` (reject); the same rule rejects `handoff_id: HX-000` regardless of
     `next_handoff_id`; ordering compares digit runs numerically (`HX-1000` > `HX-999`).
  9. `spartan/tasks/0021-*.md` (status `completed`) has neither frontmatter field and zero fence lines
     under `## Next Handoff` - legacy, no envelope, not a violation.
     `spartan/tasks/0018-*.md` (status `completed`) has neither field and 4 fence lines (a full
     two-block envelope) - legacy per D9, left as-is, not a violation.
  10. Re-read "Scope admission test" in `references/protocol.md`: all seven questions and the deletion
      test still read yes for this change, which added only Markdown protocol text, template fields,
      and prose - no script, daemon, queue, lock, or network dependency.
  - `git diff --check` clean; `git status --short` shows only the five edited files and this task plus
    unrelated `.DS_Store` files as untracked; `claude plugin validate --strict .` and
    `claude plugin validate --strict agent-skill` both passed.

## Example envelope

The canonical `Next Handoff` section for a task at `spartan/tasks/0031-rate-limit-headers.md` whose
frontmatter carries `next_role: implementer`, `handoff_id: HX-002`, `next_handoff_id: HX-003`:

````text
```text
Recommended execution (human decides):
- Host: Codex, implementation and testing round
- Model and effort: GPT-5.6 Terra, reasoning effort high
- Role: implementer
- Handoff: HX-003
- Invocation: `$spartan`, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/0031-rate-limit-headers.md` (handoff HX-003).

Act as implementer. Add the retry-after header to the rate-limit response and make the existing
rate-limit tests pass unchanged.
Run the relevant repository checks and update the same task file.

Return only the next handoff, or a completion notice if no work remains.
```
````

The advisory block gains two lines (`Role:`, `Handoff:`); the prompt block gains only the
parenthetical identifier on its `Open` line, so it stays free of execution metadata and remains
pasteable unchanged.

## Risks

- **Ritual without benefit.** If rounds increment the identifier mechanically but do not compare it,
  the field is noise. Mitigation: the compare-before-change rule (D6) is normative and stated in
  `SKILL.md`, not only in the protocol reference.
- **Mis-scoped regeneration.** The identifier is preserved on the wrong regeneration in either
  direction: bumping it after an advisory-only fix makes an already-delivered correct prompt look
  stale, while keeping it after the prompt text changed lets a stale paste pass D6 - the failure the
  review caught. Mitigation: D5 draws the line at the prompt block itself, which is the only text the
  human holds, and D7's write-then-copy rule makes "the prompt block changed" decidable against the
  persisted file.
- **Identifier churn.** Under D5 a plan that is corrected several times before being handed over
  consumes several identifiers. Mitigation: accepted deliberately. Identifiers are cheap and
  non-contiguous by design, and the alternative reintroduces the stale-paste hole.
- **False stop.** A human who edits a pasted prompt by hand may drop or mistype the identifier.
  Mitigation: only a *conflicting* identifier stops the round; an absent one proceeds under the
  artifact (D6).
- **Schema creep.** Two new frontmatter fields invite more. Mitigation: the design stores exactly two
  current values and forbids a history list; any future field faces the scope admission test.
- **Instruction budget.** `SKILL.md` must mirror the rule concisely. Mitigation: the mirror is
  budgeted at roughly ten lines; the full semantics live in `references/protocol.md`.
- **Advisory-block growth.** The advisory block now carries five lines. Mitigation: `Role:` is a
  restatement the contract already required in prose, so the block gets more checkable, not longer in
  substance.

## Review

Verdict: APPROVE

Findings (Codex independent-reviewer, GPT-5.6 Sol, fresh cross-vendor context):

- PASS - D7 selects state solely by the number of identifier fields present: zero is legacy, one is
  an immediate partial-upgrade violation naming the missing field, and two is identified. The cases
  are exhaustive and mutually exclusive, and neither one-field counterexample reaches an envelope
  branch.
- PASS - The canonical form admits exactly `HX-001` through `HX-999` and the unpadded widened sequence
  from `HX-1000` onward. It deterministically rejects `HX-000`, redundant padding, short forms, and
  non-digit suffixes in frontmatter as well as envelope copies, while ordering uses the numeric digit
  run.
- PASS - Verification steps 7 and 8 exercise both counterexamples, their mirrored or boundary cases,
  frontmatter reach, the atomic-upgrade requirement, and numeric ordering with deterministic expected
  outcomes.
- PASS - Whole-design review found no regression in the earlier resolutions: prompt-text changes
  allocate a new identifier; the high-water mark is preserved; legacy migration remains atomic; and
  the no-proposal rules still reject partial or reordered envelopes, identifiers in either admissible
  section shape, non-exact labels, and suggestions pointing to the completed artifact.
- PASS - The design remains static, host-neutral, repository-local, human-mediated, and passes all
  seven scope-admission questions including the deletion test. No checker or runtime mechanism is
  introduced.

## Blockers

None.

## Next Action

None. All six implementation steps are complete and verification steps 1-10 passed without adding a
checker script; the task is closed.

## Future implementation steps

To be performed only after this design is approved, in one implementation round:

1. `agent-skill/assets/task-template.md`: add `handoff_id: none` and `next_handoff_id: none` to the
   frontmatter, add `- Role:` and `- Handoff:` to the advisory block, and add the `(handoff HX-NNN)`
   parenthetical to the prompt's `Open` line.
2. `agent-skill/references/protocol.md`, "Task artifact contract": document the two fields and the
   `HX-NNN` format.
3. `agent-skill/references/protocol.md`, "Handoff contract": add the canonical-envelope rule scoped
   to an outstanding proposal and the removal rule when there is none (D1), the placement table (D2),
   increment semantics including the prompt-text regeneration rule and the high-water mark (D4, D5),
   compare-before-change with identifier-less acceptance (D6), the write-then-copy rule (D7), and the
   legacy exception with its atomic upgrade point (D9).
4. `agent-skill/SKILL.md`: mirror concisely - one canonical `Next Handoff` section while a proposal is
   outstanding and none when it is not, the identifier and where it appears, increment semantics with
   the rule that a changed prompt block always gets a new identifier, compare-before-change with
   stop-and-report, and write-then-copy. Keep the file under 200 lines.
5. `agent-skill/assets/spartan-readme.md` and the repository `README.md`: one sentence each on the
   identifier, only if it can be added without restating the protocol.
6. Do not add a checker script (D8).

## Verification steps

1. `wc -l agent-skill/SKILL.md` is under 200.
2. `grep -rlP '[^\x00-\x7F]' agent-skill/` returns no match (ASCII only).
3. The template's frontmatter, advisory block, and prompt block carry the identifier in all three
   specified places and agree with each other.
4. A dry run on this task file: regenerate its envelope under the new rules and confirm the frontmatter,
   advisory block, and prompt agree, and that `next_handoff_id` is greater than `handoff_id`.
5. A dry run of the regeneration rule: rewrite only the advisory host and model and confirm the
   identifier is unchanged; then change one word inside the prompt block and confirm the identifier
   rises in all three places.
6. A dry run of the no-proposal rule, covering every section shape D1 admits or forbids and both
   content rules at their boundary. Take an identified task to completion and confirm that the same
   edit which sets `next_handoff_id: none` leaves `## Next Handoff` with zero fenced blocks and no
   `HX-NNN` occurrence at all. Then confirm that:
   - a retained completion suggestion is exactly two fenced blocks, advisory first and prompt second,
     introduced by a `Non-binding suggestion` line, carrying no identifier anywhere in the section,
     and not opening the completed artifact;
   - a section left with one fenced block, with three or more, or with the prompt block before the
     advisory block is reported as partial or extra envelope residue, even though it carries no
     identifier and is not an unlabeled pair;
   - the same accepted suggestion is rejected once an `HX-NNN` is added anywhere in the section -
     in its prose, inside the advisory block, or inside the prompt block - and not only when the
     identifier appears as a `- Handoff:` line or a `(handoff HX-...)` parenthetical; removing that
     identifier restores acceptance;
   - a zero-block section is rejected on that same identifier rule when it retains prose such as
     `Prior handoff HX-001.`, even though its block count is admissible; passing the section-shape
     check is never by itself acceptance;
   - the same accepted suggestion is rejected when its label line reads `Optional non-binding
     suggestion` or `Completed - optional follow-up` rather than beginning `Non-binding suggestion`,
     and is reported as an unlabeled block pair; restoring the exact prefix restores acceptance;
   - an identifier-free section reduced to a status line and a section omitted entirely are both
     accepted, matching D1's prose wording exactly;
   - an `HX-NNN` mention elsewhere in the same artifact - for example this task's "Example envelope",
     whose wrapper fence lies outside `## Next Handoff` - is not reported as a violation.
7. A dry run of the frontmatter state classification, confirming the three cases are exhaustive and
   that a partial upgrade is rejected rather than falling through every branch. Take the
   counterexample recorded in Review - `handoff_id: none`, `next_handoff_id` absent, legacy envelope
   lines still present - and confirm it is reported as a partial frontmatter upgrade naming the
   missing field, and that no envelope check is claimed to apply to it. Confirm the mirrored case
   (`next_handoff_id: HX-001` present, `handoff_id` absent) is rejected the same way. Then confirm
   that adding the missing field alone does not clear the finding unless the same edit also
   regenerates the whole `Next Handoff` section under the new rules (D9), and that the two accepted
   cases are unchanged: both fields absent is still legacy, and both fields present is still branched
   on `next_handoff_id` exactly as before.
8. A dry run of the canonical identifier form. Confirm the counterexample recorded in Review -
   `handoff_id: none`, `next_handoff_id: HX-000`, matching `HX-000` in both envelope blocks - is
   rejected on format, and is reported as a non-canonical identifier rather than as a disagreement
   between the three copies, which do agree. Confirm that `HX-0001`, `HX-01`, `HX-1`, and a run that
   is not wholly digits are rejected; that `HX-001`, `HX-999`, and `HX-1000` are accepted; and that
   the rule reaches the frontmatter values too, so `handoff_id: HX-000` is rejected even while
   `next_handoff_id` is `none`. Finally confirm the ordering check reads the digit run numerically:
   `next_handoff_id: HX-1000` over `handoff_id: HX-999` passes, while `HX-999` over `HX-1000` fails.
9. Confirm an untouched legacy task (for example `spartan/tasks/0021-*.md`) still satisfies the
   protocol with no identifier fields and no new envelope lines, and that a legacy completed task that
   still holds a full envelope (`spartan/tasks/0018-*.md`) is not reported as a violation.
10. Re-read the scope admission test in `references/protocol.md` and confirm all seven answers stay
    yes, including the deletion test.

## Next Handoff

No handoff outstanding. This task is completed; see the suggestion below for possible follow-up.
