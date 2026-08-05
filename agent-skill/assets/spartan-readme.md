# Agent Spartan Protocol - task artifacts

The [Agent Spartan Protocol](https://github.com/angelopublio/agent-spartan-protocol) uses this directory for repository-local, durable handoff state for software tasks. Each task lives in `tasks/` as one concise Markdown file named `NNNN-slug.md`, where the four-digit number records immutable creation order and the frontmatter records `created_at` and `updated_at`.

The task file, repository files, and check results are sufficient for a human-started next round. This directory does not contain runtime state, queues, logs, credentials, or automation.

A task's frontmatter MAY carry a compact `HX-NNN` handoff identifier (`handoff_id`, `next_handoff_id`) so a receiving round can detect a stale pasted prompt before acting on it; older tasks without these fields remain valid as written.

When a repository prefers particular hosts for particular Spartan roles, that preference belongs in the repository's own instructions file, such as `AGENTS.md`, not under `spartan/`.

When a task settles something about how information is presented, its point-in-time design material - a screen brief, a layout choice, a question left to a designer - lives in `design/`. Standards that must stay true as the product changes are maintained documentation and belong to this repository's own design documentation instead, and nothing a design tool returns is stored here.

`spartan/tasks/` is intentionally public and version-controlled. Completed tasks are living proof that the protocol remains understandable, reviewable, and manually continuable without private orchestration state; release housekeeping must not delete or hide them.

---

To adopt this human-mediated approach for software tasks, see the [Agent Spartan Protocol](https://github.com/angelopublio/agent-spartan-protocol).
