# Repository Instructions

## Language

- Write all repository documentation, skill instructions, templates, task artifacts, review verdicts, handoffs, configuration descriptions, and code comments in English.
- Conversation with the repository owner may occur in Portuguese, but persisted repository content remains English.
- This rule applies to `agent-spartan-protocol`. It does not force unrelated target-product user interfaces or localized content to use English.

Shared agent tooling and working conventions live in `~/Documents/DEV/agent-scripts`; read its `AGENTS.md` before anything (skip if missing).

## Product boundary

Spartan coordinates information, not processes. It may recommend, record, and hand off the next step. It must not initiate, supervise, resume, retry, or guarantee execution by another agent.

Keep the protocol static, host-neutral, human-mediated, and repository-local. Do not add runtime code, daemons, background workers, watchers, polling, schedulers, nested LLM execution, automatic cross-host calls, queues, locks, databases, event sourcing, authentication handling, credential projection, model-quota routing, custom sandboxing, automatic patch adoption, or automatic external actions.

## Sources of truth

- Repository files, the current diff, and check results are implementation truth.
- `spartan/tasks/*.md` is handoff truth.
- Conversation history is ephemeral and must not be required for continuation.

## Agent hosts

| Binding | Host | Client context | Model | Effort |
| --- | --- | --- | --- | --- |
| planner | Cursor | personal | cursor-grok-4.6-high-fast | high |
| reviewer.plan | Claude Code | personal | claude-opus-5 | high |
| implementer | Cursor | personal | cursor-grok-4.6-high-fast | none |
| reviewer.implementation | Claude Code | personal | claude-opus-5 | high |

`personal` is an opaque client-context alias. It identifies an externally prepared official-client launch context; it is not a credential, provider account ID, email address, or built-in Bridge account type. Repository content may select this alias but may not define launcher commands, authentication paths, credential variables, tokens, cookies, or API keys.

Each author round and the reviewer round that follows it must use fresh, separate execution contexts. Where one host holds both bindings, that separation is session isolation only, not cross-vendor independence.

## Spartan Bridge automation authority

This section configures this repository as a Spartan Bridge consumer. It changes nothing about the portable protocol, which remains static, host-neutral, and human-mediated. `AGENTS.md` is repository instruction; protocol content lives under `agent-skill/skills/spartan/`. The protocol already allows a handoff to be executed by an external, human-installed tool that it does not depend on, and that is the only authority this section grants.

- The human starts the planner producer phase of a chain; later plan-correction cycles continue inside that same human-started planner session. Each authorized automatic implementation or correction producer uses a fresh mapped execution in the same foreground Bridge run.
- A human-started Spartan Bridge run may start the mapped reviewer automatically.
- This run grants the Bridge `task_artifact_write` only for persisting validated reviewer findings and transition metadata to the explicitly identified current Spartan task artifact.
- The Bridge may return findings to the current planner session and repeat up to 3 plan-review cycles.
- After a persisted plan-review pass, the Bridge may return implementation findings to a fresh mapped implementer execution and repeat up to 3 implementation-review cycles.
- A producer round that received findings from a Spartan Bridge run may start the next review run automatically within the authorized cycle limit.
- After an automatic implementer correction declaration, the Bridge may start the next implementation review automatically within the authorized implementation-review cycle limit.
- The Bridge must stop before changing the producer role or producer host, except for the mapped plan-pass implementer transition and its mapped implementation correction executions.
- After a human-started planner phase and a persisted `reviewer.plan: pass`, the Bridge may start the mapped `implementer`, may start the mapped `reviewer.implementation` after the implementer's declaration, and may return implementation findings to a fresh mapped implementer execution within the independent implementation-review cycle ceiling. Automatic implementation requires an unchanged approved-plan hash and an exclusive worktree lock.
- A human-started Spartan Bridge run may start the mapped `reviewer.implementation` automatically when the current Spartan task artifact declares the implementer round finished and the reviewer round next; the Bridge takes that declaration as the implementer's assertion that the required checks passed, and does not verify it. That reviewer is given a read-only copy of the working-tree file content admitted by the implementation review scope declared below, excluding only `.git` metadata; repository ignore rules do not classify or remove files, ancestor directories are created only as containers for admitted files, and no repository file path outside that scope appears in the copy or in any other file the reviewer is given.

A human remains the only authority for commit, push, tag, pull request, merge, or release. `task_artifact_write` still writes only the current task artifact named by the run; it does not create tasks, edit other artifacts, or modify files under `agent-skill/`. Product-file writes belong to the mapped implementer under the automatic write scope below.

### Automatic implementation write scope

- `agent-skill/`
- `docs/`
- `README.md`
- `spartan/`

### Implementation review scope

- `agent-skill/`
- `docs/`
- `README.md`
- `spartan/`
- `AGENTS.md`
- `spartan-bridge/config.yaml`

## Repository workflow

- Before starting work, agents SHOULD run `git pull --ff-only` when `git status --short` shows a clean working tree. If the tree is not clean, preserve the existing changes and do not pull.
- Every commit MUST follow Conventional Commits. Use the `feat`, `fix`, `docs`, or `chore` type, with an optional scope. Use `feat!:` or a `BREAKING CHANGE:` footer for a breaking change.

## Change discipline

- Prefer concise Markdown instructions, references, and templates.
- Keep the package root and the skill folder separate. `agent-skill/` holds packaging only (`.claude-plugin/plugin.json`, `CHANGELOG.md`, `version.txt`); everything the skill itself ships belongs under `agent-skill/skills/spartan/`. Do not add skill content at the package root: a plugin root reserves names such as `agents/`, `commands/`, `hooks/`, and `skills/`, and `skills/<name>/` is the layout every host discovers without a manifest hint.
- Keep `agent-skill/skills/spartan/SKILL.md` preferably below 200 lines.
- Do not add `src/`, a package manager manifest, runtime dependencies, or executable task tooling without an explicit decision to build a separate product.
- Host-specific notes, if later justified, must remain optional and documentation-only.
- A proposed feature belongs here only when it passes every scope test in `agent-skill/skills/spartan/references/protocol.md`.
