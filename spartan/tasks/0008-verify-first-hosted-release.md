---
protocol: "0.1.0"
id: verify-first-hosted-release
created_at: 2026-07-21
status: blocked
phase: verifying
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

- [ ] The merged release-please release PR on hosted `main` identifies the released version, and the expected `vMAJOR.MINOR.PATCH` tag exists at its release commit.
- [ ] A non-draft GitHub Release exists for that exact tag and targets the same release commit.
- [ ] Hosted `agent-skill/version.txt`, `agent-skill/.claude-plugin/plugin.json`, `agent-skill/assets/task-template.md`'s `protocol` value, and `.release-please-manifest.json` all contain the same released Semantic Version.
- [ ] Hosted `spartan/tasks/` retains every pre-release task artifact and its Git ancestry shows no deletion or rewrite of that history.
- [ ] Every check has recorded evidence, the review verdict is `APPROVE`, and no unresolved finding remains.

## Decisions

- Treat GitHub refs, releases, pull-request metadata, and the merged hosted tree as release evidence; local uncommitted release files are only context.
- Block rather than infer a release version while the release flow has not been pushed and merged.
- Operator trigger phrases are English (`commit and push`, `merge the release`) per owner direction, replacing the earlier Portuguese literals; `/spartan` is unchanged.

## Work Completed

- Codex verifier round created this uniquely numbered artifact and inspected the local repository plus GitHub's hosted refs, releases, and pull requests.
- The required operator release flow is not yet observable on GitHub, so no release acceptance criterion can be confirmed.

## Evidence

- `git -C /Users/angelopublio/Documents/DEV/agent-spartan-protocol status --short --branch`: local branch is `main...origin/main` with uncommitted release-policy files, including `.github/`, `.release-please-manifest.json`, `agent-skill/version.txt`, and `agent-skill/.claude-plugin/plugin.json`; these are not hosted evidence.
- `git -C /Users/angelopublio/Documents/DEV/agent-spartan-protocol ls-remote --heads origin main`: hosted `main` is `d49306c74e96cc30c3b4e75f4f561e60b8a3ca29`, the pre-release bootstrap commit.
- `git -C /Users/angelopublio/Documents/DEV/agent-spartan-protocol ls-remote --tags --refs origin`: no hosted tags.
- `gh api repos/angelopublio/agent-spartan-protocol/releases --paginate`: `[]` (no GitHub Releases).
- `gh pr list --repo angelopublio/agent-spartan-protocol --state all --limit 20 --json number,title,state,mergedAt,headRefName,baseRefName,url`: no release-please PR is present.
- Local inspection: task artifacts `0001` through `0007` exist locally, but hosted task-history preservation cannot be confirmed until the release changes are pushed and the release PR is merged.

## Review

Verdict: BLOCKED

Findings:

- BLOCKED - No hosted release-please release PR, tag, or GitHub Release exists. Hosted `main` remains at the bootstrap commit, so there is no released version or post-release hosted task history to verify.

## Blockers

The operator has not yet completed the explicitly required external actions: `commit and push` the release-policy changes, then `merge the release` for the generated release-please release PR.

## Next Action

Have the human operator run `commit and push` and merge the release-please release PR; success is that GitHub shows the merged release PR and completed workflow, making the resulting tag, release, metadata, and task history available for read-only verification.

## Next Handoff

```text
Recommended execution (human decides):
- Host: Codex, because the current blocker is an explicitly human-authorized GitHub release action
- Model and effort: GPT-5.6 Terra, reasoning effort medium
- Invocation: `$spartan`, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/0008-verify-first-hosted-release.md`.

Act as human-operator. Complete the explicitly authorized release flow: run `commit and push` for the release-policy changes, then `merge the release` for the generated release-please release PR; success is that GitHub exposes the merged release PR and its completed release workflow. Update the same task file with the resulting URLs or commit/tag identifiers that a verifier can inspect.

Return only the next handoff, or a completion notice if no work remains.
```
