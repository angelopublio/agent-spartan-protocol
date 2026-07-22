---
protocol: "0.3.0" # x-release-please-version
id: bind-invocation-token-to-host
created_at: 2026-07-22
status: completed
phase: complete
task_type: implementation
risk: routine
current_role: reviewer
next_role: human-operator
updated_at: 2026-07-22
---

# Bind the handoff invocation token to the recommended host

## Objective

A generated handoff never pairs a recommended host with another host's invocation token: `$spartan` appears only with Codex, `/spartan` (or `/spartan:spartan`) only with Claude Code, and hosts without skill support receive the direct prompt.

## Context

- Owner reported a real handoff that recommended Host: Claude Code but Invocation: `$spartan` (Codex's skill token). Claude Code has no `$spartan` command, so the human pastes an invocation that fails.
- Root cause: `agent-skill/SKILL.md`, `references/protocol.md`, `references/routing.md`, and `assets/task-template.md` all present the invocation as a free choice (`` `$spartan` | `/spartan` | direct prompt ``) with no binding to the host on the line above.
- `docs/INSTALL-DEV.md` already documents the correct per-host convention; the contract files simply never enforced it.
- The user-level install at `~/.claude/skills/spartan` symlinks to `agent-skill/`, so source fixes take effect immediately.

## Scope

- `agent-skill/SKILL.md`: handoff shape and binding rule.
- `agent-skill/references/protocol.md`: handoff contract wording and shape.
- `agent-skill/references/routing.md`: two-block delivery paragraph.
- `agent-skill/assets/task-template.md`: Next Handoff placeholder block.

## Out of Scope

- README, INSTALL-DEV, DOGFOODING (already host-correct in their own contexts).
- Any change to routing roles, models, or the two-block handoff structure itself.

## Constraints

- English-only artifacts; keep the two-block shape unchanged apart from the invocation binding.
- No commit or push in this round unless the owner asks.

## Acceptance Criteria

- [x] Every place that lists invocation styles states the host binding: `$spartan` = Codex, `/spartan` (or `/spartan:spartan`) = Claude Code, direct prompt = hosts without skill support.
- [x] The handoff contract forbids pairing a host with another host's token.
- [x] Review verdict is APPROVE.

## Decisions

- Express the binding inline in the shape's Invocation placeholder and as one normative sentence next to each shape, rather than adding a new section; keeps the contract compact.
- `/spartan:spartan` mentioned as the plugin-namespaced Claude Code form, matching README guidance.

## Work Completed

- Implemented in Claude Code (Fable 5) on 2026-07-22: edited the four in-scope files to bind the invocation token to the recommended host.
- Reviewed in Codex in a fresh cross-vendor review round; no implementation files were modified.

## Evidence

- `rg -n --glob '*.md' '(\$spartan|/spartan(?::spartan)?|[Ii]nvocation)' agent-skill`: passed; the complete scan found the template and both handoff shapes explicitly bind each token to its host, while the protocol and routing references state the same mapping.
- `git diff --check`: passed; no whitespace errors in the current diff.
- `claude plugin validate --strict agent-skill`: passed; the skill plugin manifest validates successfully.

## Review

Verdict: APPROVE

Findings:

- Every listing of invocation styles in the changed `agent-skill/` files now states the binding: `$spartan` for Codex, `/spartan` for Claude Code, and direct prompt for hosts without skill support. This includes `SKILL.md`, the task template, the protocol's recommended shape, and the routing summary.
- `references/protocol.md` now normatively requires the host's own convention and explicitly prohibits pairing a recommended host with another host's token; `SKILL.md` repeats the same prohibition in plain guidance. No remaining wording permits the reported Claude Code plus `$spartan` mismatch.
- The diff is confined to the four scoped contract/template files and preserves the two-block handoff structure.

## Blockers

- None.

## Next Action

None. Review approved; task completed. Changes remain uncommitted; committing or pushing requires the owner's explicit choice.

## Next Handoff

None. Completion notice delivered; no follow-up work is identifiable.
