---
name: spartan
description: Coordinate portable, human-mediated software-task handoffs through a durable repository-local Markdown artifact. Use when starting, continuing, investigating, planning, implementing, reviewing, verifying, blocking, or completing work that may move between authenticated coding-agent hosts such as Codex, Claude Code, Grok Build, or Cursor, while keeping every cross-host transfer manual and avoiding runtime orchestration.
---

# Spartan

Coordinate information, not processes. Work in the currently authenticated host, preserve the continuation state in one task file, and give the human one concise English handoff when another round is needed.

Read [references/protocol.md](references/protocol.md) before acting. Read [references/routing.md](references/routing.md) before assigning risk, the current role, or the next role. When creating the `spartan/` directory for the first time in a target repository, also copy [assets/spartan-readme.md](assets/spartan-readme.md) to `spartan/README.md`. When creating a task, copy [assets/task-template.md](assets/task-template.md) to `spartan/tasks/NNNN-<slug>.md`, assign the next unused four-digit creation number, and replace every placeholder.

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
5. Run the relevant checks already available in the target repository. Do not invent an execution or security layer.
6. Update the task artifact with the current continuation state:
   - decisions and material assumptions;
   - work completed;
   - concise evidence, including commands and outcomes rather than full output;
   - blockers and unresolved risks;
   - an explicit review verdict when acting as reviewer;
   - exactly one next action.
7. End with either one handoff or a completion notice.

## Preserve the two authorities

- Treat repository files, the current diff, and check results as implementation truth.
- Treat the task Markdown as handoff truth.
- Treat conversation history as ephemeral context, not durable state.

Do not turn the task file into a transcript, append-only event log, database, ledger, status projection, or executable state machine. Compact stale detail when it no longer helps the next round.

## Review and completion

Use only `PENDING`, `APPROVE`, `CHANGES`, or `BLOCKED` as review verdicts. A review round must write an explicit verdict. Reviewers remain read-only unless the user expressly asks them to make changes.

Mark a task `completed` only when its acceptance criteria are satisfied, relevant checks have recorded outcomes, required review is approved, and no blocker remains. Otherwise leave it `active` or `blocked` and produce the next handoff.

## Produce the handoff

Write every persisted artifact and every handoff in English. For an incomplete task, return two separate text blocks with this shape:

```text
Recommended execution (human decides):
- Host: <Codex | Claude Code | other authenticated host, with a one-phrase reason>
- Model and effort: <one concrete model, one concrete effort level, optional fallback phrase>
- Invocation: <`$spartan` | `/spartan` | direct prompt>, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/NNNN-<slug>.md`.

Act as <next-role>. <Perform one bounded action and state its success condition>.
Run the relevant repository checks and update the same task file.

Return only the next handoff, or a completion notice if no work remains.
```

Always deliver both blocks. The first is advisory and the human may override it; name one concrete model and one concrete effort level, never a generic phrase such as "best available model". The second contains only the prompt, with no execution metadata, so the human can paste it unchanged.

Keep the handoff consistent as a unit: the frontmatter `next_role`, the advisory block's role, and the prompt's `Act as <role>` must agree - when the next step changes, regenerate the whole handoff rather than editing one half. A handoff points at the current task's own continuation; if a task's remaining work is absorbed by another, close it as `completed` (noting which task absorbed it) instead of pointing it at another task's file.

A completion notice must also give direction: state what was completed and its outcome, then either suggest the next step in the same two-block shape (when follow-up work is identifiable from repository state, the roadmap, or owner direction) or state explicitly that no follow-up work is identifiable. It SHOULD suggest a documentation round when the completed work changed durable behavior or decisions, but not for every completion. Never invent scope the owner has not indicated.

For a completion suggestion, "same two-block shape" means only the advisory execution block plus the pasteable prompt block; it does not mean to reuse the incomplete-task instruction to open and update the completed task file. A prompt for a suggested new task must direct the human-started next round to create a uniquely numbered artifact from `assets/task-template.md` in `spartan/tasks/` and must not open, reopen, or update the completed artifact. A prompt may instead name an existing `active` task; in that case, it must reference and update that active artifact, never the completed one. The human may decline the suggestion, choose another host or model, or start no further round.

## Keep external actions explicit

Commit, push, open a pull request, merge, deploy, or perform another material external action only when the user explicitly requests that action for the current round. Never make those actions a default consequence of using Spartan.
