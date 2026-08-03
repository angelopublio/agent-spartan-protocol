---
protocol: "0.4.0" # x-release-please-version
id: invocation-binding-by-host-capability
created_at: 2026-08-03
status: completed
phase: complete
task_type: implementation
risk: material
current_role: independent-reviewer
next_role: human-operator
updated_at: 2026-08-03
---

# Bind the handoff invocation to host capability rather than host name

## Objective

A generated handoff can recommend any host that discovers `SKILL.md`, including Cursor, with an
invocation that matches what the host actually supports, and the routing reference carries Cursor as
a host with a concrete model so the "Model and effort" line can be satisfied.

## Context

Task `0014` bound the invocation token to the recommended host after a real handoff paired Claude
Code with Codex's `$spartan`. Its rule enumerated two hosts by name and routed everything else to the
direct prompt: `$spartan` for Codex, `/spartan` for Claude Code, direct prompt for a host without
skill support.

That enumeration is now false for Cursor. Cursor 3.10 resolves skills from `.agents/skills` and
`.cursor/skills` at both user and project scope without any third-party setting, and from
`.claude/skills` and `.codex/skills` when that setting is enabled; the owner ran a Spartan round in
Cursor against a separate repository through the existing `.agents/skills/spartan` link. Cursor
therefore has skill support and, by elimination, was being routed to the branch reserved for hosts
that have none.

Task `0021` made the gap visible from the other side: root `README.md` now ships a three-host
example naming Cursor as `implementer`, while the only mention of Cursor inside `agent-skill/` was
the `SKILL.md` frontmatter description. The repository documented a host its own routing reference
did not recognise.

The defect is the same class task `0014` fixed, from the opposite direction. `0014` corrected a
wrong pairing; this corrects an incomplete enumeration. Both produce a handoff whose invocation line
does not match the host it names.

## Scope

- `agent-skill/references/routing.md`: a Cursor row in the host table, a Cursor row in the model
  table, and the paragraph explaining how a third host interacts with the two-host cross-vendor
  alternation and with review independence.
- The six locations that state the invocation binding, changed as one unit:
  `agent-skill/SKILL.md` (two), `agent-skill/references/protocol.md` (two),
  `agent-skill/references/routing.md` (one), `agent-skill/assets/task-template.md` (one).

## Out of Scope

- Any host beyond Cursor, including Grok Build, which is named in the `SKILL.md` description but has
  no verified skill-discovery behaviour.
- A new controlled value, frontmatter field, role, verdict, or enforcement mechanism.
- Re-verifying the July 2026 model rows for Codex and Claude Code.
- Changes to root `README.md`, whose examples task `0021` settled and a fresh Codex session
  approved.
- Commit, push, pull request, release, or another external action.

## Constraints

- Persisted repository content remains English and `agent-skill/` remains ASCII-only.
- The six invocation-binding locations must continue to state one rule. Changing a subset is what
  produced the defect task `0014` fixed.
- `agent-skill/SKILL.md` stays below 200 lines.
- The rule must not require a package change for every future host.
- Routing stays advisory: naming Cursor grants it no execution authority.

## Acceptance Criteria

- [x] Every location that states the invocation binding distinguishes three cases: a host with a
      skill token, a host that discovers skills without exposing one, and a host with no skill
      support.
- [x] All six locations state the same rule, and none routes a skill-capable host to the direct
      prompt merely because it is not named.
- [x] The prohibition from task `0014` on pairing a host with another host's token survives intact.
- [x] `agent-skill/references/routing.md` lists Cursor in the host table and gives it a concrete
      model, so a Cursor-bound handoff can satisfy the "Model and effort" line.
- [x] The reference states a convention for a host that exposes no effort control, so the "Model and
      effort" line is satisfiable without inventing a level the host cannot accept.
- [x] The reference states review independence as a comparison between the model vendor of the
      producing round and the model vendor of the reviewing round, usable for a model from any
      vendor.
- [x] The model reference no longer claims one verification date for rows checked at different
      times.
- [x] `agent-skill/` remains ASCII-only, `SKILL.md` stays below 200 lines, and both plugin manifests
      validate.
- [x] A fresh-context review in a host other than Claude Code issues an explicit verdict.

## Decisions

**Enumerate by capability, not by host name.** Naming hosts one at a time is what created this
defect: every new host would need a package change, and until it arrived the host would silently
fall into the "no skill support" branch. The rule now asks what the host supports - a token, skill
discovery without a token, or neither - and names Codex, Claude Code, and Cursor as examples of those
cases. A future skill-capable host is classified correctly without touching the package.

**Change all six locations together.** The invocation rule is one rule mirrored in four files. Task
`0014` exists because those copies disagreed. Updating a subset would reproduce the original defect
while appearing to fix it, so the six were treated as a single edit.

**Cursor enters as an optional third host, not as a replacement in the default mapping.** The
existing two-host alternation - Claude plans, Codex reviews the plan; Codex implements, Claude
reviews the code - stays as written. The added paragraph describes what changes when a third host
takes implementation, so a repository without Cursor reads the reference unchanged.

**State the vendor-multiplexing caveat in the routing reference rather than leaving it to the
adopter.** Cursor runs models from several vendors, so unlike Codex and Claude Code its vendor is
not fixed by the host name. The cross-vendor independence preference depends on that fact, which
makes it a property of the package rather than something each adopting repository should have to
rediscover. The form that caveat takes is settled below.

**Date rows, not the table.** The first attempt left the "verified July 2026" heading unchanged,
reasoning that advancing it would claim a verification that did not happen. That reasoning was
sound about advancing the date and wrong about the consequence: leaving it made the heading assert
July verification for a row added in August, so it traded one inaccuracy for another instead of
avoiding both. The heading now carries no date, the two verification moments are stated separately,
and the reference tells a future round to date its own row. A table-wide date is wrong by
construction whenever a single row is added.

**State a convention for hosts without an effort control instead of inventing a level.** The handoff
contract requires one concrete effort level, which silently assumes every host exposes one. Cursor's
Composer does not: Cursor's model documentation presents effort calibration as a property of the
model rather than a user setting, and lists selectable effort variants only for other models. The
reference therefore names the absence explicitly - `Composer, no user-selectable effort` - because a
level the host cannot accept is as unusable to the human as the generic phrase the contract already
forbids.

**Express independence as a vendor comparison, not as a property of Cursor.** The prior draft's
host/model-specific test left some producer-reviewer combinations unclassified. Independence is
pairwise: compare the model vendor of the producing round with the model vendor of the reviewing
round. That form covers a Cursor round on a Claude model, which correlates with Claude Code while
remaining independent of Codex, and any future vendor without another package change - the same
classify-by-property discipline this task applied to the invocation binding.

## Work Completed

- Added Cursor to the host table and the model table in `agent-skill/references/routing.md`, with
  `Composer` as its model, and added the paragraph covering third-host routing and the dependence of
  review independence on the model Cursor runs.
- Rewrote the invocation binding in all six locations to classify hosts by capability, preserving
  the task `0014` prohibition on cross-host tokens and adding an explicit prohibition on routing a
  skill-capable host to the direct prompt for absence from the list.
- Implementation round executed in Claude Code (Claude Opus 5) as implementer.
- Independent review executed in Codex (GPT-5 family; exact tier was not exposed to the round). The
  reviewer kept `agent-skill/` read-only and returned `CHANGES` for two routing gaps; the six-location
  invocation binding itself passed review.
- Fix round executed in Claude Code (Claude Opus 5): resolved both review findings and, separately,
  the table-wide verification date the review did not raise. The six invocation-binding locations
  were left untouched, since the review approved them and reopening them would reintroduce the drift
  risk the change discipline exists to prevent.
- Final independent re-review executed in Codex (GPT-5 family; exact tier was not exposed to the
  round), kept `agent-skill/` read-only, and returned `APPROVE`.

## Evidence

- Cursor 3.10.20 bundle, `extensions/cursor-agent-exec/dist/main.js`: the user-scope and
  project-scope skill directory resolvers always include `.agents/skills` and `.cursor/skills`, and
  include `.claude/skills` and `.codex/skills` only when the third-party flag is set. This is the
  basis for treating Cursor as skill-capable.
- Owner report: a Spartan round ran successfully in Cursor against
  `/Users/angelopublio/Documents/DEV/astro/angelopublio-astro` with no protocol change, through the
  pre-existing `~/.agents/skills/spartan` link.
- `grep -rc "discovers skills" agent-skill/ --include="*.md"`: six matches across
  `SKILL.md` (2), `references/protocol.md` (2), `references/routing.md` (1), and
  `assets/task-template.md` (1); every binding location carries the capability clause.
- Focused scan for the superseded two-host enumeration: the only remaining occurrence is the new
  third-category sentence in `SKILL.md`, which states that a host without skill support receives the
  direct prompt.
- `LC_ALL=C grep -rn '[^\x00-\x7F]' agent-skill/`: no matches; the package is ASCII-only.
- `wc -l < agent-skill/SKILL.md`: 83 lines, within the 200-line limit.
- `git diff --check`: passed.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both passed.
- Independent-review focused scan found exactly the six declared invocation-binding locations and
  confirmed one capability rule in all six: own token when exposed, skill named in the prompt when
  discovery exists without a token, and direct prompt only without skill support. The explicit
  cross-host-token prohibition remains in `SKILL.md` and the normative protocol clause; the latter
  also forbids defaulting an unlisted skill-capable host to the direct prompt.
- Independent-review checks (2026-08-03): full `agent-skill/` ASCII scan returned no matches;
  `wc -l agent-skill/SKILL.md` reported 83 lines; `git diff --check` passed; and
  `claude plugin validate --strict .` plus `claude plugin validate --strict agent-skill` both passed.
Fix round (Claude Code, Claude Opus 5):

- Cursor model documentation, `cursor.com/docs/models` and `cursor.com/docs/models/cursor-composer-2-5`:
  Composer 2.5 exposes no user-selectable reasoning effort, thinking level, or effort tier. Effort
  calibration is described as a property of the model ("better effort calibration"), while
  selectable effort appears only for other models (a fixed medium level for Grok 4.5 on the Start
  plan, `gpt-5-high` as a GPT-5 reasoning-effort variant). The TypeScript SDK exposes per-model
  `model.params`, which is a different surface from the editor the human drives. Owner confirmed the
  editor shows no effort control.
- `grep -c "no user-selectable effort" agent-skill/references/routing.md`: one match; the convention
  is stated once, in the model reference.
- `grep -c "Compare the model vendor of the producing round" agent-skill/references/routing.md`: one
  match; independence is stated as a pairwise vendor comparison.
- `grep -n "^### Current model reference" agent-skill/references/routing.md`: the heading carries no
  date, so it no longer asserts one verification moment for rows checked at different times.
- `git diff --stat -- agent-skill/`: four files, sixteen insertions and eight deletions; the fix
  round touched only `references/routing.md`, leaving the six approved binding locations unchanged.
- `LC_ALL=C grep -rn '[^\x00-\x7F]' agent-skill/`: no matches. `git diff --check`: passed.
- `claude plugin validate --strict agent-skill`: passed.

Final independent re-review (Codex, GPT-5 family; exact tier not exposed):

- `agent-skill/references/routing.md` supplies the complete Cursor handoff value
  `Composer, no user-selectable effort`; no effort value has to be invented.
- The independence rule compares the producing and reviewing rounds' model vendors, explicitly
  classifies matching vendors as correlated and differing vendors as independent, and states that
  the comparison applies to a model from an unnamed vendor.
- The model-reference heading has no date. The reference records July 2026 for the Codex and Claude
  Code rows, August 2026 for the Cursor row, and instructs future updates to date each row's own last
  check; no live sentence assigns one verification moment to the table as a whole.
- Focused package-and-artifact scan found no live assertion of the superseded independence rule.
  The task's stale historical wording was compacted so the rejected formulation is not retained as
  current handoff truth.
- The six invocation-binding locations remain the text approved in the prior review:
  `SKILL.md` (2), `references/protocol.md` (2), `references/routing.md` (1), and
  `assets/task-template.md` (1). They still express the same capability-based three-case rule.
- `LC_ALL=C rg --pcre2 -n '[^\x00-\x7F]' agent-skill`: no matches; `agent-skill/` remains
  ASCII-only. `wc -l agent-skill/SKILL.md`: 83 lines. `git diff --check`: passed.
- `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both
  passed. SHA-256 checks before and after the review matched for all four changed `agent-skill/`
  files, confirming the review was read-only with respect to the package.

- Implementation/fix working-tree scope from `git status --short`: those rounds changed
  `agent-skill/SKILL.md`,
  `agent-skill/references/protocol.md`, `agent-skill/references/routing.md`,
  `agent-skill/assets/task-template.md`, and added this artifact. Root `README.md` and the task `0021`
  artifact remain modified from earlier rounds; unrelated untracked `.DS_Store` files are untouched.

## Review

Verdict: APPROVE

Findings:

- The two prior review findings are resolved. Cursor has a complete, non-invented model/effort
  value, and independence is a pairwise comparison of the producing and reviewing model vendors
  that works for unnamed vendors.
- The separate verification-date correction is sound: there is no table-wide verification date,
  distinct row groups retain their actual dates, and the reference directs future maintainers to
  record each row's own last check.
- No live package or task-artifact sentence retains the rejected independence rule. Historical
  context now describes only that a narrower prior test was replaced by the accepted pairwise rule.
- The six invocation-binding locations remain unchanged from their previously approved wording and
  continue to state one capability-based rule.
- All relevant checks pass, the review remained read-only with respect to `agent-skill/`, and no
  finding requires changes.

## Blockers

None.

## Next Action

None. Review returned `APPROVE`, every acceptance criterion and relevant check is satisfied, and no
blocker remains. Commit or another external action remains the owner's explicit choice.

## Completion Notice

Completed: the capability-based invocation binding and Cursor routing guidance are independently
`APPROVE`. The two prior review findings and the separate verification-date correction are resolved;
the six previously approved invocation-binding locations remain unchanged; ASCII, size, diff
hygiene, and both strict plugin validations pass. No follow-up work is identifiable from this task.
