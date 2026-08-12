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

The default mapping covers two hosts because alternating producer and reviewer across their vendors is the protocol's built-in independence mechanism. A host beyond those two enters a round only when the target repository's own instructions name it for that role or when the human explicitly chooses it for the current round; the round records which admission path applied. When an admitted host takes implementation, producer and reviewer separate without the two-host alternation: one host plans, the admitted host implements, and the third reviews or verifies both.

Independence is a property of model vendors, not of host names. Codex and Claude Code each fix their vendor by the host, but Cursor runs models from several vendors, so its vendor is set by the model it ran. Compare the model vendor of the producing round with the model vendor of the reviewing round: they are independent when the two vendors differ and correlated when they match, whatever the hosts are called. Composer is Cursor's own model, so it is a third vendor beside Anthropic and OpenAI; a Cursor round driving a Claude model correlates with Claude Code rounds while staying independent of Codex rounds, and a GPT model inverts that. Every round MUST record the model it actually ran, which is what lets a later reviewer make this comparison for any model, including one from a vendor not named here.

The mapping is a default, not an exclusive assignment. Any role may run on either host when the preferred host cannot complete the round, for example:

- the preferred host declines part of the work under its own safety or usage policy;
- the round needs an authentication or credential ceremony the preferred host does not handle, in which case the ceremony itself belongs to `human-operator`;
- the preferred host lacks a required capability or is unavailable.

In that case the current round records what was refused or missing and why, and the handoff recommends the alternative host (for example, Codex taking a planning round) or `human-operator`. Rerouting redistributes the work; it never broadens what the human authorized.

### Current model reference

Use this table to fill the "Model and effort" line with a concrete name. It ages; verify against the human's plan and update this section when vendors ship new models. The Codex and Claude Code rows were last verified in July 2026; the Cursor row was last verified in August 2026. Date each row's own last check rather than restating one date for the table, so adding a row never backdates it.

When the chosen model exposes selectable effort, the round names one level matched to the assessed risk and actually selectable on the human's plan. When the model exposes none, the "Model and effort" line states that instead of naming a level, because a level the host cannot accept is as unusable to the human as the generic phrase the contract already forbids. Assessed risk then governs the host and role choice: a `routine` round may stay in the effort-less model, while `material` or `high-impact` reasoning is a reason to route to a model that exposes effort. A speed or cost toggle is never turned into an effort recommendation. By the owner's August 2026 observation of the product UI, Composer 2.5 exposes no effort selector, so a Cursor round records `Composer 2.5, no user-selectable effort`.

| Host | Model | Recommended use in Spartan rounds |
|---|---|---|
| Codex | GPT-5.6 Sol | Hard reasoning: architecture review, high-impact plans; only tier with max effort and ultra mode |
| Codex | GPT-5.6 Terra | Balanced default for implementation, testing, and most reviews |
| Codex | GPT-5.6 Luna | Fast and cheap: routine, narrow rounds |
| Codex | GPT-5.3-Codex | Agentic multi-step coding rounds |
| Codex | GPT-5.3-Codex-Spark | Real-time quick edits and pair programming (research preview, ChatGPT Pro) |
| Claude Code | Opus | Default for cockpit, planning, and code review at high effort |
| Claude Code | Fable 5 | Reserve for `high-impact` risk or unusually complex/sophisticated reasoning; do not default to it over Opus |
| Claude Code | Sonnet / Haiku | `routine` risk or high-volume rounds |
| Cursor | Composer 2.5 | Implementation and testing rounds; Cursor's own model, so a third vendor beside Anthropic and OpenAI. No user-selectable effort by the owner's observation of the product UI (verified August 2026) |

This table lists the package's default model recommendations for hosts admitted to a round, not everything a host can run. When a repository's instructions declare a specific model, the round honors it under the same admission rule used for a non-default host and names an effort level if that model exposes one. The round also records the model's vendor attribution and the path it ran through. The path matters because the same model name can carry a different vendor attribution inside a host than it does directly, so an independence comparison uses the round's recorded attribution and path, never the host name.

Grok 4.5 is declaration-only reference data, not a recommended table row. It exposes low, medium, and high effort, with high as the documented default; a `routine` round names medium, while a `material` or `high-impact` round names high. Effort switching and Fast mode require a Pro or higher plan, while the Start plan fixes Grok 4.5 at medium effort in non-fast mode, so the named level must be selectable on the human's plan. Grok 4.5 is documented as a joint model from Cursor and SpaceXAI, so its vendor attribution is joint rather than a single vendor (verified August 2026).

Beyond the default mapping, choose by role and capability, not brand loyalty:

- Match model tier to the task's assessed risk, not to the host's most capable model: `routine` defaults to Sonnet/Haiku (or the Codex Luna/Terra equivalent) even inside a round that also does cockpit or review work.
- Continue in the current host when the next action is routine and no independent judgment is required.
- Recommend a fresh session when prior reasoning could bias review.
- Recommend another vendor when correlated assumptions are a material risk.
- For a review or re-review, recommend a different host - ideally a different vendor - than the one that produced or last modified the work under review; self-review correlates blind spots. The human MAY override.
- Recommend a native host feature only as a human choice. Do not invoke, monitor, resume, or depend on it.

The handoff is delivered as two blocks: a "Recommended execution" block stating the recommended host with a one-phrase reason, one concrete model and effort level, and the invocation style matched to what that host supports (`$spartan` for Codex, `/spartan` for Cursor, `/spartan` for Claude Code, the skill named in the prompt for a host that discovers skills from `SKILL.md` but exposes no invocation token, or a direct prompt for a host without skill support); then a clean prompt block naming the next role and action. The human decides the actual host, model, and invocation, and starts the round manually.
