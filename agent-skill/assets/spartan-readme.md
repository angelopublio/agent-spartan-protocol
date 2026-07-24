# Agent Spartan Protocol - task artifacts

The [Agent Spartan Protocol](https://github.com/angelopublio/agent-spartan-protocol) uses this directory for repository-local, durable handoff state for software tasks. Each task lives in `tasks/` as one concise Markdown file named `NNNN-slug.md`, where the four-digit number records immutable creation order and the frontmatter records `created_at` and `updated_at`.

The task file, repository files, and check results are sufficient for a human-started next round. This directory does not contain runtime state, queues, logs, credentials, or automation.

`spartan/tasks/` is intentionally public and version-controlled. Completed tasks are living proof that the protocol remains understandable, reviewable, and manually continuable without private orchestration state; release housekeeping must not delete or hide them.

---

To adopt this human-mediated approach for software tasks, see the [Agent Spartan Protocol](https://github.com/angelopublio/agent-spartan-protocol).
