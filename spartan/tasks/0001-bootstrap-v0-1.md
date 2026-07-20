---
protocol_version: "0.1"
id: bootstrap-v0-1
created_at: 2026-07-19
status: completed
phase: complete
task_type: review
risk: material
current_role: independent-reviewer
next_role: human-operator
updated_at: 2026-07-19
---

# Bootstrap Spartan protocol v0.1

## Objective

Establish and independently validate the first portable, English-only Spartan skill and handoff contract without introducing runtime orchestration.

## Context

The repository began empty. The v0.1 design translates the agreed principle that Spartan coordinates information rather than processes into a static skill package, normative references, a task template, and this first continuation artifact.

## Scope

- Define the project and its non-goals.
- Define the portable protocol, routing guidance, task schema, and handoff contract.
- Create a minimal installable `spartan` skill using only Markdown plus optional Codex interface metadata.
- Verify the package in a fresh Codex context.
- Request a manually initiated, read-only independent review in Claude Code after Codex verification.

## Out of Scope

- Initializing or publishing a Git repository.
- Installing the skill globally.
- Creating executable adapters or host-specific workflows.
- Daemons, nested LLM execution, authentication, scheduling, retries, locks, queues, ledgers, patch adoption, or other runtime behavior.
- Modifying the existing Agent Spartans Workbench repository.

## Constraints

- All persisted documentation, configuration descriptions, task content, verdicts, and handoffs are English.
- The human initiates every new host session and every cross-vendor transfer.
- The task Markdown is the only durable protocol state.
- Review rounds remain read-only unless the human explicitly authorizes changes.
- Subscription sessions are used directly; no API credentials or usage-based fallback belongs in the protocol.

## Acceptance Criteria

- [x] The skill folder passes the standard static skill validator.
- [x] All repository text artifacts are English and contain no initializer placeholders.
- [x] A fresh Codex verifier can evaluate the package from this file without prior conversation history.
- [x] The package contains no runtime code, dependencies, background behavior, or nested-agent instructions.
- [x] The protocol produces one concise English handoff for an incomplete round.
- [x] A manually started Claude Code independent reviewer issues an explicit verdict on host neutrality and boundary compliance.

## Decisions

- Keep the project wrapper separate from the installable `agent-skill/` folder.
- Use one current-snapshot Markdown artifact per task rather than event sourcing or full history.
- Keep routing advisory and role-based; the human chooses the actual host.
- Permit foreground repository work by the current authenticated host but prohibit Spartan-managed nested execution.
- Treat native host automation as an optional human choice outside the portable protocol.

## Work Completed

- Created the project instructions and overview.
- Created the portable skill entry point, protocol reference, routing guide, and task template.
- Added project-local Codex and Claude Code discovery links pointing to the same canonical skill folder.
- Documented the cold-context and cross-vendor dogfooding sequence.
- Created this first task artifact and handoff.
- Completed the human-started, top-level Codex verification round without modifying the protocol package.
- Completed the manually started, read-only Claude Code independent review of host neutrality and boundary compliance without modifying the protocol package.

## Evidence

- Cold-context continuation: this human-started Codex task resolved the objective, constraints, criteria, role, and next action from this artifact and repository files; no prior conversation or nested agent was required.
- Static validation: `quick_validate.py` returned `Skill is valid!` with exit code 0 for the skill source and both discovery links.
- Artifact inspection: `find . -type f` found eight Markdown files and optional `agent-skill/agents/openai.yaml`; manual inspection found English throughout. The Portuguese-term scan had no matches, and non-ASCII matches were only README tree glyphs.
- Placeholder scan: outside the intentional task template, matches were limited to documented protocol metavariables such as `<slug>` and `<next-role>`; no unresolved initializer marker was present.
- State and runtime scan: the task namespace contained only this Markdown task; no executable file, runtime source extension, package manifest, dependency file, JSON/JSONL, database, queue, ledger, or event-state artifact was found.
- Boundary review: every nested execution, retry, monitoring, scheduler, credential, and automatic external-action reference in `agent-skill/` is a prohibition or scope limit. Foreground work and routing remain advisory and human initiated.
- Portability review: vendor names are examples or optional interface/discovery metadata; normative instructions require no vendor CLI, API, credential, automation, or hidden state. Both discovery links resolve to the same canonical skill folder.
- Handoff and size checks: the incomplete task contains one concise English handoff, and `agent-skill/SKILL.md` is 68 lines, below the preferred 200-line limit.
- Claude Code independent review (2026-07-19, read-only): cold-context continuation succeeded from this artifact alone; the `.claude/skills/spartan` symlink resolved and loaded the canonical skill natively as `/spartan`, confirming discovery neutrality in a second vendor. Re-ran static scans: non-ASCII grep matched only README tree glyphs; placeholder grep (`TODO|FIXME|TBD|XXX|CHANGEME`) had no matches; `find` confirmed no executable, script, manifest, JSON/JSONL, or state file outside `.idea/` editor metadata; `agent-skill/SKILL.md` is 68 lines. Vendor references in `agent-skill/` normative text are examples only; the sole Codex-specific content is optional `agent-skill/agents/openai.yaml` interface metadata, correctly isolated and documented as optional.

## Review

Verdict: APPROVE

Findings:

- Fresh Codex verification found no defect in the acceptance criteria, host neutrality, durable-state boundary, or execution boundary.
- Claude Code independent review (independent-reviewer, read-only): APPROVE. No Codex-only assumption exists in normative protocol text; vendor names appear only as examples, in optional isolated interface metadata (`agent-skill/agents/openai.yaml`), or in dogfooding narrative that correctly describes vendor-specific rounds. No boundary leak toward runtime behavior was found: every reference to nested execution, retry, monitoring, scheduling, credentials, or automatic external action in `agent-skill/` and `AGENTS.md` is a prohibition or scope limit. Routing and completion rules are unambiguous, use closed vocabularies, and this artifact required no hidden conversation context to continue.
- Minor non-blocking observation: the repository ships no validator of its own, so "run the relevant repository checks" resolves to static grep/find scans; the `quick_validate.py` cited in earlier evidence is an external tool. Acceptable for a documentation-only package.

## Blockers

None.

## Next Action

None. All acceptance criteria are satisfied, both reviews are APPROVE, and the task is completed. The recommended follow-up (a separate future task) is the first real-project trial sequence in `docs/DOGFOODING.md`.

## Next Handoff

None. Bootstrap v0.1 is complete; open a new task from the template for the first real-project trial.
