---
protocol: "0.2.0"
id: claude-model-routing-by-risk
created_at: 2026-07-21
status: completed
phase: complete
task_type: review
risk: material
current_role: reviewer
next_role: human-operator
updated_at: 2026-07-21
---

# Verify Claude Code model recommendation is calibrated to risk, not host default

## Objective

`agent-skill/references/routing.md`'s model reference correctly recommends a Claude Code model tier based on the task's assessed risk (`routine` / `material` / `high-impact`), not on habitually naming the host's most capable model.

## Context

- Triggered by an observed miscalibration in task `0009-public-install-path.md`: an independent-reviewer handoff recommended "Claude Fable 5, medium effort" for a one-sentence README wording fix, a `routine`-risk correction. The human operator judged Sonnet sufficient and ran it there instead.
- Owner direction (2026-07-21, chat): Fable should be recommended only for `high-impact` risk or unusually complex/sophisticated reasoning; Opus is the default for cockpit/planning/review at high effort otherwise; Sonnet/Haiku remain the `routine` default.
- A first-pass edit was already applied in this session (Claude Sonnet 5) to `agent-skill/references/routing.md`: split the former combined "Fable 5 / Opus" row into separate Opus (default) and Fable 5 (reserved) rows, and added a bullet tying model tier to assessed risk rather than to the host's strongest model. This task tracks review/verification of that edit, executed with fresh reasoning in Fable.
- `agent-skill/references/protocol.md:127` already used "Claude Opus" in its example and needs no change.

## Scope

- Review the diff to `agent-skill/references/routing.md` (the "Current model reference" table and the "Beyond the default mapping" bullet list).
- Verify no other file in `agent-skill/` or repo-root docs hardcodes a stale Claude model recommendation that now conflicts with the new table.
- Confirm the routing guidance stays advisory-only (no enforcement language) and consistent with `protocol.md`'s handoff contract.

## Out of Scope

- Changing the Codex model tiers.
- Reopening task `0009-public-install-path.md` (separate task; already independent-reviewer APPROVE, awaiting the operator's commit/push round).
- Adding any automated/enforced model-selection logic — routing remains advisory per the protocol's scope admission test.

## Constraints

- Routing guidance must remain Markdown instructions only; no runtime enforcement, scoring, or model-quota routing (forbidden by `protocol.md`'s Allowed/Forbidden capabilities table).
- Keep the guidance concise; do not turn the table into an exhaustive decision tree.

## Acceptance Criteria

- [x] `agent-skill/references/routing.md` clearly states: Opus is the Claude Code default for cockpit/planning/review at high effort; Fable 5 is reserved for `high-impact` risk or unusually complex/sophisticated reasoning; Sonnet/Haiku is the `routine`/high-volume default.
- [x] Repo-wide scan confirms no stale or conflicting Claude model recommendation remains elsewhere.
- [x] A reviewer (Claude Fable 5, fresh reasoning) records an explicit APPROVE or CHANGES verdict on the calibration and wording.

## Decisions

- The correction was scoped as its own task rather than folded into 0009, because it changes durable skill-routing behavior unrelated to the install-path/README work in 0009.
- Reviewer for this task is intentionally the current host/vendor (Claude Code) rather than cross-vendor, because the subject is Claude-specific model-tier guidance where Codex has no comparable stake; the human operator explicitly requested Fable for this round.

## Work Completed

- Implementer round (Claude Sonnet 5, Claude Code, 2026-07-21): edited `agent-skill/references/routing.md` — split the model table's Claude Code row into Opus (default) and Fable 5 (reserved for `high-impact`/complex reasoning) rows, and added a bullet under "Beyond the default mapping" tying model tier to assessed risk. Repo-wide grep found only `protocol.md:127`'s existing "Claude Opus" example, consistent with the new table.
- Reviewer round (Claude Fable 5, Claude Code, 2026-07-21, read-only): verified the table and bullet against the acceptance criteria and owner direction, re-ran the repo-wide model-mention scan, confirmed the guidance stays advisory-only and consistent with `protocol.md`'s handoff contract, and recorded the verdict below.

## Evidence

- Repo-wide `grep -rn -i -E 'fable|opus|sonnet|haiku'` over all `.md`/`.yaml`/`.json` files (excluding `spartan/tasks/` and `routing.md` itself): one match, `agent-skill/references/protocol.md:127` ("Claude Opus, extended thinking" example) — consistent with the new table, no stale recommendation.
- `git diff HEAD -- agent-skill/references/routing.md`: empty. The calibrated table and bullet are already committed in HEAD (working tree clean apart from this task file), so no separate commit round is needed for routing.md.
- No automated repository checks exist (no CI workflows, Makefile, or package manifest); manual verification is the applicable check.

## Review

Verdict: APPROVE

Findings:

- Table rows are correctly calibrated: Opus is the stated default for cockpit/planning/code review at high effort; Fable 5 is explicitly reserved for `high-impact` risk or unusually complex reasoning with "do not default to it over Opus"; Sonnet/Haiku cover `routine`/high-volume rounds. This matches the owner direction of 2026-07-21.
- The "Match model tier to the task's assessed risk" bullet directly addresses the 0009 miscalibration (routine round recommended Fable) and correctly extends the principle to the Codex tiers.
- Language remains advisory-only (no enforcement, scoring, or quota logic) and consistent with the two-block handoff contract in `protocol.md`.
- Minor, non-blocking: the routing.md edit was already committed rather than pending as a working-tree diff; recorded here so no round waits on a nonexistent commit step.

## Blockers

None.

## Next Action

None. Task completed 2026-07-21 with reviewer verdict APPROVE; the calibrated routing guidance is already committed. Any commit/push ceremony for the untracked task files remains a separate human-directed round.
