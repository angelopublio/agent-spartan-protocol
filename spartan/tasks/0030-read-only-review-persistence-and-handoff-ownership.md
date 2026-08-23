---
protocol: "1.0.0" # x-release-please-version
id: read-only-review-persistence-and-handoff-ownership
created_at: 2026-08-23
status: completed
phase: complete
task_type: implementation
risk: high-impact
current_role: reviewer
next_role: none
updated_at: 2026-08-23
handoff_id: HX-003
next_handoff_id: none
---

# Define read-only review persistence and handoff ownership

## Objective

The protocol states, in one place and without contradiction, who produces a review result, who
persists it, and who advances the handoff envelope. A technically read-only reviewer can satisfy
every normative requirement addressed to it without writing a single file, and a reader can tell from
a generated prompt alone whether the round it addresses is expected to write.

This artifact settled that design and then applied it.

## Context

The package currently issues two incompatible instructions to the same round. `references/protocol.md`
says a review round `MUST record exactly one explicit verdict` (line 181) and, two lines later, that a
reviewer `SHOULD remain read-only` (line 183). `MUST record` outranks `SHOULD remain read-only`, so a
reviewer that cannot write is in violation of the protocol for behaving exactly as the protocol
prefers. `SKILL.md` line 47 compresses the same contradiction into one sentence.

The contradiction is not confined to the review rules. The handoff contract requires every generated
prompt to `require the same task file to be updated` (line 205), the identifier rules require a
receiving round to set `handoff_id` `before doing anything else` (line 252) and to write the envelope
into the artifact before reproducing it (line 256), and `references/routing.md` line 59 requires
`Every round` to record the model it ran. Each of those is a write, and each is addressed to review
rounds too.

The gap is now load-bearing rather than theoretical, because the reviewer bindings this repository
declares in `AGENTS.md` run in technically read-only sessions, and because the companion Spartan
Bridge dispatches read-only reviewers and then performs the artifact write itself under the
`task_artifact_write` grant. The protocol has no vocabulary for that split, so every occurrence has
been resolved by hand.

### Motivating operational example

Reported by the repository owner as task `0040`: read-only Cursor and Codex reviewers correctly
returned verdicts but could not persist them, so later writable rounds had to adopt the result and
advance the handoff, which made the identifier numbering appear inconsistent because this ownership
boundary was not explicit.

No artifact numbered `0040` exists in this repository (highest is `0029`) or in the companion Bridge
repository (highest is `0039`) as of this round, so the number is recorded as reported rather than
resolved to a file. The same pattern is directly observable in the Bridge's
`spartan/tasks/0039-automate-an-approved-plan-through-implementation-review.md`, which is why that
artifact is cited below as evidence rather than as the example itself: its read-only Codex review
cycles consumed `HX-009`, `HX-011`, `HX-013`, `HX-015`, `HX-017`, while the writable correction rounds
that followed adopted `HX-008`, `HX-010`, `HX-012`, `HX-014`, `HX-016`, `HX-018` and carried the
reviewers' verdicts into the artifact on their behalf. Read against today's rules that sequence looks
like a round repeatedly adopting a stale identifier; read against the boundary this task defines it is
the only correct behavior available.

## Scope

- `agent-skill/skills/spartan/references/protocol.md`: the normative correction, in the existing
  "Round lifecycle", "Review and completion", and "Handoff contract" sections, plus the
  conformance-example set.
- `agent-skill/skills/spartan/SKILL.md`: the compact operative restatement, including steps 6 and 7
  of "Run one round".
- `agent-skill/skills/spartan/assets/task-template.md`: the shipped prompt shape.
- `agent-skill/skills/spartan/references/routing.md`: the `Every round MUST record the model it
  actually ran` requirement at line 59, re-anchored per D2.
- `README.md`: the public continue-an-existing-task prompt at line 180.
- `docs/DOGFOODING.md`: the read-only rounds at lines 17, 32, 39, and 43, and the success criterion
  at line 63 (`every round emits one short English handoff`).
- This artifact.
- At most one new file under `agent-skill/skills/spartan/references/` if D10's escape is used.

## Out of Scope

- Root `AGENTS.md`. Not one byte. Its Bridge grant already describes the shape this correction
  formalizes: a human-started run may start a read-only reviewer, and the Bridge itself holds
  `task_artifact_write` for persisting validated reviewer findings. The file is also parser-sensitive
  (task `0028`), so it is the last place to demonstrate anything.
- Bridge-specific runtime behavior, its adapters, its permission modes, and its status documents. See
  decision D9 for the single interface expectation this task records and deliberately does not act on.
- Existing task artifacts `0001`-`0029`. No retrofit, no re-attribution, no renumbering.
- The verdict vocabulary, the `HX-NNN` identifier format, the legacy-migration rule, the two-block
  handoff shape, and the completion-notice rules. This task changes ownership, not vocabulary.
- `agent-skill/skills/spartan/assets/spartan-readme.md`. Reviewed this round: it describes the task
  namespace and the identifier fields without assigning write ownership, so it needs no change. If the
  implementation round finds otherwise, it records why before editing it.
- Adding `src/`, a package manifest, a dependency, a runtime, or any executable checker or test
  harness.
- `spartan/design/`. Nothing here concerns presentation.
- Commit, push, tag, pull request, merge, release, and any change to release automation.

## Constraints

- Markdown instructions, references, and templates only. The repository's change discipline forbids
  `src/`, a package-manager manifest, runtime dependencies, and executable task tooling.
- The correction guides behavior. It must not claim technical enforcement of read-only execution, and
  must not imply the protocol can detect or prevent a write.
- `agent-skill/skills/spartan/SKILL.md` stays preferably below 200 lines. It is 88 lines today, so the
  operative restatement must be compact rather than a second copy of the normative text.
- Every rule must survive the protocol's own scope admission test and the deletion test: with the
  skill removed, task files stay understandable and work continues manually.
- No new required frontmatter field, and no new controlled value that an existing artifact would fail.
- No standalone line `The human starts every round.` may be introduced in `README.md`, and no fenced
  block anywhere may add a line beginning `## ` to root `AGENTS.md`.
- Persisted repository content is English.
- No commit, push, or implementation in this task's rounds unless the human authorizes that action for
  that round.

## Decisions

### D1 - Read-only is a property of the round's execution, not of the role name

The protocol gains one distinction and no new role. A round is **technically read-only** when its
host, session, sandbox, or launcher cannot write repository files, and **writable** otherwise. Both
`reviewer` and `independent-reviewer` may run either way, and so may `verifier`; the existing advisory
sentence "a reviewer SHOULD remain read-only" keeps its meaning as review *discipline* and stops being
the source of a normative conflict.

Rejected: a new `read-only-reviewer` role. It would double the role vocabulary, force every existing
artifact's `next_role` to be re-read, and still not tell a reader whether the host could write.

### D2 - A technically read-only reviewer owns a return value, never a file

Its entire deliverable is a structured result in its final response: exactly one verdict from the
existing vocabulary, its findings, the evidence behind them, and the host, model, effort, and vendor
attribution it actually ran. It writes no task artifact and no product file. Returning that result
satisfies the review round completely; the absent write is not a partial round.

D2-D4 apply to a technically read-only `verifier` exactly as to a `reviewer` or
`independent-reviewer`. Site 16 is the verifier instance of the same split.

The requirement that a review round produce exactly one explicit verdict is preserved and re-anchored:
the round must *produce and return* a verdict, and the artifact must *carry* one before the task can
complete. Those become two obligations on two rounds instead of one obligation no read-only round can
meet.

`routing.md` line 59 (`Every round MUST record the model it actually ran`) is re-anchored the same
way: a writable round writes that attribution into the artifact; a technically read-only round
includes it in this return value and writes nothing. The independence comparison uses whichever of
those two homes holds the attribution.

### D3 - A read-only reviewer consumes its handoff and advances nothing

It reads the envelope addressed to it, applies the existing compare-before-change test, and reports a
mismatch instead of acting. It does not set `handoff_id`, does not set or clear `next_handoff_id`, and
does not touch `## Next Handoff`. Consumption and adoption become separate events: the reviewer
consumes, the writable successor adopts.

This is the rule that makes the motivating example legible. A read-only reviewer consuming `HX-013`
while `handoff_id` still reads `HX-012` is not a stale paste and not a skipped identifier; it is the
documented steady state between consumption and persistence.

### D4 - Exactly one writable round persists the result and advances the envelope once

The next explicitly authorized writable round performs one atomic edit that:

1. transcribes the returned verdict and findings into `## Review`, faithful in substance, attributed
   to the reviewing host, model, effort, and vendor, and marked as adopted rather than authored;
2. sets `handoff_id` to the identifier the read-only reviewer consumed;
3. updates `current_role`, `next_role`, `phase`, `status`, and `updated_at` to describe the state
   after persistence;
4. either issues one new identifier with a fully regenerated envelope, or sets `next_handoff_id: none`
   and clears the envelope under the existing clearing rule.

Advancement happens once for the whole round. A round that persists a verdict and also applies
corrections still advances the envelope once, not once per action. The existing prohibition on lowering
the high-water mark is unchanged: `handoff_id` must already equal the consumed identifier before
`next_handoff_id` is set to `none`.

Rejected: letting the persistence round mint an identifier for the review it is recording, so the
sequence has no apparent gap. It would fabricate an envelope no human ever pasted and make the
identifier stop naming a pasteable prompt.

### D5 - Validation, not transcription-on-trust

The persisting round checks that the result names one verdict from the vocabulary, that it identifies
the reviewing execution, and that it corresponds to the identifier the reviewer consumed. A result
that is ambiguous, carries no verdict, or names an identifier that does not match the artifact is not
persisted: the round records the discrepancy and stops, exactly as the existing mismatch rule does.
Validation is a consistency check on the returned text; it is not a re-review and must not silently
rewrite a verdict.

### D6 - A prompt declares which permission shape it assumes

The advisory block gains one line, `- Permission: read-only | writable`, so the human can match the
prompt to a session before pasting it, and so a later reader can tell which shape was intended.

A prompt addressed to a read-only round MUST NOT contain `update the same task file`, `record your
verdict in`, `Return only the next handoff` as a file-writing or envelope-issuing instruction, or any
other write. It states instead that the round returns the D2 structured result in its response and
changes nothing. A prompt addressed to a writable round keeps today's requirement to update the same
task file and, when the task remains incomplete, to issue the next persisted envelope (D13).

Rejected: inferring the shape from the role name. `reviewer` says nothing about whether the session can
write, which is precisely the ambiguity that produced this task.

### D7 - A read-only prompt may request only checks the repository proves read-only

It may name checks that write nothing, such as `git diff --check`, `git status --short`, a grep or
structural audit, or a type-check that emits no artifact. It must not request a build, a formatting or
code-generation step, a snapshot or fixture update, a dependency install, or any check that writes.

It must also state that a write failure from a check the round could not run is not a product defect,
so the reviewer neither reports it as one nor treats it as grounds for `BLOCKED`. This is a recorded
failure mode, not a hypothetical: the Bridge's read-only review sessions returned `mkdtemp` `EPERM`
from the test suite and could not write `dist/` for the build, and each reviewer had to reason its way
to discounting that on its own.

### D8 - A writable prompt separates persistence from correction

A writable follow-up prompt names one of three authorizations explicitly, and never leaves the reader
to infer it:

- **persist only** - transcribe the returned result, advance the envelope, change no product file;
- **correct only** - apply named findings to product files against a verdict already persisted;
- **persist and correct** - both, persistence first, one envelope advance for the round.

The distinction matters because the two actions carry different risk and different independence
consequences. A persistence-only round is clerical and may run in the same host as the producer; a
correction round is production work whose next review should prefer another vendor.

### D9 - Downstream Bridge alignment is identified, not performed

The Bridge structurally validates a producer's regenerated envelope (`next_handoff_id`, the advisory
`Handoff` line, the prompt identifier, the prompt role, the explicit task path) and holds this
repository's `task_artifact_write` grant for persisting validated reviewer findings. Under this
correction the Bridge is one concrete implementation of D4's writable persistence step, executed by an
external, human-installed tool the protocol already permits and does not depend on.

Nothing in this task changes Bridge behavior, and the portable rules are written so that a repository
with no Bridge reads them as complete. Whether the Bridge needs a separately documented task to align
its own wording with this vocabulary is the owner's call, identifiable from repository state at this
task's completion; this task neither opens nor presumes it.

### D10 - Ownership is documented once, in one normative home

`references/protocol.md` carries the normative rules and the conformance-example set. `SKILL.md`
carries a compact operative restatement that points at it. `assets/task-template.md` carries the
shipped prompt shape. `README.md` and `docs/DOGFOODING.md` are corrected so no reader-facing prompt
tells a read-only round to write.

Prefer extending the three existing `protocol.md` sections in Scope ("Round lifecycle", "Review and
completion", "Handoff contract") over adding a file. The implementation round MAY add one new
`references/` file if the conformance table makes `protocol.md` materially harder to navigate, and
must record which it chose and why. Scope and AC11 allow that one extra file; they do not allow any
other addition.

### D11 - The regression tests are documentation-shaped, and deliberately so

This repository ships no executable suite, and its change discipline forbids adding one. "Deterministic
regression test" is therefore realized as a normative conformance-example set: a fixed table of input
situations mapped to the required prompt shape, artifact edit, and identifier outcome, decidable by
reading, plus a reviewer checklist keyed to the acceptance criteria below. Every criterion must be
checkable that way, without running code.

Executable enforcement, if the owner ever wants it, belongs outside this package, optional and
read-only, which is the conclusion task `0023` already reached for a consistency checker.

### D12 - Risk is high-impact

The change is Markdown-only and reversible, which argues for `material`. It is nonetheless recorded as
`high-impact`, and the higher of two levels is chosen deliberately: it edits the normative handoff
contract that every artifact and every downstream consumer depends on, including a runtime that
structurally validates the envelope this contract defines. A wrong rule here is not a local defect.

### D13 - A technically read-only review round does not issue a persisted handoff

A technically read-only `reviewer`, `independent-reviewer`, or `verifier` round is complete when it
returns the D2 structured result. It does not issue a persisted envelope, does not perform
write-then-copy, and does not treat "return only the next handoff" as an artifact edit.

The next explicitly authorized writable round (D4) is who issues the persisted envelope after a
read-only review: it transcribes the result, adopts the consumed identifier, and either regenerates
one new envelope or clears the proposal under the existing clearing rule.

The following MUST/must obligations still require a write today. Each is scoped to **writable**
rounds only; none remains addressed to a technically read-only review or verifier round:

| Site | Obligation | Writable home | Read-only home |
| --- | --- | --- | --- |
| `protocol.md:60` | lifecycle step 6, update the task artifact | that round writes the artifact | D2 return value; no file write |
| `protocol.md:201` and `:205` | incomplete round MUST return a handoff and require the task file to be updated | D4/D13 persisted envelope | D2 result; D6 prompt forbids the write line |
| `protocol.md:181` | MUST record exactly one explicit verdict | artifact `## Review` | D2 return value |
| `protocol.md:252` and `:254` | set `handoff_id`; record an identifier-less paste | D4 adoption | consume and report; write nothing |
| `protocol.md:256` | write the envelope, then copy it | D4/D13 | does not issue an envelope |
| `protocol.md:266` | SHOULD record host and model in an outcome note | artifact note | D2 attribution |
| `routing.md:59` | MUST record the model it actually ran | artifact note | D2 attribution |
| `SKILL.md:28-35` | steps 6 and 7, update the artifact and end with a handoff | same as lifecycle | D2 result; no handoff edit |
| `SKILL.md:47` | must write an explicit verdict | artifact `## Review` | D2 return value |
| `SKILL.md:79` | adopt `handoff_id` and write the envelope before copying | D4/D13 | consume and report |
| `docs/DOGFOODING.md:63` | every round emits one short English handoff | every writable round | return the D2 result instead |

If implementation leaves any row's read-only home as a file write, AC3 fails. Lifecycle is in Scope
so an implementer following D10 cannot leave `protocol.md:60` untouched.

## Acceptance Criteria

- [x] **AC1 - Plan-review outcomes.** The conformance set shows a plan review returning `APPROVED`,
      `CHANGES_REQUESTED`, and `BLOCKED` from a technically read-only round, and for each one states
      the reviewer's return value, the writable successor's artifact edit, and the resulting
      `handoff_id` / `next_handoff_id` pair.
- [x] **AC2 - Implementation-review outcomes.** The same three outcomes are shown for an
      implementation review, including the `CHANGES_REQUESTED` case where the writable successor both
      persists the verdict and applies corrections, and the `BLOCKED` case where it persists and
      leaves the task `blocked` with `human-operator` as next role.
- [x] **AC3 - A reviewer that writes nothing is fully conformant.** The protocol states that a
      technically read-only review round satisfies its obligations by returning verdict, findings,
      evidence, and execution attribution in its response, and that no rule addressed to a round
      requires it to write. Verified by a targeted re-read: no surviving `MUST`/`must` in
      `protocol.md`, `SKILL.md`, `routing.md`, `assets/task-template.md`, `README.md`, or
      `docs/DOGFOODING.md` obliges a read-only round to write a file, and the twenty-one conflation
      sites listed in Evidence are each either corrected or explicitly scoped to writable rounds.
- [x] **AC4 - Faithful adoption.** The persistence rules require the verdict to be transcribed from
      the vocabulary unchanged, the findings to be faithful in substance, the reviewing host, model,
      effort, and vendor to be named, and the entry to read as adopted rather than authored. A worked
      example shows an adopted `CHANGES_REQUESTED` entry.
- [x] **AC5 - Exactly-once advancement.** The rules state that a read-only reviewer advances nothing
      and that its writable successor advances once for the whole round. The conformance set shows the
      steady state where `handoff_id` trails the consumed identifier, the atomic adoption that closes
      it, and a persist-and-correct round advancing once rather than twice. No conformance case skips
      or reuses an identifier, and the existing no-lowering rule for the high-water mark is restated
      rather than replaced.
- [x] **AC6 - Four-way agreement.** Every conformance case that leaves an outstanding proposal shows
      frontmatter `next_role`, the advisory `- Role:`, the prompt `Act as <role>`, and the identifier
      in frontmatter `next_handoff_id`, advisory `- Handoff:`, and prompt `(handoff HX-NNN)` all in
      agreement, and the new `- Permission:` line is shown as part of the block that is regenerated
      whole.
- [x] **AC7 - No write-producing commands in a read-only prompt.** The protocol forbids
      `update the same task file`, `record your verdict`, and any build, formatting, code-generation,
      snapshot, fixture, or install step in a prompt addressed to a read-only round; permits only
      checks the repository proves read-only; and states that an unrunnable write-requiring check is
      not a product defect. At least one read-only prompt example ships, and it contains none of the
      forbidden instructions.
- [x] **AC8 - Backward compatibility.** No frontmatter field, controlled value, verdict spelling, or
      identifier format is added or narrowed such that an existing artifact stops being valid. Every
      artifact in `spartan/tasks/` remains readable and resumable with no retrofit, including legacy
      artifacts with neither identifier field, and pre-correction `## Review` sections written by the
      reviewer itself remain valid historical record that no round rewrites or re-attributes.
      Verified by reading `0001`, one legacy identifier-less artifact, and `0028` against the corrected
      rules and finding no required edit.
- [x] **AC9 - Deterministic conformance set.** The shipped table maps each input situation to exactly
      one required prompt shape, artifact edit, and identifier outcome, with no case resolvable two
      ways and no case requiring information outside the artifact and the returned result. Two readers
      applying it to the same situation reach the same answer.
- [x] **AC10 - Ownership stated once and found quickly.** A reader looking for "who writes the
      verdict", "who advances the handoff", or "may this round write" finds one normative answer in
      `protocol.md`, a consistent compact restatement in `SKILL.md`, a matching shipped prompt shape in
      `assets/task-template.md`, and no contradicting instruction in `routing.md`, `README.md`, or
      `docs/DOGFOODING.md`. No two files state the rule differently.
- [x] **AC11 - Contained change.** The files changed by 0030 implementation are only those named in
      Scope: the six package and docs files, this artifact, and at most one new `references/` file if
      D10's escape is used. 0030 implementation does not modify root `AGENTS.md`. The owner-directed
      binding-table edit already in the worktree is not a 0030 change and is not reverted. Every
      other task artifact stays byte-identical. `SKILL.md` stays below 200 lines. Checked by
      capturing `git status --short` and `git diff --stat` at the start of the implementation round
      (the pre-implementation baseline) and again after the last edit, and confirming `AGENTS.md` is
      unchanged relative to that baseline.
- [x] **AC12 - Reviewed before implementation.** A reviewer records an explicit `APPROVED`,
      `CHANGES_REQUESTED`, or `BLOCKED` verdict on this plan before any implementation round begins.

## Work Completed

- Read the repository's `AGENTS.md`, the packaged `SKILL.md`, `references/protocol.md`,
  `references/routing.md`, `assets/task-template.md`, `assets/spartan-readme.md`, and
  `agents/openai.yaml`; the root `README.md`; `docs/DOGFOODING.md`; and the two closest precedent
  artifacts, `0028` (identifier discipline across five rounds) and the Bridge's `0039` (the observed
  consumption/adoption split).
- Confirmed the worktree carried no unrelated changes before starting, so this planning round is not
  layered over another task's edits.
- Enumerated every prompt, template, and normative sentence that currently conflates read-only review
  with writable persistence. The seventeen sites are recorded in Evidence with file and line, and are
  the checklist AC3 and AC10 are verified against.
- Settled D1-D12 and derived AC1-AC12 from them. No repository file was changed other than this
  artifact; no implementation was started; nothing was committed.
- Ran in Cursor on Claude Opus 5 (Anthropic vendor attribution). The repository's `AGENTS.md` then
  bound `planner` to Claude Code on `claude-opus-5`; the human ran this planning round in Cursor on
  the same model family instead, which keeps the model vendor but changes the host. It is recorded
  here so the next round's independence comparison uses the attribution that actually ran: the
  reviewing round should differ from Anthropic.
- Independent-reviewer round consumed `HX-001` (match; adopted into `handoff_id`) and recorded
  `CHANGES_REQUESTED`. No file under `agent-skill/`, `docs/`, or `README.md` was changed. No
  implementation was started. Nothing was committed.
- Planner revision consumed `HX-002`. The human's `/spbridge` paste carried no identifier; the
  round proceeded under the artifact envelope and adopted `HX-002` into `handoff_id`. Closed F1-F4:
  added D13 and the writable-only MUST table; extended D2 (verifier and `routing.md:59`); extended
  D6 (read-only prompt does not issue a handoff); reconciled D10 with Scope and AC11; restated AC11
  against a pre-implementation baseline. No file under `agent-skill/`, `docs/`, or `README.md` was
  changed. No implementation was started. Nothing was committed. Ran in Cursor on
  `cursor-grok-4.6-high-fast` (joint Cursor/SpaceXAI; planner binding, high effort named by the
  binding and fixed in the slug).
- Human invoked `/spbridge` on this artifact. The first CLI run returned
  `reason_code: automatic_review_not_authorized` (`run-eb59282b-...`) because the cycle sentence in
  `AGENTS.md` did not match the installed parser. After an owner-directed grant fix (not a 0030
  product edit), later runs returned `registry_unavailable` then `adapter_error` (`exit_nonzero`,
  `cursor-agent` authentication required in the isolated profile). `review_chain` was not a
  continue-from-status case. The human had already authorized completing the chain in this session,
  so the writable rounds below continued here instead of waiting on CLI login.
- Plan review consumed `HX-003` and recorded `APPROVED` (writable reviewer in this session). Then
  implementation applied D1-D13 to the Scope files and added
  `references/review-persistence.md` under D10's escape. Implementation review recorded `APPROVED`.
  Envelope cleared: `handoff_id: HX-003`, `next_handoff_id: none`. No identifier was minted for the
  same-session implementation or implementation-review steps. Nothing was committed.

## Evidence

- Worktree state at the start of this round: `git status --short` produced no output; `git pull
  --ff-only` reported `Already up to date`; `main` is one commit ahead of `origin/main` at `fe81f5d`.
- Next unused creation number confirmed by listing `spartan/tasks/`: `0001`-`0029` present, so this
  artifact is `0030`.
- Repository checks available: none executable. `.github/workflows/` contains only
  `release-please.yml`, which runs on push to `main`; there is no lint, test, or build step. The
  applicable checks for this task are read-only greps, structural reads, and the `git status --short` /
  `git diff --stat` pair named in AC11 - which is also a live instance of D7.
- The seventeen conflation sites, all confirmed by reading the files this round:
  1. `SKILL.md:47` - `A review round must write an explicit verdict.` immediately followed by
     `Reviewers remain read-only unless the user expressly asks them to make changes.` One sentence
     mandates a write; the next mandates no writes.
  2. `SKILL.md:28-34` - step 6 requires every round to `Update the task artifact`, listing
     `an explicit review verdict when acting as reviewer` among the required contents.
  3. `SKILL.md:66` - the single shipped prompt shape ends `Run the relevant repository checks and
     update the same task file.` and is generated for every next role, reviewer included.
  4. `SKILL.md:79` - a receiving round must `adopt it into handoff_id` on a match, record an
     identifier-less paste, and `Write the envelope into the artifact before copying it into your
     final response`. All three are writes required of a read-only reviewer.
  5. `protocol.md:181` vs `:183` - `A review round MUST record exactly one explicit verdict` against
     `A reviewer SHOULD remain read-only`. The `MUST` wins, so the preferred behavior is
     non-conformant.
  6. `protocol.md:205` - the handoff contract requires every prompt to
     `require the same task file to be updated`.
  7. `protocol.md:231` - the recommended prompt shape carries the same
     `Run the relevant repository checks and update the same task file.` line.
  8. `protocol.md:252` - `A receiving round that accepts a matching (or absent...) identifier sets
     handoff_id to it before doing anything else`, and advances `next_handoff_id` if it issues another
     handoff. This is where identifier advancement is assigned to the reviewer.
  9. `protocol.md:254` - an identifier-less paste must be recorded in Work Completed: a write on the
     compare-before-change path.
  10. `protocol.md:256` - `A round MUST write the envelope into the artifact first, then reproduce that
      persisted envelope verbatim in its final response.` Unsatisfiable read-only.
  11. `protocol.md:266` - a round `SHOULD record, in its own outcome note (Work Completed or the review
      line), the host and model it actually used`.
  12. `routing.md:59` - `Every round MUST record the model it actually ran`, with no exception for a
      round that cannot write.
  13. `assets/task-template.md:87` - the shipped prompt block carries the
      `update the same task file` line.
  14. `assets/task-template.md:56-62` - the `## Review` section ships as `Verdict: PENDING` with a
      findings list, with nothing saying which round fills it in.
  15. `README.md:180` - the public continue-an-existing-task prompt tells the next role, whichever it
      is, to `update the same task file`.
  16. `docs/DOGFOODING.md:17` - `The verifier remains read-only with respect to the protocol package.
      It may update only spartan/tasks/0001-bootstrap-v0-1.md` - a scope-relative read-only a
      technically read-only session cannot honor.
  17. `docs/DOGFOODING.md:32,39,43` - `Claude acts as independent-reviewer, remains read-only` and
      `Claude records APPROVED ... in the same task file`, then `returns it for another read-only
      review`. The same contradiction as site 1, in the reader-facing walkthrough.
  18. `protocol.md:60` - Round lifecycle step 6: the current host agent MUST `Update the task
      artifact with the current continuation state`. Addressed to every round, review included.
  19. `protocol.md:201` - `An incomplete, unblocked round MUST return exactly one short
      human-transferable prompt`, and line 205 requires that prompt to `require the same task file
      to be updated`. Combined with write-then-copy (site 10), a read-only reviewer cannot finish
      the round.
  20. `SKILL.md:35` - step 7: `End with either one handoff or a completion notice.` Same obligation
      as site 19, in the operative skill.
  21. `docs/DOGFOODING.md:63` - success criterion `every round emits one short English handoff`.
      Contradicts D13 unless scoped to writable rounds.
- Bridge `spartan/tasks/0039-automate-an-approved-plan-through-implementation-review.md`, read this
  round: the read-only Codex review cycles consumed `HX-009`, `HX-011`, `HX-013`, `HX-015`, `HX-017`,
  and each writable correction round that followed recorded `Consumed HX-0NN in this round, adopting it
  into handoff_id` for `HX-008`, `HX-010`, `HX-012`, `HX-014`, `HX-016`, `HX-018`, carrying the
  reviewer's verdict into `## Review` on its behalf. This is the recorded shape of the reported
  example.
- Same artifact, on D7: its read-only review sessions repeatedly returned `mkdtemp` `EPERM` from the
  test suite and could not write `dist/` for the build, and the record notes each time that the
  reviewer `did not treat that as a product-test failure`. The reasoning was correct and entirely
  ad hoc; no rule told the prompt not to ask.
- Root `AGENTS.md`, read this round: the `Spartan Bridge automation authority` section already grants
  `task_artifact_write` only for `persisting validated reviewer findings and transition metadata`,
  which is D4's writable persistence step described from the runtime side. This is why D9 records an
  interface expectation and changes nothing.
- `assets/spartan-readme.md`, read this round: it describes the task namespace and the optional
  `HX-NNN` fields without assigning write ownership. No conflation site; no change required.
- This round's own checks, after writing this artifact. `git status --short`: the single line
  `?? spartan/tasks/0030-read-only-review-persistence-and-handoff-ownership.md`. `git diff --stat`:
  empty. `git diff --quiet HEAD -- AGENTS.md`: exit 0, so root `AGENTS.md` is byte-identical to
  `HEAD`. `git diff --stat HEAD -- agent-skill README.md docs`: empty, so nothing in the package,
  the README, or the docs changed. `wc -l agent-skill/skills/spartan/SKILL.md`: 87, still well below
  200. Non-ASCII scan of this artifact: no matches.
- Envelope self-check on this artifact: frontmatter `next_role: independent-reviewer`, advisory
  `- Role: independent-reviewer`, and prompt `Act as independent-reviewer` agree; frontmatter
  `next_handoff_id: HX-001`, advisory `- Handoff: HX-001`, and the prompt's `(handoff HX-001)` agree;
  `handoff_id: none`, correct for a first issued handoff on a new task; `## Next Handoff` holds exactly
  two fenced blocks, advisory then prompt; and the prompt's `Open` path resolves to this artifact.
- Independent-reviewer checks after adopting `HX-001`. `git status --short`: `M AGENTS.md` and
  `?? spartan/tasks/0030-read-only-review-persistence-and-handoff-ownership.md`. `git diff --stat
  HEAD -- agent-skill README.md docs`: empty. `wc -l agent-skill/skills/spartan/SKILL.md`: 87.
  The `AGENTS.md` diff is the owner-directed binding-table edit authorized before this review, not
  a 0030 product change. The seventeen listed conflation sites were re-read and confirmed. Additional
  write-obligations that the site list does not number, also confirmed by reading: `protocol.md:60`
  (lifecycle step 6), `protocol.md:201` (incomplete round MUST return a handoff), `SKILL.md:35`
  (step 7 end with a handoff), and `docs/DOGFOODING.md:63` (`every round emits one short English
  handoff`).
- This review ran in Cursor on `cursor-grok-4.6-high-fast` (joint Cursor/SpaceXAI vendor
  attribution; no separately selectable effort). That is a different vendor from the Anthropic
  planning round. The human ran it in this session after updating the `reviewer.plan` binding to
  `claude-opus-5-thinking-high`; the executed host and model are the ones recorded here.
- Planner revision checks after adopting `HX-002`. `git status --short`: `M AGENTS.md` and
  `?? spartan/tasks/0030-read-only-review-persistence-and-handoff-ownership.md`. `git diff --stat
  HEAD -- agent-skill README.md docs`: empty. `wc -l agent-skill/skills/spartan/SKILL.md`: 87.
  D13 names every remaining MUST that would still require a read-only review to write. D10, Scope,
  and AC11 now agree on at most one new `references/` file. AC11 compares against the
  implementation-round baseline, not `HEAD`. Envelope self-check after this edit: `next_role:
  reviewer`, advisory `- Role: reviewer`, prompt `Act as reviewer`; `next_handoff_id: HX-003`,
  advisory `- Handoff: HX-003`, prompt `(handoff HX-003)`; `handoff_id: HX-002`.
- Implementation baseline (before the first package edit): `git status --short` showed `M AGENTS.md`,
  `?? spartan-bridge/`, and this untracked artifact. `git diff --stat HEAD -- agent-skill README.md
  docs` was empty. After the last package edit: those three paths plus `M README.md`, `M docs/`,
  `M agent-skill/skills/spartan/{SKILL.md,assets/task-template.md,references/protocol.md,references/routing.md}`,
  and `?? agent-skill/skills/spartan/references/review-persistence.md`. `AGENTS.md` is unchanged
  relative to that baseline. `wc -l agent-skill/skills/spartan/SKILL.md`: 93. Read `0001` (legacy,
  neither identifier field) and `0028` (identified, completed): no required edit. Targeted
  `MUST`/`must` re-read of the six named files plus the new references file: every remaining
  file-write obligation is scoped to a writable round. Conformance rows P-*, I-*, ADOPT, VALIDATE,
  STEADY, AGREE, RO-CHECK, LEGACY each have one outcome.

## Review

Verdict: APPROVED

Plan review, adopted in this writable session after consuming `HX-003` (Cursor,
`cursor-grok-4.6-high-fast`, joint Cursor/SpaceXAI). D13 and the scoped MUST table close the
handoff-production relocation. D10 and AC11 agree on at most one new `references/` file. AC11
compares against the implementation baseline. D2 covers verifier and re-anchors `routing.md:59`.
Prior `CHANGES_REQUESTED` findings F1-F4 are closed and remain historical record above.

Implementation review, adopted in the same authorized session after the package edit (same host
and model; correlated with the implementer, recorded because the human completed the chain here
after Bridge `adapter_error`). Findings: none blocking. One leftover SKILL.md "incomplete task
returns two blocks" sentence was scoped to writable rounds during the review pass.

The earlier `CHANGES_REQUESTED` verdict from the first independent-reviewer round (same host and
model) remains valid historical record and is not re-attributed.

Adopted from this independent-reviewer round (Cursor, `cursor-grok-4.6-high-fast`, joint
Cursor/SpaceXAI vendor attribution). Findings:

- **F1 - The contradiction relocates to handoff production.** D1-D5 correctly split verdict
  *production* from verdict *persistence*, and the seventeen listed sites are each pointed at by
  some decision. They do not close the write obligations that still address every incomplete
  round: `protocol.md:60` (lifecycle step 6 MUST update the artifact), `protocol.md:201` (MUST
  return a human-transferable prompt and, at line 205, require the task file to be updated),
  `protocol.md:256` (MUST write the envelope, then copy it), `SKILL.md:28-35` (steps 6 and 7),
  and `docs/DOGFOODING.md:63` (`every round emits one short English handoff`). D3 forbids a
  read-only reviewer from touching `## Next Handoff`, so that reviewer cannot satisfy
  write-then-copy. Without an explicit decision that a technically read-only review round is
  complete when it returns the structured result and is exempt from issuing a persisted
  envelope, AC3 fails after implementation. D10's "prefer extending the two existing sections"
  does not name Round lifecycle, so an implementer who follows D10 strictly leaves
  `protocol.md:60` in place. Add that decision (or extend D2/D3/D6), add the missed sites to
  Evidence, and put any lifecycle edit in Scope.
- **F2 - D10 and AC11 disagree about a new file.** D10 MAY add one `references/` file. Scope and
  AC11 say the only files changed are the seven already named. Reconcile: either drop the
  escape, or let Scope and AC11 allow at most one new `references/` file when that escape is
  used.
- **F3 - AC11's `HEAD` comparison is already stale.** The owner changed root `AGENTS.md` (binding
  table only) before this review, which 0030 still correctly leaves out of Scope. `git diff
  HEAD -- AGENTS.md` is therefore non-empty before any implementation edit. Restate the check as
  "0030 implementation does not modify `AGENTS.md`" against a recorded pre-implementation
  baseline, not as byte-identity with `HEAD`.
- **F4 - Tighten two implicit re-anchors.** D1 includes `verifier`; D2-D8 say "reviewer" while
  site 16 is verifier-specific — state that D2-D4 apply to a technically read-only verifier.
  `routing.md:59` is site 12, but no decision says the `MUST record the model` sentence is
  re-anchored to D2's return value for a read-only round; say so, or AC3's targeted re-read
  will still fail.

No finding claims runtime scope or technical enforcement. AC1-AC11 remain documentation-shaped
and checkable by reading once F1-F3 are closed. Backward compatibility (AC8) holds: no new
frontmatter field or controlled value is proposed, and pre-correction `## Review` sections stay
historical record. D9 correctly identifies Bridge alignment without performing it. D12's
`high-impact` rating is accepted.

Planner response (this revision; does not replace the verdict above):

- F1: D13 added. Who issues the persisted envelope after a read-only review is D4's writable
  successor. The MUST table names every remaining write obligation and its read-only home. Sites
  18-21 added. "Round lifecycle" is in Scope.
- F2: D10 keeps the one-file escape; Scope and AC11 now allow exactly that file and no other.
- F3: AC11 checks `AGENTS.md` against the pre-implementation baseline, not `HEAD`.
- F4: D2 now covers verifier and re-anchors `routing.md:59`.

## Blockers

None.

## Next Action

None. The task is completed: acceptance criteria are satisfied, checks have recorded outcomes,
required reviews are `APPROVED`, and no blocker remains.

## Next Handoff

Non-binding suggestion (no identifier; do not open this completed artifact):

```text
Recommended execution (human decides):
- Host: Cursor (this repository's `AGENTS.md` binds `planner` to Cursor)
- Model and effort: cursor-grok-4.6-high-fast at high effort
- Role: planner
- Permission: writable
- Invocation: `/spartan`, passing the prompt block below as the argument
```

```text
Create a uniquely numbered artifact from `agent-skill/skills/spartan/assets/task-template.md` in `spartan/tasks/`.

Act as planner. Decide whether the companion Spartan Bridge needs its own task to align wording with this repository's new read-only return value, writable persistence, and Permission line. Do not reopen or update `spartan/tasks/0030-read-only-review-persistence-and-handoff-ownership.md`.
```
