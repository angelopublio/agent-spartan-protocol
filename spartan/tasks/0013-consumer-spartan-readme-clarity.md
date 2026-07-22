---
protocol: "0.3.0"
id: consumer-spartan-readme-clarity
created_at: 2026-07-22
status: completed
phase: complete
task_type: implementation
risk: routine
current_role: reviewer
next_role: human-operator
updated_at: 2026-07-22
---

# Make the spartan/ directory self-explanatory in adopting repositories

## Objective

A reader who encounters a `spartan/` directory in any repository that adopts the protocol can immediately learn, from `spartan/README.md` alone, what the directory is, that it belongs to the Agent Spartan Protocol, and where the protocol lives.

## Context

- The current `spartan/README.md` explains the directory but never names the Agent Spartan Protocol nor links to its repository, wasting the natural discovery moment when someone browses an adopting repo.
- Nothing in the skill ships this README into adopting repositories today: `agent-skill/SKILL.md` only instructs copying `assets/task-template.md` into `spartan/tasks/`. The root `README.md` ("The `spartan/README.md` file explains this durable task namespace") describes a file that only exists in this protocol repository.
- Owner direction: the README is a distribution artifact — it must serve the reader first and promote second (protocol name plus one repository link; no badges, slogans, or version numbers that go stale).

## Scope

- Rewrite `spartan/README.md` in this repository with: h1 `# Agent Spartan Protocol — task artifacts`; a first sentence naming the Agent Spartan Protocol with a link to `https://github.com/angelopublio/agent-spartan-protocol`; the existing explanations (task naming convention, no runtime state/credentials, intentionally version-controlled, do not delete completed tasks); and a short footer separated by `---` inviting readers to adopt the protocol, with the same link.
- Add `agent-skill/assets/spartan-readme.md` containing that same README content, as the copy shipped to adopting repositories.
- Add one instruction to `agent-skill/SKILL.md` (and, if the contract belongs there, `agent-skill/references/protocol.md`): when creating the `spartan/` directory for the first time in a target repository, also copy `assets/spartan-readme.md` to `spartan/README.md`.

## Out of Scope

- Badges, protocol version stamps, or any promotional content beyond the protocol name and repository link.
- Changes to the handoff contract, routing, or task template.
- Restructuring the root `README.md` beyond keeping its `spartan/README.md` mention accurate.

## Constraints

- All persisted content in English; use "software tasks" (the protocol's established vocabulary), not "coding tasks".
- The h1 carries the full protocol name but no link, badge, or slogan; links live in the first sentence and the footer only.
- Keep `agent-skill/SKILL.md` preferably below 200 lines.
- The shipped README must remain accurate standalone in a consumer repository (no references that only resolve inside this protocol repo).

## Acceptance Criteria

- [x] `spartan/README.md` names the Agent Spartan Protocol in the h1 and first sentence and links to the protocol repository in the first sentence and a `---`-separated footer.
- [x] `agent-skill/assets/spartan-readme.md` exists and its content works standalone in an adopting repository.
- [x] `agent-skill/SKILL.md` instructs copying `assets/spartan-readme.md` to `spartan/README.md` when `spartan/` is first created in a target repository.
- [x] The root `README.md` statement about `spartan/README.md` remains accurate after the change.
- [x] `agent-skill/SKILL.md` stays below 200 lines.

## Decisions

- Title uses the full name with an em dash — `# Agent Spartan Protocol — task artifacts` — because the h1 is the most visible line when browsing the directory on GitHub and "Spartan" alone is a collision-prone token. (Owner, 2026-07-22)
- Promotion dose is fixed at protocol name plus one repository link (repeated in the footer); anything more risks deletion by consumer-repo maintainers. (Owner, 2026-07-22)
- No protocol version number in the consumer README: it would go stale on the next release; the link is sufficient. (Owner, 2026-07-22)
- Ship the README as a skill asset because the skill currently never creates `spartan/README.md` in target repositories, so the clarity would otherwise stay confined to this repo. (Planner, 2026-07-22)
- The first-creation copy instruction belongs in `agent-skill/SKILL.md`; `references/protocol.md` remains the host-neutral behavioral contract and does not prescribe asset bootstrapping. (Implementer, 2026-07-22)

## Work Completed

- Task authored in Claude Code (cockpit round, Fable 5) after owner discussion settling title, wording, and promotion dose.
- Rewrote `spartan/README.md` as the standalone consumer-facing explanation, with the required title, repository links, retained namespace guidance, and adoption footer.
- Added byte-identical `agent-skill/assets/spartan-readme.md` for distribution to adopting repositories.
- Updated `agent-skill/SKILL.md` to copy that asset to `spartan/README.md` when it first creates `spartan/` in a target repository.
- Implemented in Codex during the current foreground round.

## Evidence

- `cmp -s spartan/README.md agent-skill/assets/spartan-readme.md`: passed; the repository README and distributed asset are byte-identical.
- `wc -l agent-skill/SKILL.md`: passed; 81 lines, below the 200-line constraint.
- Content scan with `rg`: passed; both README copies have the required h1, first-sentence repository link, `---` footer, and adoption link; the skill has the first-creation copy instruction; root `README.md` continues to describe `spartan/README.md` as the durable task namespace explanation.
- `awk` trailing-whitespace scan of the changed Markdown files and `git diff --check`: passed.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: passed.

## Review

Verdict: APPROVE

Reviewed in Claude Code (fresh cross-vendor reviewer, Opus, high effort) against the Codex implementation.

Findings:

- None. All five acceptance criteria verified against the diff; every recorded check reproduced with the same outcome (`cmp` byte-identical, SKILL.md 81 lines, `git diff --check` clean, no trailing whitespace, both `claude plugin validate --strict` pass). Root `README.md:124` remains accurate — the rewritten README still explains the durable task namespace. Both README copies carry the plain-text h1, first-sentence repository link, `---` footer, and adoption link; the h1 stays link/badge/slogan-free; wording uses "software tasks". No out-of-scope changes.

## Blockers

None.

## Next Action

None. Review approved; task completed. Changes remain uncommitted — committing/pushing is the owner's explicit choice, not a protocol default.
