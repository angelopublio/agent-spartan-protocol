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

## Change discipline

- Prefer concise Markdown instructions, references, and templates.
- Keep `agent-skill/SKILL.md` preferably below 200 lines.
- Do not add `src/`, a package manager manifest, runtime dependencies, or executable task tooling without an explicit decision to build a separate product.
- Host-specific notes, if later justified, must remain optional and documentation-only.
- A proposed feature belongs here only when it passes every scope test in `agent-skill/references/protocol.md`.
