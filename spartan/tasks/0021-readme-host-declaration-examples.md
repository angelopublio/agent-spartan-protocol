---
protocol: "0.4.0" # x-release-please-version
id: readme-host-declaration-examples
created_at: 2026-08-03
status: completed
phase: complete
task_type: implementation
risk: routine
current_role: independent-reviewer
next_role: human-operator
updated_at: 2026-08-03
---

# Show what a repository host declaration looks like

## Objective

An adopting repository learns from `spartan/README.md` that its Spartan host-role preferences
belong in repository instructions and learns from root `README.md` what a human-declared,
non-executing `## Agent hosts` preference looks like, in a form that copying cannot turn into a
worse routing configuration than the package default.

## Context

The package routing reference maintains a default host-role mapping and the current model list, and
`agent-skill/SKILL.md` requires each round to read applicable repository instructions. Adopters
still need an explicit example showing that they may declare a repository-specific preference in
those instructions without granting Spartan execution authority.

A completed task with the requested slug already exists as task `0020`. Its merged implementation
added the host-preference sentence to `agent-skill/assets/spartan-readme.md` and synchronized
`spartan/README.md`. Before that synchronization, the repository copy had drifted from the asset by
missing task `0019`'s paragraph explaining point-in-time material under `design/`; task `0020`
restored it. This task was created with the next unused creation number because the owner explicitly
requested a new uniquely numbered artifact, and initially reused task `0020`'s slug. It was renamed
to `readme-host-declaration-examples` because two artifacts sharing one slug and one `id` are
indistinguishable in a handoff, and because the slug described task `0020`'s work rather than this
one's: `0020` states in the distributed asset where a host preference belongs, while this task shows
in root `README.md` what such a declaration looks like. The creation number stays `0021`; only the
descriptive half of the filename changed.

A later review round found that the example first shipped by this task assigned `planner`,
`reviewer`, and `independent-reviewer` to one host and `investigator`, `implementer`, and `verifier`
to the other. That mapping makes each host review its own output - the planning host reviews its own
plan, the implementing host verifies its own implementation - which `references/routing.md` rejects
as correlated blind spots and which inverts the deliberate cross-vendor split the same reference
describes. An adopter copying it would land below the package default.

The root cause is structural rather than editorial: a flat role-to-host table cannot express "the
reviewer is whoever did not produce the work" when only two hosts exist, because the same host both
plans one round and implements another. Two hosts therefore require a relational reviewer entry;
three hosts admit a fixed assignment. The corrected section shows both cases for that reason, rather
than showing one arrangement twice.

## Scope

- Confirm the host-preference sentence remains in `agent-skill/assets/spartan-readme.md`.
- Add a short, pasteable `## Agent hosts` example immediately after the first-task `AGENTS.md`
  advisory in root `README.md`.
- Confirm `spartan/README.md` matches the asset and includes task `0019`'s `design/` paragraph.
- State the no-self-review rule in the surrounding prose, and show one two-host and one three-host
  example that both satisfy it.
- Keep every path that appears inside a pasteable block resolvable in the repository that copies it.
- Record implementation, review, and verification evidence in this task artifact.

## Out of Scope

- Changes to `agent-skill/SKILL.md`, `agent-skill/references/protocol.md`, or
  `agent-skill/references/routing.md`.
- A second maintained model list, new controlled value, frontmatter field, role, enforcement
  mechanism, or execution authority.
- Commit, push, pull request, release, or another external action.

## Constraints

- Persisted repository content remains English.
- `agent-skill/` remains ASCII-only.
- Repository host-role mappings are human-declared routing preferences only.
- No example may show a host reviewing or verifying work it produced.
- A caveat about the example belongs in the surrounding prose, never inside the block an adopter
  copies, where the adopter's own values are real rather than illustrative.
- A pasteable block must contain no path that resolves only in this repository.
- The maintained model list stays solely in `agent-skill/references/routing.md`.
- The distributed asset and this repository's `spartan/README.md` remain byte-identical.

## Acceptance Criteria

- [x] `agent-skill/assets/spartan-readme.md` states that repository-specific host-role preferences
      belong in repository instructions such as `AGENTS.md`, not under `spartan/`.
- [x] Root `README.md` shows a short, pasteable `## Agent hosts` section immediately after the
      first-task `AGENTS.md` advisory.
- [x] The example maps roles to hosts, identifies the human as the preference authority, grants no
      execution authority, and points to `agent-skill/references/routing.md` as the maintained
      model list without maintaining a second list.
- [x] The prose states that a reviewer must not be the host that produced the work under review.
- [x] Root `README.md` shows one two-host and one three-host example, and neither assigns a review
      or verification role to a host that produces work in the same declaration.
- [x] No pasteable block contains a path that resolves only in this repository, and no block
      contains a caveat that applies to the example rather than to the adopter's declaration.
- [x] A fresh-context review in a host other than the one that drafted the correction issues an
      explicit verdict.
- [x] `spartan/README.md` contains task `0019`'s `design/` paragraph and is byte-identical to the
      distributed asset.
- [x] `agent-skill/SKILL.md`, `agent-skill/references/protocol.md`, and
      `agent-skill/references/routing.md` are unchanged, and `agent-skill/` remains ASCII-only.
- [x] Relevant repository checks pass with their outcomes recorded below.

## Decisions

- Keep host choices, not concrete model names, in the pasteable example. This demonstrates the
  requested role-to-host preference while leaving the aging model list solely in `routing.md`.
- Use only existing controlled role names in the example, so the adopter sees a preference over
  Spartan's current vocabulary rather than a new role or field.
- State both outside and inside the example that it is advisory and human-declared, so copying the
  section cannot reasonably be read as delegating execution authority.
- Preserve the already-correct asset and repository copy from task `0020` rather than creating
  meaningless textual churn; verify their required content and byte identity instead.
- Ship two examples, separated by host count rather than by style. The two-host case is the one a
  flat table cannot express correctly, so showing only the three-host case would hide the constraint
  that produced the original defect. Showing only the two-host case would leave the simpler
  arrangement undemonstrated.
- Use a relational reviewer entry - "whichever host did not produce the work under review" - in the
  two-host example instead of inventing artifact-specific role names such as "reviewer of a plan".
  Sub-roles are not in the controlled role vocabulary, and an example is the worst place to teach
  vocabulary the protocol does not have.
- State the no-self-review rule once, in prose above both examples, rather than repeating it inside
  each block. The blocks stay short enough to copy, and the rule governs the reader's own mapping
  rather than the examples.
- Keep the model reference in the prose rather than inside either block. That removes the path that
  would not resolve in an adopting repository and the "illustrative only" caveat that would be false
  once an adopter substitutes real values.

## Work Completed

- Created this task from `agent-skill/assets/task-template.md` using creation number `0021`.
- Renamed the artifact from `0021-host-preference-belongs-in-repo-instructions.md` to
  `0021-readme-host-declaration-examples.md` and updated the frontmatter `id` and the title to
  match, resolving the duplicate slug shared with task `0020`.
- Added the pasteable `## Agent hosts` example to root `README.md`.
- Confirmed task `0020` had already added the requested host-preference sentence to the distributed
  asset and synchronized the local copy, including the `design/` paragraph that had drifted after
  task `0019`; no redundant edit was made to either already-correct file.
- Implementation round executed in Codex as implementer.
- Review round executed in Claude Code (Claude Opus 5) as reviewer: found that the shipped example
  taught self-review, carried a path that does not resolve in an adopting repository, and placed an
  example-scoped caveat inside the copied block.
- Replaced the single bullet-list example with the no-self-review rule in prose plus one two-host and
  one three-host table example, at the owner's explicit request in the same round.
- Independent review executed in a fresh Codex session, separate from the Claude Code round that
  drafted the correction; the exact model identifier was not exposed to the reviewer.

## Evidence

- `cmp -s agent-skill/assets/spartan-readme.md spartan/README.md`: passed; the distributed asset and
  repository copy are byte-identical.
- Root `README.md` inspection: the pasteable example immediately follows the first-task advisory,
  maps only existing Spartan roles to hosts, names the human as preference authority, denies
  execution authority, contains no concrete model name, and points to
  `agent-skill/references/routing.md` as the maintained model list.
- Focused `rg` assertions: passed; the asset contains the host-preference sentence exactly once,
  and the repository copy contains task `0019`'s `design/` paragraph.
- `git diff --exit-code -- agent-skill/SKILL.md agent-skill/references/protocol.md
  agent-skill/references/routing.md`: passed; all three protected files are unchanged.
- `LC_ALL=C rg --pcre2 -l '[^\x00-\x7F]' agent-skill`: no matches; `agent-skill/` is ASCII-only.
- Template-placeholder and unique-`0021` assertions: passed; the task has no retained placeholder,
  and exactly one task filename uses creation number `0021`.
- `git diff --check`: passed.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both passed.

Independent review round (Codex, fresh context, read-only with respect to root `README.md`):

- Direct inspection of both pasteable blocks: the two-host declaration assigns `planner` to Claude
  Code and `implementer` to Codex, then assigns all review and verification roles to whichever host
  did not produce the work under review. The relation is stated completely in the copied table and
  requires no task history to interpret.
- Direct inspection of the three-host declaration: Claude Code plans, Cursor implements, and Codex
  reviews or verifies, so no declared producer checks its own work.
- `agent-skill/references/protocol.md:75` compared with both role columns: every named role is in the
  controlled vocabulary (`planner`, `implementer`, `reviewer`, `independent-reviewer`, `verifier`).
- Focused scan of both blocks: no repository-local path, link, model name, example-only caveat, or
  unresolved placeholder appears. The remaining statements are true after copying: unlisted roles
  use Spartan's package default, the mapping is advisory, and the human starts every round.
- `cmp -s agent-skill/assets/spartan-readme.md spartan/README.md`: passed; the distributed asset and
  repository copy remain byte-identical.
- Focused `rg` assertions: passed; the asset retains the repository-instructions sentence, the
  local copy retains the point-in-time `design/` paragraph, both expected reviewer mappings are
  present, exactly six controlled-role table rows appear, and the pasteable blocks contain none of
  the nonportable terms checked above.
- `git diff --exit-code -- agent-skill/SKILL.md agent-skill/references/protocol.md
  agent-skill/references/routing.md`: passed; all protected files remain unchanged.
- `LC_ALL=C rg --pcre2 -l '[^\x00-\x7F]' agent-skill`: no matches; `agent-skill/` remains ASCII-only.
- Task-artifact assertions: exactly one `0021-*.md` exists and no template placeholder remains.
- `git diff --check`: passed.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both passed.
- Root `README.md` SHA-256 before and after this review remained
  `7c53083cae548e6e1bc84ae588c60dbb28b80559b3b8a9da4a359426ee1afa60`; the reviewer did not modify
  it.
- `git status --short`: the implementation and correction rounds changed root `README.md` and added
  this task artifact; the independent-review round changed only this artifact. Unrelated pre-existing
  untracked `.DS_Store` files remain untouched.

Review and correction round (Claude Code, Claude Opus 5):

- `agent-skill/references/routing.md:86` versus the shipped example: the example assigned `planner`
  and `reviewer` to the same host and `implementer` and `verifier` to the other, contradicting the
  rule that a reviewer should differ from the host that produced the work (finding 1).
- `agent-skill/references/routing.md:55`: states the intended split - one vendor plans and the other
  reviews the plan, one implements and the other reviews the code - which the shipped example
  inverted (finding 1, intent).
- The shipped block instructed the adopter to use `agent-skill/references/routing.md`; that
  directory exists only in this repository, so the path was dead in any repository that copied the
  block (finding 2).
- The shipped block carried "any model value added here is illustrative only", a statement about
  this README's example that becomes false inside an adopter's own declaration (finding 3).
- `cmp -s agent-skill/assets/spartan-readme.md spartan/README.md`: passed; the asset and repository
  copy remain byte-identical after the correction.
- `git diff --exit-code -- agent-skill/`: passed; the correction touched no packaged file.
- Scan of the corrected section for model names: no match inside either example; the single model
  reference sits in the surrounding prose.
- `test -f agent-skill/references/routing.md`: passed; the prose link resolves for a reader of this
  repository, which is the only context in which it now appears.
- `git diff --check`: passed.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both passed.

## Review

Verdict: APPROVE

Findings:

- No unresolved finding. In the two-host block, “whichever host did not produce the work under
  review” is a complete, understandable routing rule and prevents `reviewer`,
  `independent-reviewer`, and `verifier` from checking the producer's own work. In the three-host
  block, the fixed Codex reviewer is distinct from both declared producers.
- Every role named in either block belongs to the controlled role vocabulary in
  `agent-skill/references/protocol.md`.
- Both blocks are portable as copied: they contain no repository-local path, link, model reference,
  unresolved placeholder, or statement that becomes false in an adopting repository.
- The previous reviewer findings remain resolved. The no-self-review rule is now embodied by both
  mappings, while the repository-local routing-reference link and example-scoped caveat remain
  outside the pasteable blocks.

Independence note: this verdict was issued in a fresh Codex session, a different host and vendor
from the Claude Code round that drafted the correction. Root `README.md` was read-only throughout.

## Blockers

None.

## Next Action

None for this task. The corrected examples are independently approved and all acceptance criteria
are satisfied. The change remains uncommitted; committing it is the owner's explicit choice.
