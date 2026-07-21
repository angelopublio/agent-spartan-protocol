---
protocol: "0.1.0"
id: versioning-and-release-policy
created_at: 2026-07-21
status: completed
phase: complete
task_type: implementation
risk: material
current_role: independent-reviewer
next_role: human-operator
updated_at: 2026-07-21
---

# Versioning and release policy for the protocol

## Objective

Adopt passive Semantic Versioning for the Spartan protocol package and make repository releases automatic after a human-authorized release-PR merge, while preserving the static, human-mediated protocol boundary.

## Context

The validated package baseline was documented as version `0.1`; this task normalizes it to SemVer `0.1.0`. Before this round the repository had no plugin manifest, changelog, release workflow, or release-please configuration. Baseline commit `d49306c74e96cc30c3b4e75f4f561e60b8a3ca29` is the release-please bootstrap boundary.

## Scope

- Add release-please repository CI driven by Conventional Commits.
- Add passive package metadata, changelog history, and automated version updates.
- Add repository workflow rules for pulling a clean tree and writing compliant commits.
- Add the task-template `protocol` birth-stamp without runtime behavior or adopting-repository migration.
- Document the public task history decision and the operator-first release flow, including a deletion-test-safe fallback.

## Out of Scope

- Commit, push, pull-request creation, merge, tag creation, or release publication in this round.
- Runtime release code, network checks, package-manager manifests, or dependencies inside `agent-skill/`.
- Rewriting existing task artifacts or versioning an adopting project's repository.

## Constraints

- Version data is passive package metadata.
- Release automation is GitHub repository CI and is not part of the installable skill package.
- Spartan must not initiate or supervise release execution; the human explicitly authorizes external actions.
- `spartan/tasks/` remains public as living proof of the protocol.
- Persisted content and handoffs are English; the three owner phrases remain literal operator invocations.

## Acceptance Criteria

- [x] A release-please workflow on `main` derives releases from Conventional Commits, maintains the package changelog and `version.txt`, updates the plugin manifest through `extra-files`, updates release state, and creates a `vMAJOR.MINOR.PATCH` tag plus GitHub Release after its release PR is merged.
- [x] `AGENTS.md` requires Conventional Commits with the allowed types and breaking-change signals, and advises `git pull --ff-only` before work when the tree is clean.
- [x] New tasks receive a passive `protocol: "0.1.0"` birth-stamp that release PRs advance for future tasks only; no runtime check or adopting-repository rewrite is introduced.
- [x] Repository documentation records that `spartan/tasks/` stays public as living proof.
- [x] `RELEASING.md` starts with `/spartan`, `commit and push`, and `merge the release`, then leaves SemVer, tags, release-please, and the manual fallback in a technical reference at the end.
- [x] The manual fallback explicitly preserves the deletion test and keeps release tooling outside `agent-skill/`.
- [x] A fresh-context independent reviewer issues `APPROVE` with no unresolved finding.

## Decisions

- Treat `agent-skill/` as the single releasable package and normalize the existing baseline to `0.1.0`.
- Store the Claude-compatible plugin manifest at `agent-skill/.claude-plugin/plugin.json`; the package's root `SKILL.md` remains the single discovered skill.
- Keep the GitHub workflow and release-please configuration at repository root, outside the installable package.
- Use release-please's `simple` strategy with its passive `version.txt`, the built-in GitHub token, `v` tags without a component prefix, and baseline commit `d49306c74e96cc30c3b4e75f4f561e60b8a3ca29`.
- Update both the plugin manifest and task template through `extra-files`; copied task birth-stamps never change afterward.
- Keep completed task artifacts public and exclude them from release cleanup.
- After this task's approval, per owner direction and the repository English-only rule, the operator trigger phrases were normalized to English (`commit and push`, `merge the release`); this is wording only and leaves the release policy and the APPROVE verdict unchanged. The original Portuguese literals (`commita`, `mergeia o release`) are noted here for traceability.

## Work Completed

- Codex (GPT-5) implementation round created the release workflow, release-please state/configuration, plugin manifest, passive `version.txt`, changelog, operator guide, repository rules, birth-stamp contract, and public-proof documentation.
- Claude Code (Opus 4.8) independent-reviewer round, fresh context and read-only, verified all seven acceptance criteria and reproduced the recorded checks cross-vendor; verdict `APPROVE`.
- No external Git action was performed in any round.

## Evidence

- `git diff --check`: pass.
- Ruby JSON/YAML parse: all three JSON files and both relevant YAML files parse successfully.
- `jq` release-config assertions: package path, `simple` strategy, tag settings, and both `extra-files` rules match the intended configuration.
- Version-alignment check: plugin manifest, release-please state, `version.txt`, task template, and task 0007 all report `0.1.0`.
- Workflow scan: push-to-`main`, write permissions, release-please action v5, and both manifest-mode file inputs are present.
- Git bootstrap check: `d49306c74e96cc30c3b4e75f4f561e60b8a3ca29` exists and is an ancestor of `HEAD`.
- Task-artifact check: exactly one `0007-*.md` exists; it has no unresolved template placeholder and all three next-role encodings resolve to `independent-reviewer` / Claude Code.
- Static boundary checks: changed protocol files are ASCII-only; `agent-skill/` contains only JSON, Markdown, text, and YAML, with no executable/runtime file; `.github/workflows/` remains outside the package.
- Discovery and size check: both project skill links still resolve `SKILL.md`, the Claude link resolves the new plugin manifest, and `agent-skill/SKILL.md` remains 81 lines.
- Hosted workflow execution is intentionally pending a later explicitly authorized commit/push; no local `actionlint` or release-please executable is installed.

Independent review reproduction (Claude Code / Opus 4.8, fresh context, read-only):

- `git diff --check` vs baseline: pass; JSON parse of all three JSON files and YAML parse of both YAML files: pass.
- Version alignment: manifest, `plugin.json`, `version.txt`, task-template birth-stamp, task 0007, and CHANGELOG heading all report `0.1.0`.
- Config wiring: `simple` strategy, `component: spartan`, `include-component-in-tag: false` + `include-v-in-tag: true` yield `v0.1.0`; JSON `extra-files` targets `$.version` in the plugin manifest and the generic marker `# x-release-please-version` exists on the template's `protocol` line; both `extra-files` target files exist at their package-relative paths.
- Bootstrap `d49306c...` exists and is an ancestor of `HEAD`; exactly one `0007-*.md` exists with zero unresolved template placeholders.
- Boundary: `agent-skill/` holds only `.md/.txt/.json/.yaml`, zero executable-bit files; `SKILL.md` is 81 lines (<200); discovery symlinks resolve `SKILL.md` and the new `plugin.json` to the package source.
- Old field `protocol_version: "0.1"` remains only in historical tasks 0001-0006, as required by the no-rewrite birth-stamp invariant.

## Review

Verdict: APPROVE

Findings (Claude Code independent-reviewer, fresh context, read-only; cross-vendor from the Codex implementation):

- PASS - AC1: the `main` push workflow runs `release-please-action@v5` in manifest mode; `simple` strategy maintains `version.txt` and `CHANGELOG.md`, JSON `extra-files` updates `plugin.json`'s `$.version`, the manifest holds release state, and the tag/Release format resolves to `v0.1.0` after the release PR merges.
- PASS - AC2: `AGENTS.md` "Repository workflow" mandates Conventional Commits with `feat/fix/docs/chore` and `feat!`/`BREAKING CHANGE:` signals, and advises `git pull --ff-only` when the tree is clean.
- PASS - AC3: the template carries `protocol: "0.1.0" # x-release-please-version`; the generic `extra-files` rule advances it for future tasks only, `protocol.md` documents it as passive with no runtime check or adopting-repo rewrite, and existing task birth-stamps are untouched.
- PASS - AC4: both `README.md` and `spartan/README.md` record that `spartan/tasks/` stays public as living proof.
- PASS - AC5: `RELEASING.md` opens with the `/spartan`, `commit and push`, `merge the release` flow and relegates SemVer, tags, release-please, and the manual fallback to a closing technical reference.
- PASS - AC6: the manual fallback explicitly preserves the deletion test and keeps release tooling outside `agent-skill/`.
- PASS - AC7: this fresh-context independent review verifies every criterion above with no unresolved finding.
- Non-blocking observations (no change required): the workflow's `issues: write` permission is broader than the strict minimum but is a common, harmless release-please setting; the `protocol_version` -> `protocol` field rename leaves historical tasks 0001-0006 on the old name, which is required by the no-rewrite invariant, not a defect.
- Not exercised (out of scope this round): live hosted CI execution, which is intentionally deferred to a later human-authorized commit/push; static verification is complete and internally consistent.

## Blockers

None.

## Next Action

None. All seven acceptance criteria are satisfied, relevant checks have recorded outcomes, the independent review verdict is `APPROVE`, and no blocker remains.

## Completion Notice

Completed: passive Semantic Versioning and operator-first automated release policy are adopted and independently APPROVED cross-vendor (Codex implemented, Claude Code reviewed). Release-please CI on `main`, manifest-mode configuration, the `0.1.0` baseline across manifest/`version.txt`/`plugin.json`/template birth-stamp, Conventional-Commit and `git pull --ff-only` workflow rules, the passive birth-stamp contract, the operator-first `RELEASING.md` with a deletion-test-safe manual fallback, and the public-proof documentation are all in place and internally consistent. No external Git action was taken; the static protocol boundary is preserved.

Documentation is already covered within this task (`README.md`, `RELEASING.md`, `AGENTS.md`, `protocol.md`), so no separate documentation round is warranted.

The one identifiable follow-up is exercising the release path end to end, which requires human-authorized external actions this task deliberately excluded. It is the operator's explicit `commit and push` then `merge the release` flow (see `RELEASING.md`); Spartan must not initiate it. Once the first release PR has merged and the hosted workflow has run, a lightweight verification round can confirm the produced tag and GitHub Release. Suggested as a new task, not a reopening of this one:

```text
Recommended execution (human decides):
- Host: Codex, cross-vendor verification of the hosted CI outcome after the human runs the release flow
- Model and effort: GPT-5.6 Terra, reasoning effort medium (fallback: GPT-5.6 Sol if deeper analysis is needed)
- Invocation: `$spartan`, passing the prompt block below as the argument
```

```text
Create a new uniquely numbered task from `agent-skill/assets/task-template.md` in `spartan/tasks/` for verifying the first hosted Spartan release.

Act as verifier. After the operator has run `commit and push` and merged the release-please release PR, confirm that the hosted workflow created the expected `vMAJOR.MINOR.PATCH` tag and matching GitHub Release, that `agent-skill/version.txt`, `.claude-plugin/plugin.json`, the task-template birth-stamp, and `.release-please-manifest.json` all advanced to the same version, and that `spartan/tasks/` history was neither deleted nor rewritten; success is every check confirmed with recorded evidence and no unresolved finding.
Run the relevant repository checks and update that new task file.

Return only the next handoff, or a completion notice if no work remains.
```
