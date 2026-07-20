# Spartan task artifacts

This directory holds the repository-local, durable handoff state for Spartan. Each task lives in `tasks/` as one concise Markdown file named `NNNN-slug.md`, where the four-digit number records immutable creation order and the frontmatter records `created_at` and `updated_at`.

The task file, repository files, and check results are sufficient for a human-started next round. This directory does not contain runtime state, queues, logs, credentials, or automation.
