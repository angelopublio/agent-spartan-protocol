---
protocol: "0.3.0"
id: readme-diagram-and-dev-install-split
created_at: 2026-07-21
status: completed
phase: complete
task_type: implementation
risk: routine
current_role: implementer
next_role: human-operator
updated_at: 2026-07-21
---

# Restructure README top: handoff diagram and dev-install split

## Objective

The README opens with a Mermaid sequence diagram that shows the human-mediated handoff loop, and the development/symlink installation instructions live in `docs/INSTALL-DEV.md` with a one-line link from the README.

## Context

The owner wants the README optimized for first-time visitors: the core concept (two hosts, one task artifact, human in the middle) should be visible in seconds, and contributor-only install detail should not occupy a third of the README. Mermaid was chosen over a generated image because it renders natively on GitHub, is versionable, and matches the project's spartan aesthetic.

## Scope

- Add a Mermaid sequence diagram to `README.md` after the opening paragraphs.
- Move the entire "Development / fallback install (symlinks)" section, including "Project-local alternative", into a new `docs/INSTALL-DEV.md`.
- Leave a short linking section in `README.md` pointing to the new document.

## Out of Scope

- Any change to the skill package under `agent-skill/`.
- Badges, GIF/asciinema assets, or further README restructuring.
- Commit or push (owner performs external actions explicitly).

## Constraints

- Docs-only, reversible change; persisted content in English.
- Moved content is relocated verbatim except for headings adjusted to the new document.

## Acceptance Criteria

- [x] `README.md` renders a Mermaid sequence diagram showing: human starts each session, host performs one bounded round against `spartan/tasks/NNNN-<slug>.md`, human manually pastes the handoff to the next host.
- [x] `docs/INSTALL-DEV.md` contains the full symlink install instructions previously in `README.md`, including the project-local alternative.
- [x] `README.md` no longer contains the symlink instructions and instead links to `docs/INSTALL-DEV.md`.
- [x] All relative links in the touched files resolve.

## Decisions

- Mermaid over an AI-generated image: native GitHub rendering, theme-aware, versionable, consistent with the protocol's no-magic positioning.
- Contributor install detail belongs in `docs/` alongside `DOGFOODING.md`; the README keeps only the adoption paths (marketplace and skills CLI).

## Work Completed

- Round executed in Claude Code (model: Claude Fable 5) as implementer; recommended and executed host match.
- Diagram added, section moved, link section added; see Evidence.

## Evidence

- `grep -c '```mermaid' README.md`: 1 (diagram block present).
- Relative-link check over `README.md` and `docs/INSTALL-DEV.md`: all targets resolve (`RELEASING.md`, `agent-skill/`, `docs/DOGFOODING.md`, `docs/INSTALL-DEV.md`, task links).
- `git status --short`: `M README.md`, new `docs/INSTALL-DEV.md`, new task file; no other changes.

## Review

Verdict: PENDING

Findings:

- Routine docs-only change; implementer self-verified and no independent review is required by routing for routine risk. Verdict remains PENDING because no review round was required or run.

## Blockers

None.

## Next Action

None. Task complete. Committing the change remains an explicit owner action outside this task's scope.

## Next Handoff

None. Completion notice delivered to the owner; an optional cross-vendor review round was suggested as a new task.
