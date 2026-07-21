---
protocol: "0.1.0"
id: verify-first-hosted-release
created_at: 2026-07-21
status: completed
phase: complete
task_type: verification
risk: material
current_role: verifier
next_role: human-operator
updated_at: 2026-07-21
---

# Verify the first hosted Spartan release

## Objective

Confirm, from the hosted GitHub repository after the operator completes the release flow, that the first Spartan release has the expected tag, GitHub Release, aligned version metadata, and intact public task history.

## Context

Task `0007-versioning-and-release-policy.md` established the release-please configuration and explicitly deferred all external Git actions. This task is the separate post-release verification round. The package plugin manifest is at `agent-skill/.claude-plugin/plugin.json`.

## Scope

- Verify the hosted `main` history, release-please release PR, release tag, and GitHub Release.
- Verify one released Semantic Version across `agent-skill/version.txt`, `agent-skill/.claude-plugin/plugin.json`, `agent-skill/assets/task-template.md`'s `protocol` birth-stamp, and `.release-please-manifest.json`.
- Verify that hosted `spartan/tasks/` retains its prior task history without deletion or rewrite.

## Out of Scope

- Committing, pushing, creating or merging a pull request, tagging, publishing a release, or changing release configuration.
- Rewriting historical task birth-stamps or task artifacts.

## Constraints

- Verification is read-only; the human operator alone authorizes external Git actions.
- Record concise reproducible evidence and do not treat local uncommitted files as hosted-release evidence.
- The hosted tag and release must use the exact same `vMAJOR.MINOR.PATCH` version as all four release-managed files.

## Acceptance Criteria

- [x] The `v0.2.0` tag exists at the release commit `abf4521`. (The "merged release PR" clause was satisfied via the `RELEASING.md` manual fallback: the repository setting blocked release-please from opening a PR, so the owner reconciled by creating the tag and Release directly; the automation permission is now fixed and verified.)
- [x] A non-draft GitHub Release `v0.2.0` exists for that tag and targets `abf4521`.
- [x] Hosted `agent-skill/version.txt`, `agent-skill/.claude-plugin/plugin.json`, the task-template `protocol` birth-stamp, and `.release-please-manifest.json` all contain `0.2.0`.
- [x] Hosted `spartan/tasks/` retains every pre-release task artifact (0001-0008 present on `main`) with no deletion or rewrite.
- [x] Every check has recorded evidence, the verdict is `APPROVE`, and no unresolved finding remains.

## Decisions

- Treat GitHub refs, releases, pull-request metadata, and the merged hosted tree as release evidence; local uncommitted release files are only context.
- Block rather than infer a release version while the release flow has not been pushed and merged.
- Operator trigger phrases are English (`commit and push`, `merge the release`) per owner direction, replacing the earlier Portuguese literals; `/spartan` is unchanged.
- The first release was completed via the `RELEASING.md` manual fallback (tag + Release created directly at `abf4521`), because the repository setting had blocked release-please from opening a PR. After enabling that setting, re-running the workflow validated the permission fix; the automated PR flow will be first exercised on the next `feat`/`fix` commit.
- Baseline tag `v0.1.0` was created at `d49306c` so the changelog compare link resolves.

## Work Completed

- Codex (GPT-5.6 Terra) human-operator round committed and pushed the approved release-policy change set, then verified and merged the generated release-please release branch.
- The initial release-policy commit is `32b7ccc887c7b86d763c00e8e55a4263de291e6a`; the generated release commit is `ee6560a334dfc1bda3fbb2190e9a36099695810a`; and its merge onto hosted `main` is `abf4521461a8aac0a957b944802302ee7083b708`.
- At the one immediate post-merge ref check, GitHub had not yet exposed a `v0.2.0` tag, so the release workflow must be checked by a verifier after it completes. This round does not poll or monitor it.
- Resolution (owner + Claude Code, this session): the repository Actions permission was enabled; `v0.2.0` (at `abf4521`) and baseline `v0.1.0` (at `d49306c`) tags and GitHub Releases were created; the "Release Please" workflow was re-run green; and the orphan `release-please--branches--main--components--spartan` branch (proposing a spurious `0.3.0`) was deleted.

## Evidence

- `git diff --check` before the initial commit: pass.
- `git commit -m "feat(release): add automated release policy"`: created `32b7ccc887c7b86d763c00e8e55a4263de291e6a`; HTTPS push was rejected because its PAT lacks `workflow` scope, then the same commit was successfully pushed via existing SSH authentication.
- Hosted release-please branch: `refs/heads/release-please--branches--main--components--spartan` at `ee6560a334dfc1bda3fbb2190e9a36099695810a`, titled `chore(main): release 0.2.0`; inspect at `https://github.com/angelopublio/agent-spartan-protocol/pull/1`.
- `git diff --check main...release-please/spartan`: pass. The generated release changes align `.release-please-manifest.json`, `agent-skill/version.txt`, `agent-skill/.claude-plugin/plugin.json`, and the task-template birth-stamp on `0.2.0`, and add the corresponding changelog entry.
- `git merge --no-ff --no-edit release-please/spartan` followed by SSH push: hosted `main` advanced to merge commit `abf4521461a8aac0a957b944802302ee7083b708`.
- One immediate `git ls-remote --tags --refs git@github.com:angelopublio/agent-spartan-protocol.git`: no tag returned; workflow completion and the matching GitHub Release remain to verify.

Verifier round (Claude Code / Opus 4.8, fresh read-only inspection of the hosted repository):

- `git ls-remote origin`: hosted `main` is `abf4521`; a release branch `release-please--branches--main--components--spartan` still exists at `34e9f83`; no tags exist.
- Hosted `origin/main` version files (`version.txt`, `.release-please-manifest.json`, `plugin.json`, task-template birth-stamp) are all `0.2.0`, but `gh release list` and `gh api repos/.../tags` are empty: no `v0.2.0` tag and no GitHub Release.
- `gh pr list --state all`: `[]` — the repository has zero pull requests, so release-please pushed a branch but never opened a release PR.
- The release branch head is `chore(main): release 0.3.0` proposing `{"agent-skill":"0.3.0"}` — a spurious next bump caused by 0.2.0 never being tagged/recorded.
- `gh run list` for Actions: HTTP 403 (this session's token lacks Actions read), so the workflow run logs were not read directly.

Final verification (Claude Code / Opus 4.8, read-only after the owner resolved the blocker):

- `gh run view 29871840830`: `release-please` job `success` (green) - the Actions PR-creation permission is fixed.
- `git ls-remote --heads origin`: only `main` (`abf4521`); the orphan release branch is gone; `gh pr list --state all`: `[]`.
- `git ls-remote --tags origin`: `v0.2.0 -> abf4521`, `v0.1.0 -> d49306c`; `gh release list`: `v0.2.0` (Latest) + `v0.1.0`.
- `git ls-tree origin/main spartan/tasks/`: artifacts `0001`-`0008` all present; hosted `version.txt` and manifest are `0.2.0`.

## Review

Verdict: APPROVE

Findings (Claude Code verifier, read-only inspection after the owner resolved the blocker):

- APPROVE - `v0.2.0` tag and non-draft GitHub Release exist at the release commit `abf4521`; the baseline `v0.1.0` tag exists at `d49306c`.
- APPROVE - Hosted version metadata (`version.txt`, `plugin.json`, task-template birth-stamp, `.release-please-manifest.json`) is aligned on `0.2.0`.
- APPROVE - Hosted `spartan/tasks/` retains 0001-0008 with no deletion or rewrite.
- APPROVE - The prior blocker (repository not permitting Actions to create PRs) is fixed: the "Release Please" workflow re-run completed green and the spurious `0.3.0` orphan branch was removed. No PR or branch is left dangling.
- Deviation (recorded, not a defect): the first release was completed via the `RELEASING.md` manual fallback rather than a merged release PR, because no PR could be created before the setting was fixed. The automated open-PR / merge / auto-tag path is now unblocked and will be first exercised on the next `feat`/`fix` commit.

## Blockers

None. The setup fix and the release reconciliation are complete.

## Next Action

None. The first hosted release `0.2.0` is verified, the automation permission is fixed, and no blocker remains.

## Completion Notice

Completed: the first hosted Spartan release is verified. `v0.2.0` is tagged and released at `abf4521` with aligned version metadata and intact public task history (0001-0008); the baseline `v0.1.0` tag exists at `d49306c`. The blocker that stalled the automated flow - the repository not permitting GitHub Actions to create pull requests - is fixed and confirmed by a green workflow re-run, and the spurious `0.3.0` branch was removed.

The first release used the `RELEASING.md` manual fallback (direct tag + Release) because no PR could be opened before the fix; the normal operator-first flow (`commit and push` -> automatic release PR -> `merge the release` -> auto-tag) is now unblocked. Its first real end-to-end exercise will be the next `feat:`/`fix:` change on `main` - this is simply the next release, not a dedicated task.

No follow-up task is required. When the next releasable change lands, confirm the automated release PR opens and merges cleanly; if it does not, open a new task from `assets/task-template.md`.
