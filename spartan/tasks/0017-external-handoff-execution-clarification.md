---
protocol: "0.3.1"
id: external-handoff-execution-clarification
created_at: 2026-07-24
status: completed
phase: complete
task_type: implementation
risk: material
current_role: independent-reviewer
next_role: human-operator
updated_at: 2026-07-24
---

# Acknowledge external handoff execution without weakening the hard boundary

## Objective

`agent-skill/references/protocol.md` contains, in its Handoff contract section, one new normative sentence stating that a handoff MAY be executed by an external, human-installed tool, that such a tool is outside this protocol, and that the protocol MUST NOT depend on it.

## Context

The owner is building an optional runtime layer, `agent-spartan-runner` (a separate repository), that reads a task's two-block handoff and starts the recommended next round without manual copy-paste - for example by invoking Codex from Claude Code through the `codex-plugin-cc` plugin, passing the model and effort already named in the advisory block. This raised the question of whether the protocol's hard boundary ("do not invoke another model on Spartan's behalf") should be revised to permit such execution.

The decision, reached in a Claude Code cockpit round on 2026-07-24, is to keep the boundary intact and add only a one-sentence acknowledgment. The full justification:

1. **Revising the boundary buys nothing.** Every desired capability (auto-starting a review round, passing model and effort, skipping copy-paste) is fully achievable in an external runner layer that consumes the handoff the protocol already produces. Moving that capability into the skill adds no function; it only relocates it into the wrong package.
2. **Revising the boundary costs portability, which is the product.** The boundary is what makes the skill identical and safe in every host. If `SKILL.md` said "may invoke another model when configured", each host would need host-specific invocation semantics (`codex-plugin-cc` exists only in Claude Code), and protocol behavior would diverge per host - the disease the protocol exists to cure.
3. **The boundary is what makes an external runner auditable.** With "skill produces the handoff / runner decides to execute it", exactly one layer holds execution authority. If both layers could execute, no clear line would show which piece started what.
4. **Precedent already points this way.** The protocol already states "Native host capabilities MAY be recommended as optional human choices. Spartan MUST NOT invoke, monitor, resume, normalize, or depend on them." The new sentence extends the same stance to external tools without granting the skill any execution ability.
5. **Board task 0007** (agent-spartan-protocol-board, `spartan/tasks/0007-evaluate-t3code-agent-orchestrator.md`) already decided not to build a runner inside the protocol and recorded the reconsideration condition "repeated manual handoffs become a measured operational burden". That burden materialized (per-round copy-paste between desktop hosts), and the response is a runner **outside** the protocol plus this clarification **inside** it - not a boundary revision.
6. **Scope admission test:** the sentence is Markdown-expressible, changes nothing about foreground execution, keeps the human initiating every transfer (the runner is human-installed and human-invoked), adds no durable protocol state, needs no daemon, and claims no enforcement. **Deletion test:** remove the skill and the runner, and the task files remain understandable and manually continuable; remove only the runner and the protocol works exactly as before, because it never depended on the runner.

## Scope

- Add exactly one sentence to the Handoff contract section of `agent-skill/references/protocol.md`, immediately after the clause "Spartan MUST NOT transmit or execute the handoff. The human chooses the next host and starts the next round.":

  > A handoff MAY be executed by an external, human-installed tool; such a tool is outside this protocol and the protocol MUST NOT depend on it.

## Out of Scope

- Building or documenting the runner itself (it lives in the separate `agent-spartan-runner` repository).
- Any runner configuration schema, auto-chain rules, or `spartan-runner/config.yaml` conventions.
- Any change to `SKILL.md`, `routing.md`, or `README.md` (precedent: task 0005 kept `SKILL.md` lean for detail that lives in the references). The owner explicitly authorized normalizing the identified pre-existing em dash in `assets/spartan-readme.md` within this task.
- The cross-checkout continuity work (absent-task guard, standing branch authorization, ephemeral locator), which is a separate candidate task.
- Any relaxation of the hard boundary or of the explicit-authorization rule for commit, push, PR, or merge.

## Constraints

- All persisted content remains English and ASCII-only.
- The added sentence is inserted verbatim; no other normative text changes.
- No new controlled values, roles, fields, or enforcement.

## Acceptance Criteria

- [x] The sentence appears verbatim in the Handoff contract section of `protocol.md`, adjacent to the "MUST NOT transmit or execute" clause.
- [x] No file under `agent-skill/` changes beyond the specified `protocol.md` sentence and the owner-authorized ASCII normalization; `SKILL.md` line count is unchanged.
- [x] `agent-skill/` remains ASCII-only after the authorized normalization and the available skill validator (if present) passes.
- [x] A fresh-context review in a host other than the implementing one issues an explicit verdict.

## Decisions

- Keep the hard boundary intact; acknowledge the external-runner ecosystem with one MAY/MUST NOT sentence instead of revising the boundary (full rationale in Context).
- Place the sentence in `protocol.md` only; `SKILL.md` stays lean per task 0005 precedent.
- The runner layer lives in a separate repository and is never referenced normatively by the protocol; the protocol acknowledges the *category* of tool, not any specific product.
- Owner decision (2026-07-24): authorize a one-character scope expansion normalizing the pre-existing em dash in `agent-skill/assets/spartan-readme.md` to an ASCII hyphen, rather than waiving the ASCII-only criterion. Rationale: a waiver would permanently break the ASCII-only invariant established by task 0006 and make every future ASCII check ambiguous; the fix is one byte with no semantic change. This resolves the acceptance-criterion conflict without weakening the protocol boundary.

## Work Completed

- Task authored in a Claude Code cockpit round (Claude Fable 5) after a multi-round advisory investigation of T3 Code, Agent Orchestrator, Codex worktrees, and `codex-plugin-cc`; no protocol file had been modified at that point.
- Implementer round (Codex, GPT-5.6 Terra) inserted the Scope sentence verbatim immediately after the specified Handoff contract anchor in `agent-skill/references/protocol.md`. No other `agent-skill/` file changed.
- Human-operator round (Codex, GPT-5.6 Luna) recorded the owner's authorization and normalized the identified pre-existing em dash in `agent-skill/assets/spartan-readme.md` to ASCII within this task's scope.
- Cockpit verification round (Claude Code, Claude Fable 5) independently re-ran the post-change checks (sentence present at the anchor, full ASCII scan clean, `git diff --check`, `SKILL.md` unchanged, `claude plugin validate --strict` on both manifests) and consolidated the duplicated owner-decision entry produced by two concurrent rounds.
- Independent-review round (Codex, fresh context) rechecked the exact sentence and its placement, the hard-boundary and native-capability clauses, the complete `agent-skill/` diff, the authorized baseline em dash, ASCII cleanliness, `SKILL.md` stability, diff hygiene, and both available strict validators. The clarification preserves Spartan's non-execution boundary; the only extra asset change is the recorded, owner-authorized ASCII normalization.

## Evidence

- `agent-skill/references/protocol.md` line 155: "Spartan MUST NOT transmit or execute the handoff. The human chooses the next host and starts the next round." - the anchor for the insertion.
- `agent-skill/references/protocol.md` line 200: "Native host capabilities MAY be recommended as optional human choices..." - the existing precedent this sentence extends.
- Board repository `spartan/tasks/0007-evaluate-t3code-agent-orchestrator.md`: decision to keep Spartan manual, with automation reconsidered only under measured operational burden.
- https://github.com/openai/codex-plugin-cc README (fetched 2026-07-24): `/codex:rescue --model <model> --effort <level>` and `.codex/config.toml` (`model`, `model_reasoning_effort`) confirm an external tool can honor the handoff's concrete model and effort - so the handoff contract already carries everything a runner needs.
- Scoped post-change checks: exact-sentence and anchor-order scan passed; `git diff --name-only -- agent-skill` reported only `agent-skill/references/protocol.md`; `git diff --exit-code -- agent-skill/SKILL.md` passed; `git diff --check` passed; `claude plugin validate --strict agent-skill` and `claude plugin validate --strict .` both passed.
- Pre-change baseline: `LC_ALL=C rg --pcre2 -n '[^\x00-\x7F]' agent-skill` found only the pre-existing em dash at `agent-skill/assets/spartan-readme.md:1`; the matching `git show HEAD:` scan confirmed it predates this task.
- Post-decision checks: the exact sentence scan passed; the full ASCII scan passed with no matches; `git diff --exit-code -- agent-skill/SKILL.md` passed; `git diff --check` passed; and `claude plugin validate --strict agent-skill` passed.
- Independent-review checks (2026-07-24): exact sentence and both boundary anchors found at `protocol.md:155`, `:157`, and `:202`; `LC_ALL=C rg --pcre2 -n '[^\\x00-\\x7F]' agent-skill` returned no matches; `git diff --check` and the unchanged-`SKILL.md` comparison passed; the `agent-skill/` diff names only `references/protocol.md` and the authorized `assets/spartan-readme.md`; and `claude plugin validate --strict agent-skill` plus `claude plugin validate --strict .` both passed.

## Review

Verdict: APPROVE

Findings:

- The sentence is verbatim, immediately follows the required non-transmission/non-execution clause, and preserves that clause's assignment of execution authority away from Spartan. Its external-tool acknowledgement explicitly places the tool outside the protocol and forbids protocol dependency.
- The diff is limited to the specified protocol sentence and the separately documented, owner-authorized one-character ASCII normalization. The baseline scan confirms the normalized em dash predated this task; the post-change `agent-skill/` scan is ASCII-clean.
- No controlled values, roles, fields, runtime behavior, or external-action authority were added. The recorded evidence is sufficient and all relevant available checks pass.
- No findings require changes.

## Blockers

- None. The owner-authorized normalization resolves the ASCII-only criterion without changing the protocol boundary.

## Next Action

None. Review returned `APPROVE`, every acceptance criterion and relevant check is satisfied, and no blocker remains.

## Completion Notice

Completed: the external-handoff clarification and owner-authorized ASCII normalization are independently APPROVED. The protocol sentence is verbatim and adjacent to the hard boundary, keeps the external tool outside the protocol, and does not add a dependency or execution authority to Spartan. Scope, authorization, ASCII, diff hygiene, and strict manifest validation all satisfy the task. No follow-up work is identifiable from this task.
