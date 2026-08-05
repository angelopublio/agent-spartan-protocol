# Spartan Portable Handoff Protocol

## Table of contents

1. [Purpose](#purpose)
2. [Normative language](#normative-language)
3. [Actors and artifacts](#actors-and-artifacts)
4. [Fundamental invariants](#fundamental-invariants)
5. [Round lifecycle](#round-lifecycle)
6. [Task artifact contract](#task-artifact-contract)
7. [Repository layout](#repository-layout)
8. [Design material](#design-material)
9. [Review and completion](#review-and-completion)
10. [Handoff contract](#handoff-contract)
11. [Authorization and mutations](#authorization-and-mutations)
12. [Allowed and forbidden capabilities](#allowed-and-forbidden-capabilities)
13. [Failure and recovery](#failure-and-recovery)
14. [Scope admission test](#scope-admission-test)

## Purpose

Spartan is a host-neutral protocol for human-mediated continuity between coding-agent sessions. It coordinates information through a durable Markdown task artifact. It does not coordinate processes.

The protocol MAY recommend, record, and hand off the next step. It MUST NOT initiate, supervise, resume, retry, or guarantee execution by another agent.

## Normative language

`MUST`, `MUST NOT`, `SHOULD`, `SHOULD NOT`, and `MAY` are normative requirements. Descriptive status and phase values do not create an executable state machine.

## Actors and artifacts

- **Human operator:** starts every round, chooses every host, and manually transfers every handoff.
- **Current host agent:** the one authenticated agent accountable for one bounded round.
- **Target repository:** the repository in which the requested work occurs.
- **Task artifact:** one Markdown file at `spartan/tasks/NNNN-<slug>.md` containing the current continuation state.
- **Round:** one foreground interaction in one current host.
- **Role:** the responsibility assigned to the current or next round.
- **Evidence:** concise, reproducible facts supporting a claim, such as a command and its outcome.
- **Handoff:** one short English prompt that references the task artifact and names the next bounded action.

## Fundamental invariants

1. Repository files, the current diff, and check results are implementation truth.
2. The task artifact is handoff truth.
3. Conversation history is ephemeral and MUST NOT be required for continuation.
4. The human operator MUST initiate every platform or model transfer.
5. A round MUST have one accountable current host agent.
6. Spartan MUST NOT create or depend on hidden operational state.
7. Persisted protocol content and generated handoffs MUST be English.

## Round lifecycle

The current host agent MUST:

1. Resolve the target repository and read its applicable instructions.
2. Locate an explicitly referenced task artifact, continue one unambiguous active task, or create a new task from the template.
3. Classify the task type, risk, current role, and recommended next role.
4. Perform one bounded, useful unit of work within the user's current authorization.
5. Run relevant checks already supplied by the target repository when practical.
6. Update the task artifact with the current continuation state.
7. Return one handoff, ask one blocking human question, or issue a completion notice.

The lifecycle describes expected behavior. No daemon, transition engine, event fold, scheduler, or background process enforces it.

## Task artifact contract

The task artifact MUST be readable without conversation history. It MUST use the template fields and sections or an equivalent superset that remains concise.

Controlled values:

- `status`: `active`, `blocked`, `completed`
- `phase`: `investigating`, `planning`, `implementing`, `reviewing`, `verifying`, `complete`
- `task_type`: `investigation`, `planning`, `implementation`, `review`, `verification`
- `risk`: `routine`, `material`, `high-impact`
- roles: `human-operator`, `investigator`, `planner`, `implementer`, `reviewer`, `independent-reviewer`, `verifier`
- verdict: `PENDING`, `APPROVE`, `CHANGES`, `BLOCKED`

The artifact MUST contain the current objective, scope, constraints, acceptance criteria, decisions, work completed, evidence, review state, blockers, and next action. Its filename MUST use the immutable `NNNN-slug.md` creation-order convention, and its frontmatter MUST include `created_at` and `updated_at` dates.

The frontmatter `protocol` value is a passive Semantic Version birth-stamp copied from the package template when the task is created. It has no runtime behavior and MUST NOT trigger version negotiation, network checks, migration, or rewrites of existing tasks. Spartan MUST NOT use this metadata to version or otherwise modify an adopting repository.

The frontmatter `handoff_id` and `next_handoff_id` fields hold the task's handoff identifier state: `handoff_id` is the high-water mark of the highest identifier this task has ever accepted, and `next_handoff_id` is the identifier of the envelope currently proposed in `Next Handoff`, or `none` when no proposal is outstanding. Both default to `none` on task creation. A handoff identifier is `HX-` followed by a digit run that is either exactly three digits or four-or-more digits whose first digit is not `0`, with a numeric value of at least 1 - `HX-001` through `HX-999`, then the unpadded widened form from `HX-1000` onward. `HX-000` and any non-canonical padding or short form are not valid identifiers. A task created before this field existed has neither field present and is a legacy artifact under the handoff contract's migration rule.

The artifact MUST NOT become:

- a conversation transcript;
- a complete stdout capture;
- an append-only event history;
- an immutable ledger;
- a database or queue;
- a projection of another source of state;
- an executable state machine.

Stale detail SHOULD be compacted when it no longer helps the next round. Evidence SHOULD identify the command or observation and the outcome, not reproduce unbounded output.

The artifact MUST NOT contain credentials, tokens, private keys, session material, or unnecessary personal data. When evidence could expose sensitive content, record a safe description and the outcome instead.

## Repository layout

Spartan writes only under `spartan/` in the target repository, except for the one explicitly
authorized case in "Graduating a settled standard." Three names are reserved:

- `spartan/tasks/` MUST hold task artifacts and nothing else.
- `spartan/design/` MAY hold a task's design material, as defined below. It is optional; a
  repository whose rounds settle nothing about presentation never creates it.
- `spartan/docs/` MUST NOT be created. Documentation that the repository maintains and that must
  stay true as the code changes belongs to the repository's own documentation location, written in
  an explicitly authorized round.

Any other output too large for the task artifact is a round deliverable and lives at
`spartan/<kind>/<slug>.md`, where the producing task names `<kind>`. A round deliverable MUST be
named in the producing task's Scope and referenced from its Work Completed. It is never handoff
state: the task artifact remains handoff truth, and removing every deliverable MUST leave the task
files understandable.

## Design material

A round MAY produce design material: what the task raises or settles about how information is
presented - a brief for a screen, a layout or component choice, a state vocabulary, a question left
open for a designer. It lives at `spartan/design/<slug>.md`.

Design material is a round deliverable. Its producing task MUST name the design-material path in its
Scope and reference it from its Work Completed. The design-material file MUST name the task artifact
that produced it and MAY additionally cite decision tasks it draws on.

A presentation decision a round makes stays in that task's Decisions by default. It becomes a file
in `spartan/design/` only when it is too large for the task artifact to stay concise, or when it is
addressed to a reader outside the round, such as a designer or a later implementation round.

Design material is point-in-time. It is true as of the task that produced it and is not maintained
afterwards. `spartan/design/` MUST stay flat, and design material MUST NOT be edited after its
producing task completes; revised material is a new file with its own slug, produced by a new task.
The directory carries no version suffix, index, or currency marker, because design material is
reached from the task that produced or consumed it rather than by browsing.

Design material MUST be readable without conversation history, MUST state already-settled decisions
as settled and closed to reopening, MUST state what is out of scope, and MUST list the questions
deliberately left open. It MUST NOT contain credentials or unnecessary personal data.

`spartan/design/` is outbound only. What an external design host returns is product material
governed by the target repository's own conventions, so this protocol does not name a location for
it. Two rules apply instead: the returned design MUST NOT be stored anywhere under `spartan/`, and
the task that consumes it MUST record in its Evidence where it landed, or how to reach it when it
lives outside the repository.

### Graduating a settled standard

An interface standard that is settled and must stay true as the product changes - typography,
sizing, colour, markup and class conventions, base layout, component patterns - is maintained
documentation. It MUST NOT live under `spartan/`.

When a round settles such a standard, it belongs in the repository's own design documentation. The
round MUST write to the home the repository already uses, such as a component workshop, a
design-token file, a style configuration, or an existing design document, and MUST NOT create a
second home beside it. Only when the repository has no such home does the round propose creating
one. This protocol names no path for it.

Graduation is the only case in which a Spartan round writes outside `spartan/`, and it requires
explicit human authorization for that round. A round MUST NOT create or update the outside file on
its own initiative. It records the proposal in the task artifact and requests authorization through
the existing routes for a missing authority: a blocking human question, or a handoff naming
`human-operator` as next role.

The request MUST state:

- the proposed path outside `spartan/`;
- whether it is a creation or an update, and for an update, which existing home was found;
- what content graduates to it;
- what stays in `spartan/design/`.

The last two are the substance of the request: the human is approving a split between maintained
documentation and point-in-time material, and cannot judge it without seeing both sides.

Design material in `spartan/design/` SHOULD reference those standards rather than restate them, so
that a standard has one copy that can be corrected once.

## Review and completion

A review round MUST record exactly one explicit verdict: `APPROVE`, `CHANGES`, or `BLOCKED`. An absent or ambiguous verdict is not approval.

A reviewer SHOULD remain read-only unless the human explicitly authorizes changes for that round. A fresh-context or cross-vendor review is advisory independence, not a technically enforced guarantee.

An independent review SHOULD run in a different host - and preferably a different vendor - than the one that produced or last modified the work under review, because a host reviewing its own output shares its blind spots. The same preference applies to a re-review after fixes: prefer a host other than the one that applied them. This preference is advisory; the human chooses the host and MAY override it.

A task MAY become `completed` only when:

- every acceptance criterion is satisfied or explicitly waived by the human;
- relevant checks have recorded outcomes;
- every required review is `APPROVE`;
- no blocker remains;
- there is no required next action.

Otherwise the task remains `active` or `blocked`.

A task MAY also be closed as `completed` when its remaining work has been fully absorbed by another task; record which task absorbed it and treat the folded acceptance criteria as satisfied under that task.

## Handoff contract

An incomplete, unblocked round MUST return exactly one short human-transferable prompt. It MUST:

- reference the current task's own `spartan/tasks/NNNN-<slug>.md`, never another task's file;
- name one next role;
- state one bounded action and its success condition;
- require the same task file to be updated;
- request one next handoff or a completion notice;
- avoid repeating architecture, history, or requirements stored in the task artifact;
- be written in English;
- be delivered as two separate text blocks: first a "Recommended execution" block for the human, then the prompt block to pass to the next host.

The "Recommended execution" block is advisory. The human MAY override any part of it. It MUST state the recommended host with a one-phrase reason, one concrete model and one concrete effort or reasoning level (for example "GPT-5 Codex, reasoning effort high" or "Claude Opus, extended thinking"), and the invocation style. Generic phrases such as "the best available model" do not satisfy the contract; when the exact model on the human's plan is unknown, name the most likely model and add one fallback phrase.

The prompt block contains only the prompt itself, with no execution metadata, so the human can paste it unchanged: as the skill argument when invoking the host's skill token, or directly when the target host has no skill support.

The invocation style MUST match what the recommended host supports, determined by capability rather than by host name. A host that exposes a skill token receives that token: `$spartan` for Codex, `/spartan` for Claude Code (`/spartan:spartan` when installed under a plugin namespace). A host that discovers skills from `SKILL.md` but exposes no invocation token receives the prompt with the skill named in it, which is how Cursor is invoked. Only a host without skill support receives the direct prompt. A handoff MUST NOT pair a recommended host with another host's invocation token, and MUST NOT route a skill-capable host to the direct prompt merely because it is not named here.

Recommended shape:

```text
Recommended execution (human decides):
- Host: <Codex | Claude Code | other authenticated host, with a one-phrase reason>
- Model and effort: <one concrete model, one concrete effort level, optional fallback phrase>
- Invocation: <the recommended host's own convention: `$spartan` for Codex, `/spartan` for Claude Code, the skill named in the prompt for a host that discovers skills without exposing a token, direct prompt otherwise>, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/NNNN-<slug>.md`.

Act as <next-role>. <Perform one bounded action and state its success condition>.
Run the relevant repository checks and update the same task file.

Return only the next handoff, or a completion notice if no work remains.
```

The next role is encoded in three places that MUST agree: the frontmatter `next_role`, the role and its reason in the "Recommended execution" block, and the `Act as <role>` line in the prompt block. When the next step changes, regenerate the whole handoff as a unit; never edit one part and leave the others stale.

### Handoff identifier

An identified task (both `handoff_id` and `next_handoff_id` present, see "Task artifact contract") carries a compact monotonic handoff identifier so a stale envelope or a stale pasted prompt is detectable rather than silently applied. A legacy task (neither field present) is unaffected and keeps its old envelope shape exactly as written; only the pre-existing three-place role agreement above applies to it.

**Canonical envelope, scoped to an outstanding proposal.** A task has an outstanding proposal exactly when `next_handoff_id` is not `none`. While a proposal is outstanding, `## Next Handoff` MUST be the single canonical current envelope: exactly one such section, holding exactly two fenced blocks (advisory, then prompt), always regenerated whole. When there is no outstanding proposal (`next_handoff_id: none`), the section MUST NOT carry a handoff envelope: the round that clears the proposal MUST remove both fenced blocks in the same edit, and no `HX-NNN` occurrence MUST remain anywhere in the section. The section MAY still be omitted, reduced to a status line, or retained as a completion-notice suggestion (a "Recommended execution" and prompt pair introduced by a line beginning `Non-binding suggestion`, carrying no identifier and not opening the completed artifact).

**Placement.** The identifier is encoded in three places that MUST agree with `next_handoff_id`, alongside the existing role agreement:

| Value | Where it is written | Agreement test |
|---|---|---|
| Next role | frontmatter `next_role`; advisory `- Role:`; prompt `Act as <role>` | the three strings are equal |
| Proposed handoff id | frontmatter `next_handoff_id`; advisory `- Handoff:`; prompt `(handoff <id>)` on the `Open` line | the three strings are equal |
| Task file | the artifact's own filename; prompt `Open spartan/tasks/NNNN-slug.md` | the prompt path resolves to this artifact |

**Increment semantics.** On task creation both fields are `none`. A newly issued handoff sets `next_handoff_id` to one more than the higher of `handoff_id` and the previous `next_handoff_id`; identifiers increase monotonically within a task and are never reused, decremented, or renumbered. The identifier names the pasteable prompt text: regenerating an outstanding proposal keeps `next_handoff_id` only while the prompt block stays textually identical to the persisted one; any change inside the prompt block (its `Open` path, its `Act as <role>` line, its bounded action or success condition, or any other wording) MUST issue a higher identifier. A correction confined to the advisory block keeps the identifier, because the human pastes only the prompt block. A receiving round that accepts a matching (or absent, see below) identifier sets `handoff_id` to it before doing anything else; if it then issues another handoff it advances `next_handoff_id`, and if it leaves no outstanding proposal it sets `next_handoff_id: none` and clears the envelope. Clearing a proposal MUST NOT lower the high-water mark: `handoff_id` MUST already equal the identifier being accepted before `next_handoff_id` is set to `none`.

**Compare before change.** A round started from a pasted prompt that carries an identifier MUST compare it with the artifact's `next_handoff_id` before making any change. If they match, proceed. If they differ, stop: make no change to the repository or the artifact, report the pasted identifier, the artifact's current `next_handoff_id`, and the artifact's current `Next Action`, and recommend re-running from the artifact's own current envelope. If the pasted prompt carries no identifier while the artifact proposes one, do not hard-stop: proceed under the artifact's current envelope, which is acceptance of it, so set `handoff_id` to the artifact's `next_handoff_id` and record in Work Completed that the pasted prompt carried no identifier. If both are absent, proceed as before this field existed.

**Write, then copy.** A round MUST write the envelope into the artifact first, then reproduce that persisted envelope verbatim in its final response, so the response is always a copy of the artifact rather than an independently authored text. Whether the human's chat actually matches the artifact at the moment of pasting, and which of several past responses they pasted, are not deterministically checkable; the receiving round's compare-before-change step is the practical detector, applied at the point where a stale paste would otherwise be silently applied.

**Legacy migration.** A legacy task (neither field present) is valid as it stands and needs no retrofit; a round that only reads, completes, or blocks it without issuing a handoff adds nothing. The first round that issues a newly generated handoff on a legacy task upgrades it in one atomic edit: it adds both frontmatter fields (`handoff_id: none`, `next_handoff_id: HX-001`) and regenerates the whole `Next Handoff` section under these rules in the same edit. An edit that adds one field but leaves the old envelope lines standing, or the reverse, is a partial upgrade and MUST be finished, not left half-done.

A handoff MUST point at the current task's own continuation and MUST NOT hand off to work that a different active task already tracks. When a task's remaining work is fully absorbed by another task, close it as `completed` and note which task absorbed it, rather than leaving it `active` with a handoff that points at another task's file.

Spartan MUST NOT transmit or execute the handoff. The human chooses the next host and starts the next round.

A handoff MAY be executed by an external, human-installed tool; such a tool is outside this protocol and the protocol MUST NOT depend on it.

The recommended host is only a suggestion. When the human runs a round, that round SHOULD record, in its own outcome note (Work Completed or the review line), the host and model it actually used - even when they differ from the recommendation. This keeps both the recommended and the executed host visible and lets the different-host review preference be audited. It is a single current-snapshot note per round, not a per-round execution log.

A completion notice MUST NOT leave the human without direction. It MUST state what was completed and its verdict or outcome, and then either:

- suggest the next step in the same two-block shape (a "Recommended execution" block and a prompt block), when follow-up work is identifiable from repository state, the roadmap, or explicit owner direction; or
- state explicitly that no follow-up work is identifiable.

A suggested next step is a recommendation for a possible new round or task, not a continuation of the completed task. The human still initiates it, and the suggestion MUST NOT invent scope the owner has not indicated.

When the completed work changed durable behavior or decisions, the completion notice SHOULD suggest a documentation round when one is identifiable. It SHOULD NOT make a documentation round a ritual for every completion.

When a round creates a repository's first Spartan task and the repository root contains neither `AGENTS.md` nor `CLAUDE.md`, the round's final response SHOULD include one advisory recommendation that the human create an `AGENTS.md` covering stack, dev/build/lint/test commands, and conventions plus a minimal `CLAUDE.md` that points to it, without creating those files or tracking suppression state.

For a completion notice, "the same two-block shape" reuses only the advisory "Recommended execution" block and the pasteable prompt block. It does not reuse the incomplete-task requirement to open and update the same task file. The prompt block MUST instead do one of the following:

- For a suggested new task, direct the human-started next round to create a uniquely numbered artifact from `assets/task-template.md` in `spartan/tasks/`. It MUST NOT open, reopen, or update the completed artifact.
- For an explicitly named existing `active` task, reference that active artifact and require that the next round update that active artifact. It MUST NOT update the completed artifact.

The completion suggestion remains advisory: the human MAY decline it, choose another host or model, or start no further round.

## Authorization and mutations

The current host agent MAY inspect files, inspect Git state, edit code, and run relevant existing checks when those actions are within the user's request and the host's normal authorization.

Commit, push, pull-request creation, merge, deployment, destructive changes, and other material external actions MUST occur only when explicitly requested for the current round. Spartan MUST NOT make them automatic consequences of protocol progression.

## Allowed and forbidden capabilities

| Area | Allowed | Forbidden |
|---|---|---|
| State | One readable task Markdown | Databases, `events.jsonl`, projections, ledgers, queues |
| Execution | Foreground work by the current host | Nested LLM APIs, CLIs, subprocesses, or cross-host calls |
| Continuity | Human starts the next round | Daemons, auto-resume, background workers |
| Failure | Record a blocker and safe evidence | Automatic retry, watchdog, polling, kill/resume |
| Concurrency | Human sequences rounds | Locks, leases, worker pools, concurrency coordination |
| Authentication | Existing host session | Credential, token, account, or login management |
| Models | Advisory role or host recommendation | API routing or quota-aware model selection |
| Security | Existing repository checks and guidance | Custom sandbox, policy engine, or secret scanner |
| Git | Inspect and edit in the authorized round | Automatic patch adoption, merge, push, or deployment |
| Review | Explicit human-readable verdict | Implicit approval or automatic adoption |
| Scheduling | Recommend a native host option | Spartan scheduler, loop, watcher, or timer |
| Logs | Concise command and outcome | Full stdout capture or conversation transcript |

Native host capabilities MAY be recommended as optional human choices. Spartan MUST NOT invoke, monitor, resume, normalize, or depend on them. Internal host behavior is outside the protocol; Spartan does not treat it as portable functionality.

## Failure and recovery

Recovery is manual by design:

1. The interrupted host stops.
2. The human opens a host and invokes Spartan again.
3. The new current host reads the task artifact, repository state, and applicable instructions.
4. The new round records any uncertainty rather than inventing missing history.

Spartan does not guarantee exactly-once work, patch identity, crash recovery, or replay. Requirements for those guarantees belong in a separate runtime or workbench.

## Scope admission test

A proposed feature belongs in the portable protocol only when every answer is yes:

1. Can it be expressed using Markdown instructions or templates?
2. Does it finish in the current foreground session?
3. Does a human still initiate every platform transfer?
4. Is the task Markdown the only durable protocol state?
5. Is manual continuation from that Markdown sufficient after failure?
6. Does it work without API keys, daemons, locks, queues, or subprocesses?
7. Does it guide behavior without claiming technical enforcement?

The deletion test is decisive:

> If the skill is removed, do the task files remain understandable, and can the work continue manually?

If not, the feature belongs in a runtime or workbench, not in Spartan Portable Handoff Protocol.
