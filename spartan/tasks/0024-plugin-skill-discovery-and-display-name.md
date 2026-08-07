---
protocol: "0.5.0"
id: plugin-skill-discovery-and-display-name
created_at: 2026-08-07
status: active
phase: verifying
task_type: implementation
risk: material
current_role: human-operator
next_role: human-operator
updated_at: 2026-08-07
handoff_id: HX-003
next_handoff_id: HX-004
---

# Plugin skill discovery and marketplace display name

## Objective

A marketplace install of the plugin exposes the `spartan` skill in every Claude surface (CLI and desktop app), and the plugin is listed as "Agent Spartan Protocol" instead of "Spartan".

## Context

The repository owner installed the plugin on a second account through Plugins / Marketplace in the Claude desktop app. The plugin installed at version 0.5.0, but its "Habilidades" tab reported that the plugin has no skills or agents, so `/spartan:spartan` was not available. The listing also showed the label "Spartan".

The cause was the package layout. `agent-skill/` was simultaneously the plugin root, the portable skill folder, and the home of the Codex interface metadata, with `SKILL.md` sitting at the plugin root. A plugin-root `SKILL.md` with no `skills/` directory and no `skills` manifest field is only auto-detected from Claude Code v2.1.142 onward, and the desktop app's plugin browser did not detect it.

## Scope

- `agent-skill/`: move the skill payload (`SKILL.md`, `references/`, `assets/`, `agents/`) into `agent-skill/skills/spartan/`.
- `agent-skill/.claude-plugin/plugin.json`: `displayName`.
- `.claude-plugin/marketplace.json`: `displayName` on the `spartan` plugin entry.
- `release-please-config.json`: `extra-files` path for the task template.
- Repository symlinks `.claude/skills/spartan` and `.agents/skills/spartan`, and their documented personal equivalents.
- `README.md`, `AGENTS.md`, `RELEASING.md`, `docs/INSTALL-DEV.md`, `docs/WHEN-TO-CREATE-A-SKILL.md`.

## Out of Scope

- Renaming the plugin `name` identifier, which would break existing installs and the `/spartan:spartan` token.
- Renaming the `agent-skill/` directory itself; it is the release-please package key and renaming it mid-stream risks the release automation for no functional gain.
- Rewriting path references inside `spartan/tasks/0001`-`0023`; those are point-in-time records, not maintained documentation.
- Confirming the fix on the second account's desktop app. That observation belongs to the owner and closes the last acceptance criterion.

## Constraints

- `name` must stay `spartan` in both manifests: it is the stable identifier used by `enabledPlugins`, `pluginConfigs`, and the invocation namespace.
- The marketplace `source` must stay `./agent-skill` so existing installs keep resolving.
- The relative links inside `SKILL.md` must keep resolving; the payload therefore moves as one unit.
- Repository content stays English.

## Acceptance Criteria

- [x] The skill payload lives at `agent-skill/skills/spartan/`, and the package root holds packaging only.
- [x] Both manifests carry `displayName: "Agent Spartan Protocol"` with `name` unchanged.
- [x] Both manifests pass `claude plugin validate`.
- [x] Release automation points at the moved task template, and the version marker survives the move.
- [x] Project-local and personal symlinks resolve to the new skill folder for both hosts.
- [x] No live document still references the pre-move payload paths.
- [ ] A marketplace install from a published version lists the skill and the new label in the Claude desktop app.

## Decisions

- Adopt the `skills/<name>/` layout rather than declaring `"skills": ["./"]` at the plugin root. Rationale: `skills/` is the layout every host discovers without a manifest hint, whereas both root-level paths are version-gated (auto-detect needs v2.1.142; `"."` fails manifest validation before v2.1.221). It also gives a clean diagnostic: if the desktop app still fails on this layout, the cause is elsewhere.
- Separate the package root from the skill folder. Rationale: while the plugin root *was* the skill folder, the skill competed for the directory names a plugin root reserves (`agents/`, `commands/`, `hooks/`, `skills/`), and a symlink install dragged `CHANGELOG.md`, `version.txt`, and `.claude-plugin/` into `~/.claude/skills/spartan`.
- Move `agents/openai.yaml` down with the payload instead of handling it separately. Its position relative to the skill folder is unchanged, so Codex still resolves it at `<skill>/agents/openai.yaml`, and it is no longer in the plugin root where the name is reserved.
- Change the label through `displayName` in both manifests rather than through `name`. The marketplace entry needs its own copy because the listing renders before the plugin manifest is fetched.
- Record the root/folder split in `AGENTS.md` so a later round does not reintroduce skill content at the package root.
- Commit this work under a `feat` type when the owner authorizes the commit. Release-please bumps the version only for `feat`, `fix`, or a breaking change; a `chore` or `docs` commit would leave `plugin.json` at 0.5.0, and the desktop app would never offer an update. `feat` yields 0.6.0. A `!` or `BREAKING CHANGE` footer is deliberately avoided: this configuration does not set `bump-minor-pre-major`, so a breaking change would jump straight to 1.0.0.

## Work Completed

- Moved `SKILL.md`, `references/`, `assets/`, and `agents/` into `agent-skill/skills/spartan/` with `git mv`; Git recorded all six as pure renames with no content change.
- Set `displayName: "Agent Spartan Protocol"` in `agent-skill/.claude-plugin/plugin.json` and in the `spartan` entry of `.claude-plugin/marketplace.json`, leaving `name` and `source` untouched. Removed the interim `"skills": ["./"]` declaration, made redundant by the `skills/` directory.
- Updated `release-please-config.json` `extra-files` to `skills/spartan/assets/task-template.md`.
- Repointed the project-local and personal symlinks for both hosts to `agent-skill/skills/spartan`.
- Updated `README.md` (package tree, two reference links, symlink examples, plus a paragraph explaining the split), `AGENTS.md` (new change-discipline rule and two paths), `RELEASING.md` (two paths), `docs/INSTALL-DEV.md` (both symlink recipes and the canonical-folder sentence), and `docs/WHEN-TO-CREATE-A-SKILL.md` (one path).
- Audited every skill symlink under the home directory. The only remaining links to the pre-move path belong to the marketplace's own clone of this repository at commit `b7ca22d`; they are this repository's committed project-local links and resolve themselves when the marketplace pulls the new commit. No other repository on the machine links the skill.
- The restructure and documentation rounds above were executed by Claude Code running Opus 5.
- Released the reviewed work on the owner's explicit authorization: committed as `ca86623` under a `feat` type, pushed to `main`, inspected and merged the release proposal (#7), and confirmed the published release. Executed by Claude Code running Opus 5, acting as `human-operator` under HX-003.
- Independently reviewed the restructure and both manifest changes: verified the moved payload, both manifests, `release-please-config.json`, the CI workflow, both symlink pairs, and every live document for stale pre-move paths. Executed by Claude Code running Sonnet 5. This is same-vendor (Anthropic) review, not the cross-vendor review the prior handoff recommended (Codex/GPT-5.6 Terra) — the human ran this round in Claude Code instead; recorded here per the routing reference's requirement that the actually-used host be visible even when it differs from the recommendation.

## Evidence

- `claude plugin validate .` and `claude plugin validate ./agent-skill`: both `✔ Validation passed` (re-run this round, `claude --version` 2.1.215).
- `git status --short` with rename detection: six `R` entries under `agent-skill/`, zero content diff (re-confirmed this round).
- `agent-skill/.claude-plugin/plugin.json`: `name: "spartan"`, `displayName: "Agent Spartan Protocol"`, `version: "0.5.0"` — no `skills` field, so discovery relies solely on the `skills/` directory as intended.
- `.claude-plugin/marketplace.json`: `spartan` entry carries `displayName: "Agent Spartan Protocol"`, `source: "./agent-skill"` unchanged.
- `release-please-config.json`: `extra-files` generic entry reads `skills/spartan/assets/task-template.md`, which resolves (relative to package `agent-skill/`) to the moved file; the JSON `extra-files` entry for `plugin.json` is untouched and correctly still points at the unmoved manifest.
- `grep x-release-please-version agent-skill/skills/spartan/assets/task-template.md`: marker intact at line 2.
- `.github/workflows/release-please.yml`: only references `release-please-config.json` and `.release-please-manifest.json` by name; no hardcoded payload path to go stale.
- Relative-link check inside the moved `SKILL.md`: all four links (`references/protocol.md`, `references/routing.md`, `assets/spartan-readme.md`, `assets/task-template.md`) resolve on disk from the new location.
- `README.md` package-tree diagram matches the actual `agent-skill/` layout on disk exactly (packaging files at root, payload under `skills/spartan/`).
- Grep across README.md, AGENTS.md, RELEASING.md, docs/INSTALL-DEV.md, docs/WHEN-TO-CREATE-A-SKILL.md for `agent-skill/SKILL.md|references|assets|agents/`: every hit is the corrected `agent-skill/skills/spartan/...` form; a repo-wide grep excluding `spartan/tasks/` (point-in-time history, out of scope) and `CHANGELOG.md` returned zero files with a pre-move path.
- `.agents/skills/spartan` and `.claude/skills/spartan` (project-local, `readlink`): both resolve to `../../agent-skill/skills/spartan`.
- `~/.agents/skills/spartan` and `~/.claude/skills/spartan` (personal, `ls -la`): both resolve to the absolute new path.
- `agent-skill/.DS_Store` is listed in `.gitignore` and untracked — not part of this change, not a residue of the move.
- Release: commit `ca86623` on `main`; release proposal #7 (`chore(main): release 0.6.0`) inspected before merging and touching only the five expected files; merged as `b5fb227`; both `release-please.yml` runs completed successfully.
- `gh release view v0.6.0`: published, not a draft, not a prerelease — https://github.com/angelopublio/agent-spartan-protocol/releases/tag/v0.6.0
- The release updated `agent-skill/skills/spartan/assets/task-template.md` at its post-move path, which is direct proof that the corrected `extra-files` rule works; tasks created from now on are born with `protocol: "0.6.0"`.
- Local repository fast-forwarded to `b5fb227`; `version.txt`, `plugin.json`, and the template all read 0.6.0, and tag `v0.6.0` is present locally.
- Post-release re-check: both manifests still pass `claude plugin validate`, and all four symlinks still resolve `SKILL.md`.

## Review

Verdict: APPROVE

Findings:

- None. The restructure is content-identical (pure Git renames), both manifests validate and carry the correct `displayName`/`name`/`source` split, `release-please-config.json` and the release workflow correctly track the moved task template, both symlink pairs resolve, and no live document or config retains a pre-move path.
- Advisory, not a defect: this review ran in Claude Code (Sonnet 5), the same vendor as the implementing round (Claude Code, Opus 5), rather than the cross-vendor review the prior handoff recommended. The human's choice to run it here is within their authority to override the routing suggestion; noted for audit per the routing reference.

## Blockers

None.

## Next Action

Everything this repository can settle is settled and released as v0.6.0. One acceptance criterion stays open because it is an observation only the owner can make: on the second account, update the plugin from the marketplace and confirm the "Habilidades" tab now lists the skill under the "Agent Spartan Protocol" label. Success condition: the last acceptance-criteria checkbox is checked from direct observation, after which this task completes. If the tab is still empty at 0.6.0, the `skills/` layout is ruled out and the next round investigates the desktop app's plugin loader rather than the package layout.

## Next Handoff

```text
Recommended execution (human decides):
- Host: the owner's second account in the Claude desktop app, the only place the remaining criterion is observable
- Model and effort: none applicable; this round is a human observation, not a model round
- Role: human-operator
- Handoff: HX-004
- Invocation: none; open Settings, then Plugins, and use the update control on the Agent Spartan Protocol entry
```

```text
Open `spartan/tasks/0024-plugin-skill-discovery-and-display-name.md` (handoff HX-004).

Act as human-operator. On the second account, update the plugin from the marketplace to 0.6.0 and record whether the "Habilidades" tab lists the spartan skill and whether the entry is labelled "Agent Spartan Protocol".
Run the relevant repository checks and update the same task file.

Return only the next handoff, or a completion notice if no work remains.
```
