---
protocol: "0.4.0" # x-release-please-version
id: host-preference-belongs-in-repo-instructions
created_at: 2026-08-03
status: completed
phase: complete
task_type: implementation
risk: routine
current_role: implementer
next_role: human-operator
updated_at: 2026-08-03
---

# Put repository host-role preferences in repository instructions

## Objective

An adopting repository's `spartan/README.md` states that preferences assigning particular Spartan
roles to particular hosts belong in the repository's own instructions file, such as `AGENTS.md`,
and this repository's `spartan/README.md` matches the distributed asset.

## Context

The routing reference provides a package-level default mapping from Spartan roles to hosts, and
`agent-skill/SKILL.md` requires every round to read the target repository's applicable instructions.
Neither currently tells adopters that a repository may express a different host-role preference or
where to record it.

Task `0013` established `agent-skill/assets/spartan-readme.md` as the distribution source and made
this repository's `spartan/README.md` byte-identical to it. Task `0019` later added the `design/`
explanation to the asset only. The repository copy drifted because that follow-up did not synchronize
the asset change back into the local copy, even though this repository dogfoods the distributed
README and the two copies are expected to track each other.

## Scope

- Add one sentence to `agent-skill/assets/spartan-readme.md` placing repository-specific host-role
  preferences in the repository's own instructions file, such as `AGENTS.md`, not under `spartan/`.
- Synchronize `spartan/README.md` with the asset, including the `design/` paragraph added by task
  `0019`.
- Record implementation and verification evidence in this task artifact.

## Out of Scope

- Any change to `agent-skill/SKILL.md` or the routing and protocol references.
- A new controlled value, frontmatter field, role, enforcement mechanism, or host-selection process.
- Commit, push, pull request, release, or other external action.

## Constraints

- All persisted content is English.
- `agent-skill/` remains ASCII-only.
- The host preference is advisory repository documentation, not protocol state under `spartan/`.
- The two README copies remain byte-identical.

## Acceptance Criteria

- [x] `agent-skill/assets/spartan-readme.md` contains exactly one added sentence stating where a
      repository-specific host-role preference belongs and that it does not belong under `spartan/`.
- [x] `spartan/README.md` contains that host-preference sentence and task `0019`'s `design/`
      paragraph.
- [x] `spartan/README.md` and `agent-skill/assets/spartan-readme.md` are byte-identical.
- [x] `agent-skill/SKILL.md` is unchanged and `agent-skill/` is ASCII-only.
- [x] Relevant repository checks pass with outcomes recorded below.

## Decisions

- Keep the adopter-facing detail in the distributed README asset, following task `0019`, rather
  than expanding `SKILL.md`; tasks `0005`, `0017`, and `0019` establish the same detail-placement
  precedent.
- Name `AGENTS.md` as an example rather than a required filename because the applicable repository
  instructions remain host- and repository-dependent.
- Restore byte identity between the asset and repository copy because task `0013` established the
  asset as the copy shipped to adopters and this repository's copy as its dogfooded instance.

## Work Completed

- Added the repository-specific host-role preference sentence to the distributed README asset.
- Synchronized this repository's README with the asset, restoring task `0019`'s missing `design/`
  paragraph and the asset's ASCII title punctuation.
- Implementation round executed in Codex as implementer.

## Evidence

- `cmp -s agent-skill/assets/spartan-readme.md spartan/README.md`: passed; the distribution asset
  and repository copy are byte-identical.
- Focused `rg` assertions: passed; the host-preference sentence occurs exactly once in the asset,
  and the repository copy contains task `0019`'s `design/` paragraph.
- `git diff --exit-code -- agent-skill/SKILL.md`: passed; `SKILL.md` is unchanged.
- `LC_ALL=C rg --pcre2 -n '[^\x00-\x7F]' agent-skill`: no matches; `agent-skill/` is ASCII-only.
- `git diff --check`: passed.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both passed.
- `git status --short`: only the two intended README files and this new task are changed by this
  round; unrelated pre-existing untracked `.DS_Store` files remain untouched.

## Review

Verdict: PENDING

Findings:

- Routine documentation-only change; no review round has been required or run.
- Implementer verification found no unresolved issue; routing permits a routine, narrow,
  reversible change to complete in one round, so the verdict remains `PENDING` because no review
  round was required or performed.

## Blockers

None.

## Next Action

None. The adopting-repository guidance and synchronized README copy satisfy all acceptance criteria;
commit or other external action remains the owner's explicit choice.

## Next Handoff

None. Completion notice delivered; no follow-up work is identifiable from this task.
