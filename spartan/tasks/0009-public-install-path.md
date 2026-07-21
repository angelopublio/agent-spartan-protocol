---
protocol: "0.2.0"
id: public-install-path
created_at: 2026-07-21
status: complete
phase: complete
task_type: implementation
risk: material
current_role: human-operator
next_role: none
updated_at: 2026-07-21
---

# Make the public install path work

## Objective

A stranger can adopt Spartan in under two minutes: install via the Claude Code plugin marketplace or via `npx skills add` for Codex and other agents, guided by a Quick start section at the top of the README.

## Context

- v0.2.0 is released (tag + GitHub Release); `agent-skill/.claude-plugin/plugin.json` existed and was valid before this task.
- Schema facts verified against code.claude.com docs (2026-07-21): the marketplace catalog lives at `.claude-plugin/marketplace.json` in the repository root; required fields are `name`, `owner.name`, and `plugins[]` with `name` + `source`; a relative `source` starting with `./` resolves against the marketplace root. Since Claude Code v2.1.142, a plugin with `SKILL.md` at its root and no `skills/` directory is auto-loaded as a single-skill plugin, using the frontmatter `name` for invocation — this matches `agent-skill/` exactly, so no plugin restructure was needed.
- The skills CLI (`npx skills add`, vercel-labs/skills) requires only `SKILL.md` with `name` and `description` frontmatter; it clones the repo and scans root, `skills/`, `.claude/skills/`, and `.agents/skills/`. The repository's committed symlinks satisfy discovery.

## Scope

- `.claude-plugin/marketplace.json` at the repository root listing plugin `spartan` with source `./agent-skill`.
- `agent-skill/.claude-plugin/plugin.json` reconciliation (author added to clear the validator warning).
- README rewrite: Quick start first, symlink instructions moved to a "Development / fallback install" section with generic paths.
- Public-repo sensitivity scan of README, docs/, spartan/tasks/ (report only).

## Out of Scope

- Commit, push, tag, or release-config changes (separate operator round).
- Restructuring `agent-skill/` layout.
- Adding a `.codex-plugin/` manifest (investigated; not required — see Decisions).

## Constraints

- Manifests are static metadata only; no install-time scripts.
- Must pass the protocol's deletion/portability test: removing the manifests leaves the skill and task files fully usable.

## Acceptance Criteria

- [x] `.claude-plugin/marketplace.json` exists at repo root and passes `claude plugin validate .`.
- [x] `npx skills add <repo> --skill spartan` discovery confirmed against the hosted repository.
- [x] README opens with a Quick start (one install command per host plus invoke examples) before the philosophy sections; symlink instructions preserved lower down.
- [x] Sensitivity scan completed and findings reported without deletions.
- [x] Cross-vendor review of the diff records an APPROVE verdict.
- [x] Post-push: `/plugin marketplace add angelopublio/agent-spartan-protocol` then `/plugin install spartan@agent-spartan-protocol` verified in a real Claude Code session (requires the operator commit/push round first).

## Decisions

- Marketplace name is `agent-spartan-protocol` (matches the repo; the generic name `spartan` risks collision since users can register only one marketplace per name). Install syntax: `/plugin install spartan@agent-spartan-protocol`.
- No `.codex-plugin/` manifest was added. The codex-build pattern was investigated: its `.codex-plugin/plugin.json` is not consumed by the skills CLI, which needs only `SKILL.md`. An empirical `npx skills add ... --list` against the hosted repo already found the `spartan` skill, so extra metadata would be dead weight and fail the minimalism test.
- No `version` in the marketplace plugin entry: `plugin.json` carries `0.2.0` and is bumped by release-please; duplicating it would drift.
- Omitted `$schema` from marketplace.json: no schemastore schema exists for the marketplace catalog (404), and Claude Code ignores the field.
- README symlink instructions now use `SPARTAN_REPO="$(pwd)"` from the repo root instead of the owner's absolute local path.
- Repository visibility changed from private to public (`gh repo edit ... --visibility public --accept-visibility-change-consequences`) on explicit operator authorization, given in chat, specifically so the "a stranger can adopt Spartan" objective could be verified for real rather than only for the owner's own authenticated access. This was a deliberate, human-approved, hard-to-reverse action, not an automated or default step.

## Work Completed

- Round executed in Claude Code on Claude Fable 5 (2026-07-21): created `.claude-plugin/marketplace.json`; added `author` to `agent-skill/.claude-plugin/plugin.json`; rewrote `README.md` (Quick start first, Codex/skills-CLI section, symlinks demoted to Development / fallback install, Current status updated to 0.2.0); ran the sensitivity scan.
- Cross-vendor review executed in Codex on GPT-5 (2026-07-21): inspected only the requested uncommitted manifests and README, reproduced the relevant checks, and left the implementation files unchanged. Verdict: CHANGES (README misattributed the `v0.2.0` release to CI).
- Correction round executed in Claude Code on Claude Sonnet 5 (2026-07-21): reworded the "Current status" paragraph in `README.md` to state the release was tagged/published without attributing it to CI, and to note the automated release-please flow is configured and unblocked but not yet exercised end-to-end — consistent with task 0008's recorded manual-fallback release. No other file in the diff was touched.
- Independent review executed in Codex on GPT-5 (2026-07-21): confirmed the corrected wording against task 0008, confirmed the marketplace and plugin manifests remain the previously reviewed static metadata, and left implementation files unchanged. Verdict: APPROVE.

## Evidence

- `claude plugin validate .`: "Validation passed" (after adding plugin author; before that, one attribution warning).
- `npx -y skills@latest add https://github.com/angelopublio/agent-spartan-protocol --list`: "Found 1 skill — spartan" with the correct description, confirming Codex-path discovery needs no new metadata.
- `python3 -m json.tool` on both manifests: valid JSON.
- Sensitivity scan (`grep -rniE` for keys/tokens/passwords, emails, `/Users/` paths across README.md, docs/, spartan/, RELEASING.md, AGENTS.md, agent-skill/): no credentials or personal emails. Findings: (a) the old README hard-coded the owner's local absolute path — removed by the rewrite; (b) `spartan/tasks/0004-...md` retains that path inside historical review evidence — harmless, left untouched pending owner preference; (c) the repository has no LICENSE file, which hinders public adoption — owner decision, not changed.
- Reviewer checks: `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill` both passed; both manifests passed `python3 -m json.tool`; `git diff --check` passed; both manifests are non-executable static JSON; and the skill instructions and task artifacts remain independently readable without the manifests.
- Reviewer discovery check: `npx -y skills@latest add angelopublio/agent-spartan-protocol --skill spartan --list` resolved the README's exact repository shorthand and found exactly one skill, `spartan`.
- Reviewer sensitivity scan reproduced no credential or personal-email finding; only the already-recorded historical `/Users/...` evidence and a public `git@github.com` remote appeared.
- Correction round: `claude plugin validate --strict .`: "Validation passed"; `python3 -m json.tool` on both manifests: valid; `git diff --check`: pass (no whitespace errors).
- Independent-reviewer checks: `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill` passed; both manifests passed JSON parsing; `git diff --check` passed; the focused wording check found no `v0.2.0`/repository-CI misattribution and found task 0008's manual-fallback record; current status shows no new manifest or runtime-file changes.

## Review

Verdict: APPROVE

Findings:

- None. The corrected `README.md` wording says `v0.2.0` was tagged and published as a GitHub Release, while describing repository CI only as the future automated flow; this matches task 0008's manual-fallback record. The previously reviewed marketplace and plugin manifests remain unchanged and valid static metadata.

## Blockers

None.

## Post-push verification (2026-07-21)

- Human-operator round executed in Claude Code on Claude Sonnet 5.
- Committed the approved diff (`.claude-plugin/marketplace.json`, `agent-skill/.claude-plugin/plugin.json`, `README.md`, `agent-skill/references/routing.md`, this task file) as `74b4ec3` after re-running checks: `python3 -m json.tool` on both manifests (valid), `git diff --check` (pass), `claude plugin validate .` and `claude plugin validate --strict agent-skill` (both "Validation passed").
- User confirmed the push in chat; pushed `74b4ec3` to `origin/main` (`738a00e..74b4ec3 main -> main`).
- First pass (flagged as invalid by the operator, correctly): ran `claude plugin marketplace add angelopublio/agent-spartan-protocol` and `claude plugin install spartan@agent-spartan-protocol` while the repository was still **private**. Both commands reported success and `claude plugin list` showed the plugin enabled, but this only proved the operator's own SSH-authenticated access worked — not that a stranger could install it. The operator caught this gap (`gh repo view ... --json visibility` confirmed `PRIVATE`) before the task was accepted as complete.
- Operator explicitly authorized making the repository public to allow a real stranger-install test: `gh repo edit angelopublio/agent-spartan-protocol --visibility public --accept-visibility-change-consequences` succeeded; `gh repo view ... --json visibility` then returned `PUBLIC`.
- Removed the prior marketplace/plugin state (`claude plugin marketplace remove agent-spartan-protocol`, `claude plugin uninstall spartan@agent-spartan-protocol --scope user`) to retest cleanly.
- Verified anonymous access with a bare `git clone` outside the user's normal shell (`env -i PATH=/usr/bin:/bin`, no SSH agent) against the HTTPS URL: succeeded, confirming the repo content is fetchable without credentials.
- Re-ran the install with `GIT_SSH_COMMAND="ssh -o BatchMode=yes -o IdentitiesOnly=yes -i /dev/null"` to force any SSH attempt to fail with no usable key, simulating a stranger with no SSH key registered on this repo:
  - `claude plugin marketplace add angelopublio/agent-spartan-protocol`: logged `Cloning via SSH: git@github.com:...` then `SSH clone failed, retrying with HTTPS: https://github.com/...`, then `✔ Successfully added marketplace: agent-spartan-protocol (declared in user settings)`.
  - `claude plugin install spartan@agent-spartan-protocol`: `✔ Successfully installed plugin: spartan@agent-spartan-protocol (scope: user)`.
  - `claude plugin list`: shows `spartan@agent-spartan-protocol`, version 0.2.0, scope user, status "✔ enabled" under "Installed plugins" — this time under conditions that reproduce what a real stranger without SSH access would experience (automatic HTTPS fallback on a public repo).
- Note (not a defect): `claude plugin list` also flags the operator's pre-existing personal symlink install at `~/.claude/skills/spartan` (the Development/fallback path from this same README, installed earlier on this machine) as shadowed by the plugin, since both use the name `spartan`. This is expected precedence behavior between the two install paths on one machine, not a hosted-install failure; the operator's own next step, if any, is to remove or rename the personal symlink to avoid the shadow warning locally.
- **Correction recorded**: repository visibility (public vs. private) was not checked before the first "verified" claim in this round. The user (in Portuguese) challenged the result — "tem certeza que ele instalou usando o plugin?" given the repo wasn't public and a personal symlink already existed — which was the correct catch. The retest above addresses both concerns: it confirms the marketplace clone is a distinct, real clone (not the symlink) and that installation succeeds without the operator's own SSH credentials.

## Outcome

All acceptance criteria met. The public install path works end to end for Claude Code (marketplace + plugin install) and was previously confirmed for Codex/skills CLI. No further round is required for this task.

## Next Action

None. Task complete.

## Next Handoff

None — no work remains on this task.
