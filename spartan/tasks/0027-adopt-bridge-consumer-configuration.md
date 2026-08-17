---
protocol: "0.6.1" # x-release-please-version
id: adopt-bridge-consumer-configuration
created_at: 2026-08-17
status: active
phase: reviewing
task_type: implementation
risk: high-impact
current_role: implementer
next_role: independent-reviewer
updated_at: 2026-08-17
handoff_id: none
next_handoff_id: HX-001
---

# Adopt Bridge consumer configuration in this repository's instructions

## Objective

This repository's root `AGENTS.md` is a working Spartan Bridge consumer configuration: it carries both the existing `## Agent hosts` section and a `## Spartan Bridge automation authority` section whose authorising sentences match the runtime byte-for-byte, `.spartan-bridge/` is gitignored, the consumer-versus-protocol decision is recorded here, and `git status --short` is clean after a path-scoped commit.

## Context

An uncommitted `## Agent hosts` section already sat in root `AGENTS.md`, added by hand and flagged by task `0026`'s review as unowned working-tree state. That review asked for a separate artifact and a separate commit so the verdict-vocabulary `feat!:` release would not sweep it in. This task owns that section and completes it; it does not reopen or update `0026`.

The creating prompt carried no handoff identifier. This artifact did not exist yet, so no compare-before-change mismatch applied.

This repository is the public reference other repositories copy. The decision below is the substance of the round; the Markdown mechanics follow it.

## Scope

- Root `AGENTS.md`: keep the hand-edited `## Agent hosts` section, fix the double blank line before it, and add `## Spartan Bridge automation authority`.
- Root `.gitignore`: ignore `.spartan-bridge/`.
- This task artifact.
- One Conventional Commit scoped to those three paths.

## Out of Scope

- Any file under `agent-skill/`, including protocol, skill, templates, and packaging.
- Any other file under `spartan/tasks/`.
- Push, tag, pull request, merge, or release.
- The pending release proposal for the already-committed verdict-vocabulary change.
- The companion Bridge project.

## Constraints

- The three authorising sentences, when included, are list items reproduced byte-for-byte.
- Cycle count is one of `1`, `2`, or `3`.
- The `task_artifact_write` sentence is included only if this repository intends the runtime to persist validated reviewer findings here.
- The exact sentence `The human starts every round.` must not appear as a standalone line in `AGENTS.md`.
- Persisted content is English.
- Commit is authorized for this round and only for `AGENTS.md`, `.gitignore`, and this artifact.

## Acceptance Criteria

- [x] Root `AGENTS.md` carries `## Agent hosts` and `## Spartan Bridge automation authority`.
- [x] The authorising list items in `AGENTS.md` match the runtime sentences byte-for-byte, including the chosen cycle count and, if granted, the `task_artifact_write` sentence.
- [x] The exact sentence `The human starts every round.` does not appear as a standalone line in `AGENTS.md`.
- [x] `.gitignore` lists `.spartan-bridge/`.
- [x] The consumer-versus-protocol decision is recorded in Decisions below.
- [x] Protocol files under `agent-skill/` are untouched.
- [x] A path-scoped Conventional Commit of `AGENTS.md`, `.gitignore`, and this artifact is the only new commit, and `git status --short` is clean.
- [ ] A reviewer records an explicit `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED` verdict.

## Decisions

- This configures this repository as a Bridge consumer and changes nothing about the portable protocol. The distinction holds because `AGENTS.md` is repository instruction, not protocol content: adopters install `agent-skill/skills/spartan/`, not this file. The protocol already allows a handoff to be executed by an external, human-installed tool that it does not depend on, so granting a human-started Bridge run the authority to start a mapped reviewer is a consumer choice, not a protocol revision. Declaring specific hosts and an automatic-review grant is defensible here for the same reason, even though this repository is the public reference and the file will be read as exemplary. Copying this file is copying one repository's consumer example; it is not a requirement of using Spartan. If Bridge is absent, the protocol continues unchanged — the deletion test still passes — and the human still starts every Bridge run. No host binding, cycle count, or write grant in this file becomes a protocol rule.
- Cycle count is `3`. `1` would deny the producer a chance to address `CHANGES_REQUESTED` inside the same human-started run, making the grant mostly theatrical. `2` covers the common one-fix pattern. `3` is the runtime ceiling and is chosen because this repository's work is small Markdown where a leftover wording finding after two cycles is common enough that the extra pass is cheaper than another human-started run. The prose stopping conditions are the real bounds; `3` is a cap, not a target.
- Grant `task_artifact_write`. Without it, automatic review cannot persist validated verdicts into handoff truth, and the human would still paste findings by hand. The grant is only for persisting validated reviewer findings and transition metadata to the explicitly identified current task artifact.
- Adopt the hand-edited `## Agent hosts` table as written (bindings, the `personal` alias paragraph, and the same-Cursor-host context-separation note). Completing the configuration means adding the parsed authority section and ignoring machine-local runtime state, not redesigning the host map.
- Further constraints stay ordinary prose, not extra list items, because only the authorising sentences are parsed. The Bridge stops on `APPROVED`, `BLOCKED`, exhausted cycles, or a finding that would broaden the current round's authorization. It must not start a planner, implementer, or any other non-reviewer role. A human starts every implementation round and remains the only authority for commit, push, tag, pull request, merge, or release. The Bridge writes only the current task artifact named by the run.

## Work Completed

- Created this artifact as `spartan/tasks/0027-adopt-bridge-consumer-configuration.md`. Recorded the consumer-versus-protocol decision before editing repository files.
- Fixed the double blank line before `## Agent hosts`. Left that section's table, `personal` alias paragraph, and context-separation note as written. Added `## Spartan Bridge automation authority` with the three authorising list items and prose stopping conditions, including the human-only gate before implementation.
- Added `.spartan-bridge/` to `.gitignore`.
- Path-scoped Conventional Commit of `AGENTS.md`, `.gitignore`, and this artifact. Did not push, tag, or open a pull request, and did not touch the pending release proposal or any other task artifact.
- This round ran in Cursor on Grok 4.6 (high effort; vendor attribution joint Cursor/SpaceXAI).

## Evidence

- Creating prompt named slug `adopt-bridge-consumer-configuration` and forbade opening or updating any other task artifact. Highest existing creation number was `0026`.
- Authorising list items present byte-for-byte in `AGENTS.md`. Standalone line `The human starts every round.` is absent (`raw_occurrences: 0`). Double blank line before `## Agent hosts` removed. `.gitignore` is `.idea`, `.DS_Store`, `.spartan-bridge/`.
- `git diff --check`: clean. `git diff --name-only -- agent-skill`: empty. `git status --short -- spartan/tasks/` listed only this new artifact before the commit.
- Risk is `high-impact` because this is an authorization grant that will be read as exemplary; rollback is deleting the consumer section, but the meaning is the grant, not the diff size.

## Review

Verdict: PENDING

Findings:

- None recorded.

## Blockers

None.

## Next Action

Independent-reviewer, read-only: issue an explicit `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED` verdict on the recorded decision, the three authorising sentences, the cycle-count and write-grant choices, the prose constraints, the gitignore entry, and confirmation that protocol files were not modified.

## Next Handoff

```text
Recommended execution (human decides):
- Host: Claude Code (fresh-context independent review; Anthropic vendor differs from this Cursor/Grok implementation round)
- Model and effort: Claude Opus, high effort (fallback: Sonnet 5 if Opus is unavailable)
- Role: independent-reviewer
- Handoff: HX-001
- Invocation: `/spartan`, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/0027-adopt-bridge-consumer-configuration.md` (handoff HX-001).

Act as independent-reviewer. Read-only review of the committed Bridge consumer configuration: the recorded decision that this is repository instruction not protocol, the three authorising sentences byte-for-byte, the cycle-count and task_artifact_write choices, the prose constraints, absence of the conflicting standalone line, `.spartan-bridge/` in `.gitignore`, and that protocol files under `agent-skill/` were not modified. Success is an explicit APPROVED, CHANGES_REQUESTED, or BLOCKED verdict in this artifact.
Run the relevant repository checks and update the same task file.

Return only the next handoff, or a completion notice if no work remains.
```
