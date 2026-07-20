---
protocol_version: "0.1"
id: protocol-restructure-naming-and-conventions
status: completed
phase: complete
task_type: implementation
risk: material
current_role: human-operator
next_role: human-operator
created_at: 2026-07-20
updated_at: 2026-07-20
---

# Restructure skill/task layout and adopt task-numbering conventions

## Objective

Apply the naming, layout, and convention decisions agreed with the owner so the Spartan package has a consistent, publicly legible structure with no singular/plural collision, and so task files carry a stable creation sequence.

## Context

A design conversation settled several structural decisions for the protocol. They exist only in ephemeral chat and must be captured here before implementation. The repository is currently not a Git repo, so changes are evaluated against working-tree state, not a diff.

This task overlaps the open `completion-notice-next-step` review (verdict CHANGES): that finding's fix (correcting a template path) touches the same files and is folded into this sweep. The owner may mark that task `completed` as superseded once this lands.

## Scope

- Rename the skill source directory `spartan/` to `agent-skill/`.
- Repoint the discovery symlinks `.agents/skills/spartan` and `.claude/skills/spartan` to `../../agent-skill`.
- Rename the plural durable task directory to the singular `spartan/`; add `spartan/README.md`; keep the `tasks/` subdirectory.
- Rename existing task files to the `NNNN-slug.md` form and update internal cross-references to them.
- Update `assets/task-template.md`: add `created_at`, document the `NNNN-slug.md` filename convention.
- Correct every template-path reference to `assets/task-template.md` (folds in the CHANGES finding from `completion-notice-next-step`).
- Update all path references repo-wide: `README.md`, `AGENTS.md`, `docs/DOGFOODING.md`, the skill instructions, their references, and task files (the former plural task namespace to `spartan/`; the former skill references to `agent-skill/references`; etc.).
- Add to `protocol.md` and `SKILL.md`: a completion notice SHOULD suggest a documentation round when the completed work changed durable behavior or decisions (not on every completion).
- Record a "when to create a skill" heuristic in `docs/`.

## Out of Scope

- Renaming the protocol brand; it stays "Spartan".
- Any orchestrator/dispatcher skill or commit/push skill.
- Runtime, host invocation, or model-routing behavior.
- Initializing Git or committing.

## Constraints

- All persisted content remains English.
- `SKILL.md` stays under 200 lines.
- No runtime, dependency, or state artifacts introduced.
- After the rename, both discovery links MUST resolve and expose a readable `SKILL.md`; the deletion test MUST still hold.
- Filename numbers reflect immutable creation order; dates live in frontmatter, not filenames.

## Acceptance Criteria

- [x] `agent-skill/` holds the skill; no `spartan/` source directory remains.
- [x] Both symlinks resolve to `agent-skill/` and `SKILL.md` is readable through each.
- [x] Durable folder is `spartan/` with `README.md` and `tasks/`; no plural task directory remains.
- [x] Existing tasks renamed to `NNNN-slug.md`; template documents the convention and includes `created_at`.
- [x] Every path reference updated; checks for the former plural namespace and former skill-reference path return no matches. (Review findings 1 and 2 applied: `README.md` install defaults now use `agent-spartan-protocol`; product name and `AGENTS.md` reconciled to singular. Awaits re-review.)
- [x] Template-path reference corrected to `assets/task-template.md` (CHANGES finding resolved).
- [x] `protocol.md` and `SKILL.md` state the completion-notice docs-round suggestion for durable changes.
- [x] `docs/` records the "when to create a skill" heuristic.
- [x] Static checks pass: English-only, no runtime artifacts, `SKILL.md` under 200 lines, and `quick_validate.py` passes if available.
- [x] A fresh-context independent review issues an explicit verdict. (CHANGES; see Review.)

## Decisions

- Brand stays **Spartan** (skill name `spartan`, invocation `/spartan`, folder `spartan/`) - one canonical singular form; the `spartan`/`spartans` split is eliminated.
- Skill source directory becomes `agent-skill/`; symlink leaf names keep the public identity `spartan`.
- Durable per-repo folder is `spartan/` containing `README.md` (explains the folder) plus `tasks/`; namespace kept bare so future subfolders do not force a rename.
- Task filenames use ADR-style `NNNN-slug.md` (4-digit zero-padded). Four digits are sufficient and act as a guardrail; timestamps were rejected (a task is a living document, not an event).
- No orchestrator/dispatcher skill and no commit/push skill: hosts already route among peer skills, and Spartan only advises; commit/push stays explicit and human-authorized.
- Completion notices SHOULD suggest a documentation round only when durable behavior or decisions changed, keeping docs current without becoming ritual.

## Work Completed

- Authored this task consolidating the design decisions (planning round in the Claude Code cockpit).
- Renamed the portable skill source to `agent-skill/` and repointed both project-local discovery links to it.
- Renamed the durable task namespace to `spartan/`, added its README, and numbered the four existing task artifacts from 0001 through 0004.
- Added `created_at` to each existing task and to the template; the template now explains immutable four-digit creation-order filenames.
- Updated repository paths, installation instructions, historical task references, and the completion-suggestion template source to the new canonical locations.
- Added the conditional documentation-round suggestion to the protocol and skill instructions, and documented the skill-creation heuristic in `docs/WHEN-TO-CREATE-A-SKILL.md`.
- Ran the scoped static checks successfully.
- Follow-up implementer round (owner drove the repo-directory rename to `agent-spartan-protocol`): applied review finding 1 (both `README.md` install-block `SPARTAN_REPO` defaults now use the singular repo path) and finding 2 (owner intent is singular: README title/intro product name and `AGENTS.md:7` reconciled to "Agent Spartan Protocol" / `agent-spartan-protocol`). Recreated the two global home symlinks (`~/.agents/skills/spartan`, `~/.claude/skills/spartan`), which had dangled after the directory rename, to target the new `agent-skill/` path. Awaits re-review.

## Evidence

- Initial layout confirmed: the skill source carried the `spartan` name (with `SKILL.md`, `references/`, `assets/`, `agents/`); both discovery symlinks targeted that source; durable tasks used the plural namespace.
- Discovery check: `.agents/skills/spartan` and `.claude/skills/spartan` each target `../../agent-skill`, and `SKILL.md` is readable through both links.
- Namespace check: a recursive Markdown/YAML scan for the former plural task namespace and former skill-reference path returned no matches.
- Task convention check: every file in `spartan/tasks/` matches `NNNN-slug.md` and has a `created_at: YYYY-MM-DD` frontmatter value.
- Static checks: all Markdown/YAML is ASCII-only; `agent-skill/` and `spartan/` contain no runtime, manifest, or state-artifact extensions; `agent-skill/SKILL.md` is 79 lines.
- `quick_validate.py` is unavailable in this environment, so it was skipped as permitted by the acceptance criterion.
- Documentation check: the conditional durable-change documentation-round rule appears in both canonical instructions; `spartan/README.md` and `docs/WHEN-TO-CREATE-A-SKILL.md` are readable.
- `git status --short --branch` now reports a repository with no commits and all project files untracked. No Git action was taken in this round; the implementation remains uncommitted as required.
- Review round (2026-07-20, read-only): symlink resolution via `readlink` and `test -f` through both links = pass; `wc -l agent-skill/SKILL.md` = 79; non-ASCII scan across `*.md`/`*.yaml` = none; `find agent-skill spartan -type f` extensions = only `.md`/`.yaml`; `find . -name quick_validate.py` = absent. `grep -riE "spartans"` = install-path and product-name hits only (see finding 1/2); task-namespace and skill-reference scans = clean. `ls "/Users/.../agent-spartans-protocol"` = orphan dir with only `.idea/` and no `agent-skill/`, confirming the install default would dangle.

## Review

Verdict: APPROVE

Owner-accepted in a Claude Code (Opus) cockpit round; formal cross-vendor re-review waived by the owner. The prior fresh-context review (Claude Code, Opus, high effort) passed all structural, template, documentation, and static-constraint criteria and raised two findings, both since applied and re-verified (see below).

Verified and passing:
- Skill namespace: `agent-skill/` holds the skill (`SKILL.md`, `references/{protocol,routing}.md`, `assets/task-template.md`, `agents/openai.yaml`); no old skill-source `spartan/` directory remains.
- Discovery symlinks: `.agents/skills/spartan` and `.claude/skills/spartan` both resolve to `../../agent-skill`; `SKILL.md` is readable through each; deletion test still holds (removing the link leaves durable task files intact).
- Task namespace: durable `spartan/` contains `README.md` + `tasks/`; no plural task directory remains.
- Numbered artifacts: all four task files match `NNNN-slug.md`; each carries a `created_at` date (0001 2026-07-19; 0002/0003/0004 2026-07-20). Cross-references use the numbered form.
- Template source: `assets/task-template.md` includes `created_at` and documents the immutable four-digit creation-order convention (line 16); the pasteable prompt block uses `NNNN-task-slug.md`.
- Template-path correction (folded CHANGES from 0003): every reference now points to `assets/task-template.md` (SKILL.md:10,75; protocol.md:156). No stale template path found.
- Documentation-round guidance: present in both `protocol.md` (line 152) and `SKILL.md` (line 73), correctly conditioned on durable behavior/decision changes.
- Skill-creation heuristic: `docs/WHEN-TO-CREATE-A-SKILL.md` present and points to `agent-skill/references/protocol.md`.
- Static constraints: all Markdown/YAML is ASCII-only (English); `agent-skill/` and `spartan/` contain only `.md`/`.yaml`, no runtime/state artifacts; `agent-skill/SKILL.md` is 79 lines (< 200); `quick_validate.py` is absent from the repo, so it is correctly skipped.

Findings (both ADDRESSED in the follow-up implementer round; re-verified and owner-accepted):

1. (Material, blocking) `README.md` install blocks set `SPARTAN_REPO="/Users/angelopublio/Documents/DEV/agent-spartans-protocol"` (lines 63 and 97), but this repository — the only one containing `agent-skill/` — is the singular `.../agent-spartan-protocol`. The plural path resolves to a separate, essentially empty directory (only `.idea/`, no `agent-skill/`), so copy-pasting either install block creates dangling symlinks and the README's own verification step (`test -f "$HOME/.<host>/skills/spartan/SKILL.md"`) fails. **Applied:** both `SPARTAN_REPO` defaults now use `agent-spartan-protocol`; the README verification commands resolve against this checkout.
2. (Minor, consistency) The product name is still plural: `README.md` title/intro (lines 1, 5) "Agent Spartans Protocol" and `AGENTS.md:7` "`agent-spartans-protocol`". **Applied:** owner intent is a singular canonical brand; README title/intro now read "Agent Spartan Protocol" and `AGENTS.md:7` reads `agent-spartan-protocol`. (The 0001:38 mention of the separate "Agent Spartans Workbench" is a historical reference to a different repo and remains out of scope.)

## Blockers

None. Finding 1 is a bounded documentation fix, not a blocker.

## Next Action

None - task completed.

## Completion Notice

Completed: the package was restructured to the canonical singular layout - skill source `agent-skill/`, durable `spartan/` with `README.md` + `tasks/`, `NNNN-slug.md` filenames with `created_at`, corrected template path (`assets/task-template.md`), the conditional documentation-round suggestion, and the skill-creation heuristic in `docs/`. Review findings 1 and 2 (README install path and product-name plurality) were applied and re-verified: a repo-wide scan shows no `agent-spartans-protocol` install path and the README verification commands resolve against this checkout. Owner-accepted; no follow-up work is identified for this task.
