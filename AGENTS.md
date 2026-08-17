# Repository Instructions

## Language

- Write all repository documentation, skill instructions, templates, task artifacts, review verdicts, handoffs, configuration descriptions, and code comments in English.
- Conversation with the repository owner may occur in Portuguese, but persisted repository content remains English.
- This rule applies to `agent-spartan-protocol`. It does not force unrelated target-product user interfaces or localized content to use English.

## Product boundary

Spartan coordinates information, not processes. It may recommend, record, and hand off the next step. It must not initiate, supervise, resume, retry, or guarantee execution by another agent.

Keep the protocol static, host-neutral, human-mediated, and repository-local. Do not add runtime code, daemons, background workers, watchers, polling, schedulers, nested LLM execution, automatic cross-host calls, queues, locks, databases, event sourcing, authentication handling, credential projection, model-quota routing, custom sandboxing, automatic patch adoption, or automatic external actions.

## Sources of truth

- Repository files, the current diff, and check results are implementation truth.
- `spartan/tasks/*.md` is handoff truth.
- Conversation history is ephemeral and must not be required for continuation.

## Agent hosts

| Binding | Host | Client context |
| --- | --- | --- |
| planner | Claude Code | personal |
| reviewer.plan | Cursor | personal |
| implementer | Cursor | personal |
| reviewer.implementation | Cursor | personal |

`personal` is an opaque client-context alias. It identifies an externally prepared official-client launch context; it is not a credential, provider account ID, email address, or built-in Bridge account type. Repository content may select this alias but may not define launcher commands, authentication paths, credential variables, tokens, cookies, or API keys.

Each author round and the reviewer round that follows it must use fresh, separate execution contexts. Where one host holds both bindings, that separation is session isolation only, not cross-vendor independence.

## Spartan Bridge automation authority

This section configures this repository as a Spartan Bridge consumer. It changes nothing about the portable protocol, which remains static, host-neutral, and human-mediated. `AGENTS.md` is repository instruction; protocol content lives under `agent-skill/skills/spartan/`. The protocol already allows a handoff to be executed by an external, human-installed tool that it does not depend on, and that is the only authority this section grants.

- A human-started Spartan Bridge run may start the mapped reviewer automatically.
- The Bridge may return findings to the current producer and repeat up to 3 review cycles.
- This run grants the Bridge `task_artifact_write` only for persisting validated reviewer findings and transition metadata to the explicitly identified current Spartan task artifact.

The grant ends when a reviewer records `APPROVED` or `BLOCKED`, when the three cycles are exhausted, or when applying a finding would broaden the current round's authorization. The Bridge must not start a planner, implementer, or any other non-reviewer role. A human starts every implementation round and remains the only authority for commit, push, tag, pull request, merge, or release. The Bridge writes only the current task artifact named by the run; it does not create tasks, edit other artifacts, or modify files under `agent-skill/`.

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
