---
protocol: "0.6.1" # x-release-please-version
id: adopt-bridge-consumer-configuration
created_at: 2026-08-17
status: completed
phase: complete
task_type: implementation
risk: high-impact
current_role: independent-reviewer
next_role: none
updated_at: 2026-08-17
handoff_id: HX-001
next_handoff_id: none
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
- [x] A reviewer records an explicit `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED` verdict.

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
- Independent review round: read-only verification of the committed configuration against the Bridge parser and this repository's own instructions. No repository file was changed; only this artifact was updated. The pasted prompt carried `HX-001` and matched `next_handoff_id`. Ran in Claude Code on Claude Opus, high effort (Anthropic vendor, independent of the implementing Cursor/Grok round).

## Evidence

- Creating prompt named slug `adopt-bridge-consumer-configuration` and forbade opening or updating any other task artifact. Highest existing creation number was `0026`.
- Authorising list items present byte-for-byte in `AGENTS.md`. Standalone line `The human starts every round.` is absent (`raw_occurrences: 0`). Double blank line before `## Agent hosts` removed. `.gitignore` is `.idea`, `.DS_Store`, `.spartan-bridge/`.
- `git diff --check`: clean. `git diff --name-only -- agent-skill`: empty. `git status --short -- spartan/tasks/` listed only this new artifact before the commit.
- Risk is `high-impact` because this is an authorization grant that will be read as exemplary; rollback is deleting the consumer section, but the meaning is the grant, not the diff size.

Review round (read-only):

- Ran the Bridge parser `src/policy/agents-policy.ts` from the companion runtime against this repository's committed `AGENTS.md`: `ok: true`, `host: cursor`, `client_context: personal`, `automatic_review_authorized: true`, `task_artifact_write_authorized: true`, `max_review_cycles: 3`. This is the authoritative byte-for-byte test; the runtime accepts the file with exactly the intended values.
- Independent line-level check against the parser's literals: automatic-review grant exact-match count `1`, `task_artifact_write` grant exact-match count `1`, cycle item matches the pinned regex with `3`. No duplicates, which would be `agents_policy_invalid`.
- `The human starts every round.` appears zero times as a trimmed standalone line and zero times as a substring anywhere in `AGENTS.md`.
- `AGENTS.md` is ASCII-only, LF-only, has no trailing whitespace, and ends with one newline, so no invisible character can break the literal match.
- `.gitignore` is `.idea`, `.DS_Store`, `.spartan-bridge/`, now with a trailing newline (the previous file had none).
- `git diff --name-only HEAD~1 HEAD`: exactly `.gitignore`, `AGENTS.md`, and this artifact. Nothing under `agent-skill/`. `git status --short`: clean. `git log --oneline -1`: `docs: configure this repository as a Spartan Bridge consumer`, a valid Conventional Commit type per `AGENTS.md`.
- `release-please-config.json` tracks only the `agent-skill` package, so a root-level `docs:` commit cannot affect the pending release proposal for the verdict-vocabulary change.
- Cross-checked the prose stopping conditions against the parser's behaviour: extra list items in the automation section are ignored by the parser, so keeping the further constraints as prose is correct and carries no parsing risk either way.

## Review

Verdict: APPROVED

The consumer-versus-protocol decision holds. `AGENTS.md` is repository instruction and is not shipped to adopters, who install `agent-skill/skills/spartan/`. The protocol already permits a handoff to be executed by an external, human-installed tool it does not depend on, and this section grants nothing beyond that: the deletion test still passes, no protocol file changed, and no host binding, cycle count, or write grant became a protocol rule. The cycle count of `3` is inside the parser's `1|2|3` domain and is argued as a ceiling rather than a target, with the prose stopping conditions carrying the real bound. The `task_artifact_write` grant is the narrow one the runtime defines and is scoped in prose to the single named current artifact. The three findings below are documentation follow-ups outside this task's declared scope; none is a defect in what was committed, and acting on them here would broaden this round's authorization.

Findings:

1. `## Agent hosts` now carries two incompatible published meanings inside this repository. `README.md` documents it as a `| Role | Host |` table of Spartan roles that is explicitly "routing preferences, not execution authority"; the committed `AGENTS.md` uses `| Binding | Host | Client context |` with Bridge binding names (`reviewer.plan`, `reviewer.implementation`) that Spartan's own routing does not define, parsed by an external runtime. Each is correct for its own reader, and neither mentions the other. Because this repository is the public reference, a follow-up round should reconcile the two in `README.md`.
2. `README.md` states the one rule shaping any host declaration — a reviewer must not be the host that produced the work under review — and, for two hosts, prescribes saying so rather than pinning the reviewer. The committed table pins `implementer` and `reviewer.implementation` both to Cursor. `AGENTS.md` discloses the consequence in plain words ("context separation, not cross-host or cross-vendor independence"), so it does not mislead, and Bridge bindings are a different axis from Spartan's advisory routing. It remains the shape the reference repository's own README tells adopters to avoid, and belongs in the same follow-up as Finding 1.
3. Interaction note, no change requested here. `README.md`'s prescribed closing line ends with `The human starts every round.` preceded by another sentence on the same line. The Bridge disables automatic review only when that sentence is the entire trimmed line, so the README form is safe as written, and this repository correctly avoids the standalone form. The coupling is invisible from either file and deserves one sentence if Finding 1 is taken up.

## Blockers

None.

## Next Action

None. Every acceptance criterion is satisfied, the relevant checks have recorded outcomes, the review verdict is `APPROVED`, and no blocker remains. The three findings are follow-up documentation work for a separate task, not remaining work on this one.

## Next Handoff

No outstanding handoff. This task is complete.

Non-binding suggestion for a separate new task, from Findings 1-3: reconcile `README.md` with the committed `AGENTS.md` so the repository's public reference and its own instructions agree on what `## Agent hosts` means.

```text
Recommended execution (human decides):
- Host: Claude Code (documentation round in the repository's public reference, framed with the human)
- Model and effort: Claude Opus, high effort (fallback: Sonnet 5 if Opus is unavailable)
- Role: planner
- Invocation: `/spartan`, passing the prompt block below as the argument
```

```text
Create a new uniquely numbered task from `assets/task-template.md` in `spartan/tasks/`.

Act as planner. This repository's `README.md` documents `## Agent hosts` as a `| Role | Host |` table of Spartan roles that is routing preference and never execution authority, and states that a reviewer must not be the host that produced the work under review. The committed root `AGENTS.md` now also carries `## Agent hosts` as a `| Binding | Host | Client context |` table of Bridge bindings that pins Cursor as both implementer and implementation reviewer, plus a `## Spartan Bridge automation authority` section. Define the scope, constraints, and acceptance criteria for reconciling the two so an adopter reading either file understands which one applies to them, and decide whether the Bridge table should keep the `## Agent hosts` heading at all. Success is a new artifact a later round can act on without conversation history. Do not reopen or update `spartan/tasks/0027-adopt-bridge-consumer-configuration.md`, and do not commit in this round.
Run the relevant repository checks and update the new task file.

Return only the next handoff, or a completion notice if no work remains.
```
