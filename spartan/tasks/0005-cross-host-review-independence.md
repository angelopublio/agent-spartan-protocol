---
protocol_version: "0.1"
id: cross-host-review-independence
status: completed
phase: complete
task_type: implementation
risk: material
current_role: human-operator
next_role: human-operator
created_at: 2026-07-20
updated_at: 2026-07-20
---

# Prefer a different host for review than the one that produced the work

## Objective

Make explicit, in the canonical protocol, two complementary points: (1) an independent review (and any re-review after fixes) SHOULD run in a host — ideally a vendor — different from the one that produced or last modified the work under review; and (2) each round SHOULD record the host it actually ran in, so that the recommended host and the executed host are both visible and the different-host preference can be audited.

## Context

The owner observed that review handoffs did not clearly state that the reviewing host should differ from the host that ran the previous action. Existing guidance in `routing.md` gestured at this ("recommend a fresh session"/"another vendor") but never named the concrete rule for re-reviews. This task adds that rule as advisory guidance, consistent with the protocol's human-decides stance.

## Scope

- Add a normative `SHOULD` sentence to the Review section of `agent-skill/references/protocol.md`.
- Add an actionable bullet to the host-choice list in `agent-skill/references/routing.md`.
- Add to the Handoff contract of `agent-skill/references/protocol.md` a `SHOULD` that each executed round records the host and model it actually used, distinct from the recommended host, as a single current-snapshot note (not a per-round log).

## Out of Scope

- Any enforcement mechanism; the preference stays advisory and human-overridable.
- Runtime, host invocation, or model-routing behavior.
- Editing `SKILL.md` (kept lean; the detail lives in the references it already points to).

## Constraints

- All persisted content remains English.
- The rule MUST remain advisory: the human chooses the host and may override.
- No new controlled values, roles, or enforcement.

## Acceptance Criteria

- [x] `protocol.md` Review section states the different-host preference, including for re-reviews, as advisory.
- [x] `routing.md` host-choice list carries an actionable bullet for the same preference.
- [x] `protocol.md` Handoff contract states that each executed round records the host and model it actually used, distinct from the recommended host, as a current-snapshot note rather than a log.
- [x] A fresh-context independent review issues an explicit verdict. (Waived by the owner, who accepted the change directly — an explicit exercise of the advisory override the rule itself permits.)

## Decisions

- An independent review SHOULD run in a different host, preferably a different vendor, than the one that produced or last modified the work under review; this includes a re-review after fixes. Self-review correlates blind spots.
- The preference is advisory only, consistent with "the human decides the host". No technical enforcement is added.
- The rule is placed in `protocol.md` (normative) and `routing.md` (actionable); `SKILL.md` stays lean and continues to point to `routing.md` before role/host assignment.
- The recommended host (a suggestion, in the Next Handoff block) and the executed host (recorded by the round that runs, in its own outcome note) live in different places, so the artifact holds both without conflict. The executed-host note is prose in the existing round record — not a new frontmatter field and not a per-round log — to avoid schema growth and transcript drift.

## Work Completed

- Authored this task and implemented the edits in the same round (Claude Code cockpit, Claude Opus): the different-host `SHOULD` in `protocol.md` Review, the actionable bullet in `routing.md`, and the executed-host recording `SHOULD` in the `protocol.md` Handoff contract.

## Evidence

- `protocol.md` Review section now contains the different-host/different-vendor preference and its re-review clause, marked advisory.
- `routing.md` "choose by role and capability" list now includes the review/re-review host-difference bullet.
- `protocol.md` Handoff contract now states that each executed round records the host and model it actually used, distinct from the recommended host, as a single current-snapshot note.
- No `SKILL.md` change; its line count is unchanged.

## Review

Verdict: APPROVE

Owner-accepted in a Claude Code (Opus) cockpit round. A formal independent cross-vendor review was waived by the owner — an explicit exercise of the advisory override this task's own rule permits ("the human chooses the host and MAY override it").

## Blockers

None.

## Next Action

None - task completed.

## Completion Notice

Completed: the canonical protocol now states, advisorily, that an independent review (and any re-review after fixes) SHOULD run in a host - ideally a different vendor - than the one that produced or last modified the work (`protocol.md` Review, `routing.md`), and that each executed round records the host and model it actually used, distinct from the recommended host, as a current-snapshot note rather than a per-round log (`protocol.md` Handoff contract). `SKILL.md` unchanged. Owner-accepted (independent review waived per the rule's own advisory override); no follow-up work is identified for this task.
