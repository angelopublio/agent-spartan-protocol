---
name: spartan
description: Coordinate portable, human-mediated software-task handoffs through a durable repository-local Markdown artifact. Use when starting, continuing, investigating, planning, implementing, reviewing, verifying, blocking, or completing work that may move between authenticated coding-agent hosts such as Codex, Claude Code, Grok Build, or Cursor, while keeping every cross-host transfer manual and avoiding runtime orchestration.
---

# Spartan

Coordinate information, not processes. Work in the currently authenticated host, preserve the continuation state in one task file, and give the human one concise English handoff when another round is needed.

Read [references/protocol.md](references/protocol.md) before acting. Read [references/routing.md](references/routing.md) before assigning risk, the current role, or the next role. Read [references/review-persistence.md](references/review-persistence.md) when the round is a review or verifier, or when issuing a prompt whose permission shape matters. When creating the `spartan/` directory for the first time in a target repository, also copy [assets/spartan-readme.md](assets/spartan-readme.md) to `spartan/README.md`. When creating the repository's first Spartan task, if the target repository root contains neither `AGENTS.md` nor `CLAUDE.md`, append one advisory line recommending that the human create an `AGENTS.md` covering stack, dev/build/lint/test commands, and conventions plus a minimal `CLAUDE.md` that points to it; create no file outside `spartan/`, and add no marker, suppression file, or task-artifact status field. When creating a task, copy [assets/task-template.md](assets/task-template.md) to `spartan/tasks/NNNN-<slug>.md`, assign the next unused four-digit creation number, and replace every placeholder.

## Hard boundary

Do not invoke, require, supervise, monitor, resume, retry, or coordinate another model, agent, subagent, workflow, scheduler, watcher, daemon, or LLM subprocess on Spartan's behalf. Do not send a handoff automatically.

The current host may inspect files, edit code, run commands, and use ordinary tools authorized for the current user request. That foreground work is the current round; it is not a nested worker.

## Run one round

1. Resolve the target repository. Read its applicable instructions before changing anything.
2. Resolve the task artifact:
   - Continue only from a task file explicitly named by the user or from one unambiguous active task in `spartan/tasks/`.
   - Otherwise create a new task from the template with a unique, descriptive slug.
   - If multiple tasks could apply, ask the human to choose; do not guess.
3. Classify the task type, risk, current role, and recommended next role using the routing reference.
4. Perform one bounded, useful unit of work in the current host. Do not broaden the user's authorization.
5. Run the relevant checks already available in the target repository. Do not invent an execution or security layer. A technically read-only round may run only checks that write nothing.
6. If the round is writable, update the task artifact with the current continuation state:
   - decisions and material assumptions;
   - work completed;
   - concise evidence, including commands and outcomes rather than full output;
   - blockers and unresolved risks;
   - an adopted review verdict when persisting a reviewer's return value;
   - exactly one next action.
   If the round is technically read-only, return the structured result (verdict, findings, evidence, execution attribution) and write nothing.
7. If the round is writable, end with either one handoff or a completion notice. A technically read-only review or verifier round is complete when it returns the structured result; it does not issue a persisted envelope.

## Preserve the two authorities

- Treat repository files, the current diff, and check results as implementation truth.
- Treat the task Markdown as handoff truth.
- Treat conversation history as ephemeral context, not durable state.

Do not turn the task file into a transcript, append-only event log, database, ledger, status projection, or executable state machine. Compact stale detail when it no longer helps the next round.

## Review and completion

Use only `PENDING`, `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED` as review verdicts. A review or verifier round must produce and return exactly one explicit verdict. A technically read-only round satisfies that obligation in its response and writes nothing; the next writable round persists the adopted result and advances the envelope once. Reviewers SHOULD remain read-only as discipline unless the user expressly authorizes changes. See [review-persistence.md](references/review-persistence.md).

Mark a task `completed` only when its acceptance criteria are satisfied, relevant checks have recorded outcomes, required review is approved, and no blocker remains. Otherwise leave it `active` or `blocked` and produce the next handoff.

## Produce the handoff

Write every persisted artifact and every handoff in English. For an incomplete task, a writable round returns two separate text blocks with this shape:

```text
Recommended execution (human decides):
- Host: <Codex | Claude Code | other authenticated host, with a one-phrase reason>
- Model and effort: <one concrete model, one concrete effort level, optional fallback phrase>
- Role: <next-role>
- Handoff: HX-NNN
- Permission: writable
- Invocation: <the recommended host's own convention: `$spartan` for Codex, `/spartan` for Claude Code, the skill named in the prompt for a host that discovers skills without exposing a token, direct prompt otherwise>, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/NNNN-<slug>.md` (handoff HX-NNN).

Act as <next-role>. <Perform one bounded action and state its success condition>.
Run the relevant repository checks and update the same task file.

Return only the next handoff, or a completion notice if no work remains.
```

A read-only prompt uses `- Permission: read-only`, omits every file-writing line, and asks the round to return the structured result. Do not generate `update the same task file` for a read-only round.

The invocation must match what the recommended host actually supports, not its name. Use the host's own skill token when it has one: `$spartan` for Codex, `/spartan` for Cursor, and `/spartan` for Claude Code (`/spartan:spartan` when installed as a plugin). When the host discovers skills from `SKILL.md` but exposes no invocation token, name the skill in the prompt instead. Only a host without skill support gets the direct prompt. Never pair a host with another host's token.

A writable round always delivers both blocks. The first is advisory and the human may override it; name one concrete model and one concrete effort level, never a generic phrase such as "best available model". The second contains only the prompt, with no execution metadata, so the human can paste it unchanged. A technically read-only round delivers the structured result instead.

Keep the handoff consistent as a unit: the frontmatter `next_role`, the advisory block's role, and the prompt's `Act as <role>` must agree - when the next step changes, regenerate the whole handoff rather than editing one half. A handoff points at the current task's own continuation; if a task's remaining work is absorbed by another, close it as `completed` (noting which task absorbed it) instead of pointing it at another task's file.

### Handoff identifier

An identified task (frontmatter carries `handoff_id` and `next_handoff_id`) keeps one canonical `Next Handoff` section while a proposal is outstanding (`next_handoff_id` not `none`), and none when it is not - the clearing edit removes both fenced blocks and every `HX-NNN` mention in the section in one go. The identifier (`HX-NNN`, monotonic, never reused) appears in the frontmatter `next_handoff_id`, the advisory `- Handoff:` line, and the prompt's `(handoff HX-NNN)` parenthetical, and all three must agree. A changed prompt block always gets a new identifier; an advisory-only correction keeps it. Before acting on a pasted prompt that names an identifier, compare it with `next_handoff_id`: on a match, proceed; on a mismatch, stop, make no change, and report both identifiers; if the paste carries none while the artifact proposes one, proceed under the artifact's envelope. A writable round adopts the identifier into `handoff_id` and writes the envelope into the artifact before copying it into the final response. A technically read-only round consumes the identifier and writes nothing; the next writable round adopts. A legacy task (neither field present) keeps its old envelope untouched; the first writable handoff issued on it upgrades both fields and the whole section in one atomic edit.

A completion notice must also give direction: state what was completed and its outcome, then either suggest the next step in the same two-block shape (when follow-up work is identifiable from repository state, the roadmap, or owner direction) or state explicitly that no follow-up work is identifiable. It SHOULD suggest a documentation round when the completed work changed durable behavior or decisions, but not for every completion. Never invent scope the owner has not indicated.

For a completion suggestion, "same two-block shape" means only the advisory execution block plus the pasteable prompt block; it does not mean to reuse the incomplete-task instruction to open and update the completed task file. A prompt for a suggested new task must direct the human-started next round to create a uniquely numbered artifact from `assets/task-template.md` in `spartan/tasks/` and must not open, reopen, or update the completed artifact. A prompt may instead name an existing `active` task; in that case, it must reference and update that active artifact, never the completed one. The human may decline the suggestion, choose another host or model, or start no further round.

## Keep external actions explicit

Commit, push, open a pull request, merge, deploy, or perform another material external action only when the user explicitly requests that action for the current round. Never make those actions a default consequence of using Spartan.
