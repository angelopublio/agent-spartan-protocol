# Releasing Spartan

## The three-phrase flow

1. `/spartan` - start or continue the work. Follow its handoffs until the task is approved and complete.
2. `commit and push` - ask the agent to commit the approved change and push it to `main`, so automation can prepare a release. (`commit` alone means commit locally without pushing.)
3. `merge the release` - after the automated release proposal appears, ask the agent to inspect it and merge it when it is healthy.

That is the whole operator flow. The only release-specific decision is when to say `merge the release`; the agent and repository automation handle the rest.

The release proposal can remain open while more approved changes arrive. Automation refreshes it. Documentation-only or maintenance-only work may wait for a later releasable change.

When asked to merge, the agent checks the proposal before acting and reports the resulting release URL afterward. The operator does not calculate a release number or edit release files during the normal path.

## Technical reference

### Semantic Versioning and commit signals

The package version is `MAJOR.MINOR.PATCH`:

- `fix:` produces a patch release.
- `feat:` produces a minor release.
- `feat!:` or a `BREAKING CHANGE:` footer produces a major release.
- `docs:` and `chore:` remain valid history but do not normally cause a release by themselves.

Scopes are optional, so `feat(protocol): ...` is valid. Release Please reads these Conventional Commit signals; the operator never edits a version directly in the normal flow.

### Automated release mechanics

`.github/workflows/release-please.yml` runs repository CI after a change reaches `main`. It uses the repository's `GITHUB_TOKEN` and the manifest-mode configuration in `release-please-config.json` plus `.release-please-manifest.json`. Repository settings must allow GitHub Actions to write contents and pull requests; this is a one-time setup condition, not a per-release step.

The releasable package root is `agent-skill/`. A release proposal updates:

- `agent-skill/CHANGELOG.md` with generated release notes;
- `agent-skill/version.txt` with the package version required by the `simple` release strategy;
- `agent-skill/.claude-plugin/plugin.json` through a JSON `extra-files` rule;
- `agent-skill/assets/task-template.md` through a generic `extra-files` marker, so future tasks receive the released version as their passive `protocol` birth-stamp;
- `.release-please-manifest.json` with the released package version.

When the release proposal is squash-merged with its generated Conventional Commit title, the next workflow run creates tag `vMAJOR.MINOR.PATCH` and the matching GitHub Release. It does not publish a runtime artifact, call another agent, or modify an adopting repository. Existing task files retain the birth-stamp they received at creation.

### Manual fallback

Use this only when repository CI cannot complete the release. The operator explicitly asks an agent to prepare one manual release; each external action still needs operator authorization.

1. Determine the next version from Conventional Commits since the latest `vMAJOR.MINOR.PATCH` tag, using the mapping above.
2. Prepare one release-only pull request that updates `agent-skill/CHANGELOG.md`, `agent-skill/version.txt`, `agent-skill/.claude-plugin/plugin.json`, `agent-skill/assets/task-template.md`, and `.release-please-manifest.json` to the same version. Title it `chore(main): release spartan MAJOR.MINOR.PATCH`.
3. After that pull request is reviewed and merged, tag its merge commit as `vMAJOR.MINOR.PATCH` and create the GitHub Release from the matching changelog section.
4. Restore or repair the repository workflow separately; do not add release scripts or runtime behavior to `agent-skill/`.

The fallback must continue to pass the deletion test: never rewrite existing `spartan/tasks/` files, never hide or delete their public history, and never add version negotiation, network checks, a database, a queue, or executable release state to the skill. If `agent-skill/` is deleted, every task artifact must remain understandable and manually continuable.
