# Spartan Routing Guide

Routing is advisory. It selects the responsibility for the current and next human-started rounds; it does not invoke or supervise a host.

## Classify the task

Choose the task type that best describes the current round:

- `investigation`: gather evidence without changing implementation.
- `planning`: resolve scope, approach, constraints, and acceptance criteria.
- `implementation`: modify the target repository and verify the change.
- `review`: assess an existing plan or diff and issue an explicit verdict.
- `verification`: confirm acceptance criteria and evidence without broadening scope.

## Assess risk

- `routine`: narrow, reversible, familiar, and covered by existing checks.
- `material`: meaningful behavior or multiple files, but with bounded impact and a clear rollback.
- `high-impact`: architecture, security, authentication, authorization, data migration, infrastructure, destructive operations, broad refactors, or hard-to-reverse decisions.

When uncertain between two levels, choose the higher level and record why. Risk is a routing aid, not a permission grant.

## Assign roles

- `human-operator`: provides a missing decision, authorization, credential ceremony, or external coordination.
- `investigator`: gathers and evaluates evidence without changing implementation.
- `planner`: defines or challenges the approach and acceptance criteria.
- `implementer`: makes the authorized change and runs relevant checks.
- `reviewer`: evaluates work with an explicit verdict.
- `independent-reviewer`: reviews from fresh context, preferably in another vendor when independence is materially valuable.
- `verifier`: confirms acceptance criteria and evidence without redesigning the work.

## Recommended routes

| Situation | Recommended route |
|---|---|
| Small, reversible, well-specified change | Implementer verifies and may complete in one round |
| Material implementation | Implementer, then fresh-context reviewer or verifier |
| Architecture or broad-impact decision | Planner, implementer, independent-reviewer, verifier as needed |
| Security, authentication, authorization, or migration | Planning plus independent review; cross-vendor review is preferred when available |
| Read-only investigation | Investigator in the current host; add a second review only for material uncertainty |
| Missing human decision or authority | `blocked` with `human-operator` as next role |

Do not require cross-vendor review for every task. Coordination cost must be justified by risk, uncertainty, or the value of independent judgment.

## Choose the next host

The skill recommends the host; the human keeps the final choice. Default mapping by role:

| Host | Default responsibilities |
|---|---|
| Claude Code | Cockpit (task authoring and coordination with the human), planning, code review |
| Codex | Plan review, implementation (coding), testing, browser and end-to-end verification (e.g. Playwright) |

This mapping deliberately crosses vendors between producing and reviewing: Claude plans and Codex reviews the plan; Codex implements and Claude reviews the code. Deviate when capability, availability, or task context justifies it, and record the reason in the task file.

The mapping is a default, not an exclusive assignment. Any role may run on either host when the preferred host cannot complete the round, for example:

- the preferred host declines part of the work under its own safety or usage policy;
- the round needs an authentication or credential ceremony the preferred host does not handle, in which case the ceremony itself belongs to `human-operator`;
- the preferred host lacks a required capability or is unavailable.

In that case the current round records what was refused or missing and why, and the handoff recommends the alternative host (for example, Codex taking a planning round) or `human-operator`. Rerouting redistributes the work; it never broadens what the human authorized.

### Current model reference (verified July 2026)

Use this table to fill the "Model and effort" line with a concrete name. It ages; verify against the human's plan and update this section when vendors ship new models.

| Host | Model | Recommended use in Spartan rounds |
|---|---|---|
| Codex | GPT-5.6 Sol | Hard reasoning: architecture review, high-impact plans; only tier with max effort and ultra mode |
| Codex | GPT-5.6 Terra | Balanced default for implementation, testing, and most reviews |
| Codex | GPT-5.6 Luna | Fast and cheap: routine, narrow rounds |
| Codex | GPT-5.3-Codex | Agentic multi-step coding rounds |
| Codex | GPT-5.3-Codex-Spark | Real-time quick edits and pair programming (research preview, ChatGPT Pro) |
| Claude Code | Fable 5 / Opus | Cockpit, planning, and code review at high effort |
| Claude Code | Sonnet / Haiku | Routine or high-volume rounds |

Beyond the default mapping, choose by role and capability, not brand loyalty:

- Continue in the current host when the next action is routine and no independent judgment is required.
- Recommend a fresh session when prior reasoning could bias review.
- Recommend another vendor when correlated assumptions are a material risk.
- For a review or re-review, recommend a different host - ideally a different vendor - than the one that produced or last modified the work under review; self-review correlates blind spots. The human MAY override.
- Recommend a native host feature only as a human choice. Do not invoke, monitor, resume, or depend on it.

The handoff is delivered as two blocks: a "Recommended execution" block stating the recommended host with a one-phrase reason, one concrete model and effort level, and the invocation style (`$spartan`, `/spartan`, or direct prompt); then a clean prompt block naming the next role and action. The human decides the actual host, model, and invocation, and starts the round manually.
