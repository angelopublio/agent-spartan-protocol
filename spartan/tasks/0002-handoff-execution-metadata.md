---
protocol_version: "0.1"
id: handoff-execution-metadata
created_at: 2026-07-20
status: completed
phase: complete
task_type: review
risk: material
current_role: reviewer
next_role: human-operator
updated_at: 2026-07-20
---

# Add recommended-execution metadata to the handoff contract

## Objective

Every handoff must tell the human, explicitly, how to run the next round: which host, which model and effort, which invocation style, and the exact prompt to pass, while the human keeps the final choice.

## Context

During bootstrap dogfooding the human had to infer where and how to run each handoff. The owner requested that this guidance become a mandatory, advisory block in every handoff.

## Scope

- Extend the handoff contract in `agent-skill/references/protocol.md` with a required "Recommended execution" block.
- Update `agent-skill/SKILL.md`, `agent-skill/references/routing.md`, and `agent-skill/assets/task-template.md` to match.

## Out of Scope

- Any runtime, host invocation, or model routing behavior.
- Initializing Git or committing.

## Constraints

- Recommendations stay advisory; the human decides the actual host, model, and invocation.
- Model and effort guidance is best effort, since availability varies by vendor and plan.
- All persisted content remains English; no runtime code is added.

## Acceptance Criteria

- [x] The protocol handoff contract requires host, model and effort, invocation style, and the prompt to pass.
- [x] SKILL.md, routing.md, and the task template use the same updated handoff shape.
- [x] Static checks pass: English-only content, no placeholders outside the template, no runtime artifacts, SKILL.md under 200 lines.
- [x] A fresh-context review issues an explicit verdict on the change.

## Decisions

- The handoff is delivered as two separate blocks: an advisory Recommended execution block for the human, then a clean prompt block with no execution metadata that can be passed unchanged.
- The block is labeled "human decides" to preserve the advisory-routing invariant.
- The model and effort recommendation must be concrete (one named model, one named effort level, optional fallback phrase); generic wording such as "best available model" violates the contract.
- routing.md carries a dated "Current model reference" table (July 2026: GPT-5.6 Sol/Terra/Luna, GPT-5.3-Codex and Codex-Spark; Claude Fable/Opus/Sonnet/Haiku) so handoffs can name concrete models; the table must be updated as vendors ship new models.
- Owner-defined default host mapping recorded in routing.md: Claude Code handles cockpit, planning, and code review; Codex handles plan review, implementation, testing, and browser/end-to-end verification. The mapping intentionally crosses vendors between producing and reviewing.
- The mapping is a default, not exclusive: when the preferred host declines work under its own policy, cannot handle an authentication ceremony, or lacks a capability, the round records the reason and the handoff reroutes the role to the other host or to human-operator, without broadening authorization.

## Work Completed

- Added the "Recommended execution" requirements and shape to the handoff contract in protocol.md.
- Updated SKILL.md, routing.md, and the task template to the same shape.
- Added the default host-by-role mapping table to routing.md.
- Completed a read-only review of the four scoped implementation files; no implementation change was needed.
- Reconciled this task artifact's stale single-prompt wording with the accepted two-block design.

## Evidence

- `quick_validate.py` and validation through both `.agents` and `.claude` discovery links: `Skill is valid!` with exit code 0 in all three runs.
- Static scans: non-ASCII matches are limited to README tree glyphs; no unresolved initializer marker was found; no runtime source, executable, package manifest, dependency file, or state artifact exists outside editor metadata.
- `wc -l agent-skill/SKILL.md`: 75 lines, below the preferred 200-line limit.
- Cross-file handoff-shape scan: `agent-skill/SKILL.md`, `agent-skill/references/protocol.md`, `agent-skill/references/routing.md`, and `agent-skill/assets/task-template.md` consistently require an advisory Recommended execution block followed by a clean prompt block.
- Boundary scan: host selection is explicitly advisory and human-overridable; the human starts each round; invocation, monitoring, resumption, automatic routing, and external execution remain prohibited.
- Repository-state limitation: `git status --short` reports that this directory is not a Git repository, consistent with the recorded out-of-scope decision not to initialize Git. The review therefore evaluated the current scoped files rather than a baseline diff.

## Review

Verdict: APPROVE

Findings:

- Clarity: the protocol, skill, routing guide, and task template agree on the required host, concrete model and effort, invocation style, and exact prompt; separating human guidance from the clean prompt removes paste ambiguity.
- Advisory neutrality: "human decides," explicit override language, non-exclusive host defaults, availability fallback guidance, and manual initiation preserve human authority despite concrete recommendations.
- Boundary compliance: the change is static documentation and passes every scope-admission test; it adds no runtime, model invocation, supervision, retry, credential handling, quota routing, or automatic external action.
- No blocking defect was found. The only inconsistency was stale single-prompt wording in this task artifact, which was reconciled while recording the review; the scoped implementation remained read-only.

## Blockers

None.

## Next Action

None. All acceptance criteria are satisfied, relevant checks have recorded outcomes, the review verdict is APPROVE, and no blocker remains.

## Next Handoff

None. The recommended-execution handoff change is approved and this task is complete.
