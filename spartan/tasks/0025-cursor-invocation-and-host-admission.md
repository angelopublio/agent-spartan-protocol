---
protocol: "0.6.0" # x-release-please-version
id: cursor-invocation-and-host-admission
created_at: 2026-08-12
status: completed
phase: complete
task_type: implementation
risk: material
current_role: reviewer
next_role: none
updated_at: 2026-08-12
handoff_id: HX-002
next_handoff_id: none
---

# Correct Cursor invocation and generalize host admission

## Objective

The maintained package states Cursor's invocation as `/spartan`, admits any host beyond the default
two by a rule written without host names, makes the effort recommendation conditional on what the
chosen model exposes, and scopes the model table to package defaults while handling a
repository-declared model beside it.

## Context

Two facts changed under the package since task `0022` shipped its Cursor guidance in August 2026.
Cursor now exposes an explicit skill token, so the package's statement that Cursor is the example of
a host that discovers skills without exposing a token is false. And the Cursor host row entered the
routing default mapping as an "optional third host", which reads as a package-level default even for
a repository that never mentions Cursor.

Task `0014` bound the invocation token to the host by enumerating hosts by name; task `0022` had to
correct that enumeration when Cursor appeared and rebound it to capability. The host-admission rule
carries the same defect today: it is written about Cursor rather than about any host beyond the two
defaults. This round writes the rule generically so the next new host needs no correction round.

This is a planning round. It edits no file under `agent-skill/`. Its only deliverable is this
artifact, and it must be sufficient on its own for a fresh-context implementation round.

## Scope

- `spartan/tasks/0025-cursor-invocation-and-host-admission.md` (this artifact) is the only file this
  round writes.
- The implementation round it plans touches exactly three package files:
  - `agent-skill/skills/spartan/SKILL.md`
  - `agent-skill/skills/spartan/references/protocol.md`
  - `agent-skill/skills/spartan/references/routing.md`
- `agent-skill/skills/spartan/assets/task-template.md` was inspected and needs no edit; see
  "Already stated - plan no edit".

## Out of Scope

- Subagents, worktrees, concurrency, and any coordination mechanism the hard boundary already
  forbids.
- Merge or patch adoption.
- Historical completed task artifacts under `spartan/tasks/`. They record what was true when written
  and are not retrofitted.
- Root `README.md`. It was read this round and carries no live statement that the corrected package
  would contradict: it documents `/spartan:spartan` for Claude Code and `$spartan` for Codex, names
  Cursor only as an agent the skills CLI detects and as the `implementer` in a three-host
  *repository declaration* example, and states no Cursor invocation style. If the implementation
  round finds such a contradiction, correcting that one statement is in scope; nothing else in
  `README.md` is.
- Any host beyond those already named. In particular, recommending Grok Build as a host stays out
  (see Decisions).
- New controlled values: no new frontmatter field, role, verdict, phase, status, or handoff-contract
  clause.

### Observation, not scope: the `SKILL.md` description frontmatter

`agent-skill/skills/spartan/SKILL.md` frontmatter still reads:

> ...work that may move between authenticated coding-agent hosts such as Codex, Claude Code, Grok
> Build, or Cursor...

The package recommends no Grok host anywhere, and this round adds no such recommendation. That field
drives skill triggering in every host that discovers `SKILL.md`, so changing it can change whether
the skill fires at all. It deserves its own round with its own verification, not a side edit here.

**Proposed follow-up task:** revise the `SKILL.md` `description` frontmatter so its example hosts
match what the package actually recommends, verifying in each host that discovery and triggering are
unaffected.

## Constraints

- English-only persisted content (`AGENTS.md`, Language).
- Change discipline prefers concise Markdown; do not add a parallel statement beside an existing one
  that already says the thing. Rework the existing sentence instead.
- `agent-skill/skills/spartan/SKILL.md` stays preferably below 200 lines (`AGENTS.md`); it is 87
  lines today.
- Routing stays advisory. Naming or admitting a host grants it no execution authority.
- No per-token prices anywhere in the package.
- No commit, push, or release action unless the owner asks for it in that round.

## Verified facts (do not re-verify)

Each fact below was checked on **2026-08-12** against the source named. The implementation round
should treat these as settled input.

1. **Cursor invokes a discovered skill with an explicit slash token.** `cursor.com/docs/skills`
   (fetched 2026-08-12): "Skills can also be manually invoked by typing `/` in Agent chat and
   searching for the skill name." The same page describes `disable-model-invocation: true` as making
   a skill "behave like a traditional slash command, where it is only included in context when you
   explicitly type `/skill-name` in chat" - that field governs *automatic* use, not whether the token
   exists. The skill folder is named `spartan`, so the token is `/spartan`.
2. **Cursor's skill discovery paths still cover this repository's symlinks.** Same page: skills load
   from `.agents/skills/`, `.cursor/skills/`, `~/.agents/skills/`, `~/.cursor/skills/`, and
   additionally from `.claude/skills/`, `.codex/skills/`, `~/.claude/skills/`, `~/.codex/skills/`.
3. **The maintained Composer row should name Composer 2.5.** `cursor.com/docs/models` (fetched
   2026-08-12) lists the current Composer as **Composer 2.5**, "Cursor's own model, trained to be
   highly capable for agentic coding"; `cursor.com/docs/models/cursor-composer-2-5` calls it
   "Cursor's own agentic model".
4. **Composer 2.5 documents a Fast variant, not an effort control.**
   `cursor.com/docs/models/cursor-composer-2-5` (fetched 2026-08-12): "A **faster variant** with the
   same intelligence is also available... Fast is the default in the product." The page documents no
   effort or reasoning-level selector either way.
5. **Owner observation (not documentation):** Composer 2.5 exposes **no effort selector in the
   Cursor product UI** - only a Fast toggle. Current Cursor documentation states this neither way, so
   the package must attribute it to the owner, not to a doc page. Recorded by the owner, 2026-08-12.
6. **Grok 4.5 effort levels, default, and plan gating.** `cursor.com/docs/models/grok-4-5` (fetched
   2026-08-12): three levels, "high (the default), medium, and low". "Effort switching and Fast mode
   are available on Pro and higher plans." On the Start plan (India only) "Grok 4.5 is fixed at
   medium effort in non-fast mode"; `cursor.com/docs/models` states the same gating as needing an
   upgrade to Pro or higher to "choose effort levels and Fast mode".
7. **Grok 4.5's documented developer.** `cursor.com/docs/models/grok-4-5` (fetched 2026-08-12)
   describes it as "a joint model from Cursor and SpaceXAI"; `cursor.com/docs/models` phrases the
   same as "jointly trained by Cursor and SpaceXAI". Its vendor attribution is therefore joint, not
   a single vendor, which is exactly why the round must record the attribution rather than infer it
   from the host.

## Already stated - plan no edit

Each sentence below already exists in the maintained package and covers something this round would
otherwise be tempted to add. The implementation round must not restate any of them.

- A round reads the target repository's instructions - `SKILL.md`, "Run one round":
  > 1. Resolve the target repository. Read its applicable instructions before changing anything.
- The same, normatively - `references/protocol.md`, "Round lifecycle":
  > 1. Resolve the target repository and read its applicable instructions.
- Repository host-role preference lives in the consumer's own instructions -
  `assets/spartan-readme.md`:
  > When a repository prefers particular hosts for particular Spartan roles, that preference belongs
  > in the repository's own instructions file, such as `AGENTS.md`, not under `spartan/`.
- Every round records the model it actually ran - `references/routing.md`:
  > Every round MUST record the model it actually ran, which is what lets a later reviewer make this
  > comparison for any model, including one from a vendor not named here.
- And the host-and-model outcome note - `references/protocol.md`:
  > When the human runs a round, that round SHOULD record, in its own outcome note (Work Completed or
  > the review line), the host and model it actually used - even when they differ from the
  > recommendation.
- Independence is a vendor comparison, not a host-name comparison - `references/routing.md`:
  > Independence is a property of model vendors, not of host names.
  > ...
  > Composer is Cursor's own model, so it is a third vendor beside Anthropic and OpenAI
  (This paragraph is version-free and stays as written; do not rewrite "Composer" to "Composer 2.5"
  here, or the sentence acquires a version to maintain.)
- Model choice follows assessed risk - `references/routing.md`:
  > Match model tier to the task's assessed risk, not to the host's most capable model
- Host-internal behavior is already outside the protocol - `references/protocol.md`:
  > Native host capabilities MAY be recommended as optional human choices. Spartan MUST NOT invoke,
  > monitor, resume, normalize, or depend on them. Internal host behavior is outside the protocol;
  > Spartan does not treat it as portable functionality.
- The effort-less case is already reconciled with the handoff contract's "one concrete effort level"
  MUST, so `references/protocol.md`'s handoff-contract clause needs no edit -
  `references/routing.md`:
  > because a level the host cannot accept is as unusable to the human as the generic phrase the
  > contract already forbids
- `assets/task-template.md`'s `Invocation:` placeholder is capability-generic and names no host, so
  it is already correct under the corrected rule and gets no edit:
  > - Invocation: <the recommended host's own convention: `$spartan` for Codex, `/spartan` for Claude
  >   Code, the skill named in the prompt for a host that discovers skills without exposing a token,
  >   direct prompt otherwise>, passing the prompt block below as the argument
  The identical placeholder inside the `SKILL.md` and `references/protocol.md` shape blocks is
  unchanged for the same reason.

## Planned changes

### 1. Invocation truth: Cursor takes `/spartan`

**Files:** `SKILL.md`, `references/protocol.md`, `references/routing.md`. All three change in one
commit; a partial change leaves the package self-contradicting.

**Sentences to change, quoted exactly.**

`agent-skill/skills/spartan/SKILL.md`, "Produce the handoff":

> The invocation must match what the recommended host actually supports, not its name. Use the
> host's own skill token when it has one: `$spartan` for Codex, `/spartan` for Claude Code
> (`/spartan:spartan` when installed as a plugin). When the host discovers skills but exposes no
> token, name the skill in the prompt instead, as Cursor does. Only a host without skill support gets
> the direct prompt. Never pair a host with another host's token.

`agent-skill/skills/spartan/references/protocol.md`, "Handoff contract":

> The invocation style MUST match what the recommended host supports, determined by capability rather
> than by host name. A host that exposes a skill token receives that token: `$spartan` for Codex,
> `/spartan` for Claude Code (`/spartan:spartan` when installed under a plugin namespace). A host
> that discovers skills from `SKILL.md` but exposes no invocation token receives the prompt with the
> skill named in it, which is how Cursor is invoked. Only a host without skill support receives the
> direct prompt. A handoff MUST NOT pair a recommended host with another host's invocation token, and
> MUST NOT route a skill-capable host to the direct prompt merely because it is not named here.

`agent-skill/skills/spartan/references/routing.md`, final paragraph:

> The handoff is delivered as two blocks: a "Recommended execution" block stating the recommended
> host with a one-phrase reason, one concrete model and effort level, and the invocation style
> matched to what that host supports (`$spartan` for Codex, `/spartan` for Claude Code, the skill
> named in the prompt for a host such as Cursor that discovers skills without exposing a token, or a
> direct prompt for a host without skill support); then a clean prompt block naming the next role and
> action. The human decides the actual host, model, and invocation, and starts the round manually.

**Wording direction.** In each of the three, move Cursor from the token-less branch into the token
list - `/spartan` for Cursor, alongside `$spartan` for Codex and `/spartan` for Claude Code
(`/spartan:spartan` under a plugin namespace). Keep the token-less branch as a branch, stated by
capability with **no named example**: a host that discovers skills from `SKILL.md` but exposes no
invocation token receives the prompt with the skill named in it. The rule is bound to capability, not
to host name, so the branch remains correct with nothing currently sitting in it. Keep every other
clause of these sentences, including the two prohibitions in the protocol clause. Change no fenced
shape block.

**Reviewer confirmation.**
- `grep -rn "as Cursor does\|how Cursor is invoked\|a host such as Cursor" agent-skill/` returns
  nothing.
- Each of the three files gives Cursor `/spartan` in its token list.
- Each of `SKILL.md` and `references/protocol.md` still carries one sentence describing the
  token-less branch, and that sentence names no host.
- The three fenced `Invocation:` placeholders (`SKILL.md`, `references/protocol.md`,
  `assets/task-template.md`) are byte-identical to their current text.

### 2. Generalized host admission in `references/routing.md`

**File:** `agent-skill/skills/spartan/references/routing.md`, "Choose the next host".

**Sentences to change, quoted exactly.**

Host-table row:

> | Cursor | Optional third host: implementation (coding) and testing, which frees Codex for review |

Paragraph below the mapping:

> Cursor is optional and belongs to a repository that has it available. When it takes implementation,
> the producing and reviewing hosts separate without the two-host alternation above: Claude Code
> plans, Cursor implements, and Codex reviews or verifies both.

**Wording direction.** Rework both in place; add no third statement beside them.

- The default-mapping table keeps exactly two rows, Claude Code and Codex. The Cursor row is removed
  from it, because a table headed "Default responsibilities" cannot hold a host the package does not
  recommend by default.
- The paragraph becomes the generic admission rule, written without host names in its operative
  clauses: the default mapping covers two hosts because alternating producer and reviewer across
  their vendors is the protocol's built-in independence mechanism; a host beyond those two enters a
  round only when the target repository's own instructions name it for that role, or when the human
  explicitly chooses it for the current round; the round records which of the two applied. Keep the
  useful consequence the old paragraph carried, generalized: when an admitted host takes
  implementation, producer and reviewer separate without the two-host alternation - one host plans,
  the admitted host implements, and the third reviews or verifies both.
- Do not restate that the round reads the repository's instructions, or that the preference lives in
  the consumer's `AGENTS.md`; both are already stated (see "Already stated").

**Reviewer confirmation.**
- The default-mapping table in `references/routing.md` has exactly two data rows.
- `grep -n "belongs to a repository that has it available\|Optional third host" agent-skill/` returns
  nothing.
- The admission paragraph's operative clauses name no host, and it states the two admission paths
  plus the requirement to record which applied.
- Reading `references/routing.md` against `/Users/angelopublio/Documents/DEV/astro/angelopublio-astro`
  (no host preference declared) yields no Cursor recommendation; reading it against
  `/Users/angelopublio/Documents/DEV/vercel/na5-orbit` (declares `| implementer | Cursor |`) admits
  Cursor for `implementer` by the declaration path.

### 3. Effort conditional on what the model exposes

**File:** `agent-skill/skills/spartan/references/routing.md`, "Current model reference".

**Sentence to change, quoted exactly.**

> Not every host exposes an effort control. When it does not, the "Model and effort" line states that
> instead of naming a level, because a level the host cannot accept is as unusable to the human as
> the generic phrase the contract already forbids. Composer is the current case: it calibrates its
> own effort and offers no user-selectable level, so a Cursor round records
> `Composer, no user-selectable effort`.

**Wording direction.** Rewrite this one paragraph so the property belongs to the **model**, not the
host, and so it states all four rules:

1. Where the chosen model exposes selectable effort, the round names one level matched to the
   assessed risk and actually selectable on the human's plan.
2. Where it exposes none, the line says so instead of naming a level - keeping the existing
   justification clause verbatim, since it is the reason the handoff contract tolerates this.
3. Where it exposes none, the assessed risk then governs the host and role choice instead: a
   `routine` round may stay in the effort-less model, while `material` or `high-impact` reasoning is
   a reason to route to a model that does expose effort.
4. A speed or cost toggle is never turned into an effort recommendation.

Keep Composer as the worked example, updated: a Cursor round on it records
`Composer 2.5, no user-selectable effort`. Attribute the no-selector fact to the owner's observation
of the product UI, not to documentation (verified fact 5). Do not describe Composer as "calibrating
its own effort"; the Composer 2.5 documentation does not say so.

**Reviewer confirmation.**
- The paragraph states the exposes/does-not-expose split, the risk-governs-routing consequence, and
  the toggle prohibition, in one place.
- `grep -n "Composer 2.5, no user-selectable effort" agent-skill/skills/spartan/references/routing.md`
  matches.
- `grep -rn "calibrates its own" agent-skill/` returns nothing.
- No sentence anywhere converts Fast mode, a speed toggle, or a cost tier into an effort level.

### 4. Model table scoped to package defaults, declared models handled beside it

**File:** `agent-skill/skills/spartan/references/routing.md`, "Current model reference".

**Sentences to change, quoted exactly.**

Table preamble:

> Use this table to fill the "Model and effort" line with a concrete name. It ages; verify against the
> human's plan and update this section when vendors ship new models. The Codex and Claude Code rows
> were last verified in July 2026; the Cursor row was added in August 2026. Date each row's own last
> check rather than restating one date for the table, so adding a row never backdates it.

Cursor model row:

> | Cursor | Composer | Implementation and testing rounds; Cursor's own model, so a third vendor
> beside Anthropic and OpenAI. No user-selectable effort: it calibrates its own (added August 2026) |

**Wording direction.**

- **Preamble.** Change only the Cursor clause, from "the Cursor row was added in August 2026" to the
  row having been last verified in August 2026. Leave "The Codex and Claude Code rows were last
  verified in July 2026" exactly as written - do not backdate it, do not restate it, do not promote
  one date to the whole table. Keep the per-row dating instruction.
- **Cursor row.** Stays a single Composer row. Name it **Composer 2.5**. Keep "Cursor's own model, so
  a third vendor beside Anthropic and OpenAI" and keep the statement that it exposes **no
  user-selectable effort**, now attributed to the owner's observation of the product UI rather than
  to documentation. Carry the row's own date (verified August 2026). No price, no token rate, no
  Fast-variant recommendation.
- **New text beside the table** - the one genuinely new passage this round adds, because nothing in
  the package covers it. Short, and it must state:
  - The table lists what the package recommends **by default**, not everything a host can run.
  - When a repository's instructions declare a specific model, the round honors it under the same
    admission rule as change 2, and names an effort level if that model exposes one.
  - The round records the model, its **vendor attribution**, and the **path** it ran through. Do not
    restate the existing "Every round MUST record the model it actually ran" sentence; the gap this
    fills is the attribution and the path, not the model name.
  - Why the path matters: the same model name can carry a different vendor attribution inside a host
    than it does directly, so an independence comparison uses what the round recorded, never the host
    name.
  - **Grok 4.5 as declaration-only reference data**, not a recommended row and not a table row:
    effort levels low, medium, and high with **high** the documented default; a `routine` round names
    medium and a `material` or `high-impact` round names high; effort switching and Fast mode need a
    Pro or higher plan, and the Start plan fixes it at medium in non-fast mode, so the named level
    must be selectable on the human's plan; documented as a joint model from Cursor and SpaceXAI, so
    its vendor attribution is joint rather than a single vendor (all verified August 2026).

**Reviewer confirmation.**
- The model table contains exactly one Cursor row, naming Composer 2.5, carrying "no user-selectable
  effort" and its own August 2026 date.
- `grep -n "July 2026" agent-skill/skills/spartan/references/routing.md` still shows the Codex and
  Claude Code verification clause unchanged.
- `grep -n "Grok 4.5" agent-skill/skills/spartan/references/routing.md` matches only prose lines; no
  matching line begins with `|`.
- The Grok 4.5 reference states levels, default, plan gating, the risk mapping, and the joint vendor
  attribution.
- `grep -rnE '\$[0-9]|/M input|/M output|per[- ]token' agent-skill/` returns nothing.
- The passage says the table is the package's default recommendation, not an exhaustive list, and
  requires recording the vendor attribution and the path without restating the model-recording
  requirement.

## Acceptance Criteria

Checkable against the repository, not against intent.

- [x] **No default Cursor recommendation.** `references/routing.md`'s default-mapping table has
      exactly two data rows (Claude Code, Codex). Applying it to
      `/Users/angelopublio/Documents/DEV/astro/angelopublio-astro`, which declares no host
      preference, and to this repository's own `AGENTS.md`, which declares none either, yields no
      Cursor recommendation.
- [x] **Declared Cursor resolves to a complete line.** Applying it to
      `/Users/angelopublio/Documents/DEV/vercel/na5-orbit`, whose `AGENTS.md` declares
      `| implementer | Cursor |`, yields the "Model and effort" value
      `Composer 2.5, no user-selectable effort`; that exact string appears in
      `references/routing.md`.
- [x] **Declared Grok 4.5 resolves to a level.** The reference data beside the model table states
      medium effort for a `routine` round and high for `material` and above, names high as the
      documented default, and notes the Pro-or-higher gating on effort selection.
- [x] **Every maintained invocation statement agrees.** All three locations that enumerate tokens
      (`SKILL.md`, `references/protocol.md`, `references/routing.md`) give Cursor `/spartan`; no file
      under `agent-skill/` places Cursor in the token-less branch; the token-less branch still exists
      in `SKILL.md` and `references/protocol.md` and names no host.
- [x] **Generic admission rule.** The admission paragraph's operative clauses contain no host name,
      state both admission paths (repository instructions name the host; the human chooses it for the
      round), and require the round to record which applied.
- [x] **`SKILL.md` size.** `wc -l agent-skill/skills/spartan/SKILL.md` reports fewer than 200 lines.
- [x] **No contract growth.** `git diff` adds no frontmatter field, no role, no verdict, no phase or
      status value, and no handoff-contract clause. The controlled-value lists in
      `references/protocol.md` and the frontmatter in `assets/task-template.md` are unchanged.
- [x] **No prices.** `grep -rnE '\$[0-9]|/M input|/M output|per[- ]token' agent-skill/` returns
      nothing.
- [x] **Package still validates.** `claude plugin validate --strict .` and
      `claude plugin validate --strict agent-skill` both pass, and `git diff --check` passes.
- [x] **Root `README.md`** is unchanged, unless a live statement there contradicts the corrected
      package, in which case only that statement changed.

## Decisions

Settled this round and closed to reopening.

- **Host-internal execution topology is outside the protocol**, including Cursor's Agent versus
  Multitask choice. Multitask is decomposition into subagents, which the hard boundary already
  forbids Spartan to coordinate; and even advisory guidance would make the protocol opine on a host's
  internal execution, which `references/protocol.md` already places outside it ("Internal host
  behavior is outside the protocol"). A consumer that wants to fix a mode states it in its own
  `AGENTS.md`, by the same precedent that put host-role preference there (task `0020`).
- **No `Cursor mode` line is added to the advisory execution block.** It follows from the decision
  above, and the block's fields are part of the handoff contract, which this round does not grow.
- **Speed and cost toggles carry no protocol recommendation**, including Composer's Fast variant.
  They are the human's choice about latency and spend, not a reasoning-quality control, and turning
  one into an effort recommendation would state something the package cannot support.
- **Grok 4.5 is not a package default for any host.** It appears only as reference data for the case
  where a repository declares it. The package recommends a default per host; a model the package has
  not adopted as a default does not become one by being documented.
- **Recommending Grok Build as a host stays out of scope.** A repository declaring the Grok 4.5
  *model* is not a verification of that *host's* skill discovery, which task `0022` explicitly left
  open ("Any host beyond Cursor, including Grok Build, which is named in the `SKILL.md` description
  but has no verified skill-discovery behaviour").
- **The three fenced `Invocation:` placeholders are not edited.** They are capability-generic and
  name no host, so they are already true under the corrected rule; editing them would grow three
  blocks to say what the normative sentence beside each already says.
- **`references/protocol.md`'s "one concrete effort or reasoning level" MUST is not edited.** The
  routing reference already reconciles the effort-less case with it, and "no user-selectable effort"
  is a concrete statement rather than the generic phrase that clause forbids.

## Work Completed

- Planning round run in Claude Code (Claude Opus 5), 2026-08-12. No file under `agent-skill/` was
  modified; this artifact is the round's only deliverable.
- Read the maintained package in full: `SKILL.md`, `references/protocol.md`,
  `references/routing.md`, `assets/task-template.md`, `assets/spartan-readme.md`,
  `agents/openai.yaml`, plus root `README.md` and `AGENTS.md`.
- Verified the seven external facts recorded above against current Cursor documentation, and
  recorded the Composer 2.5 effort-selector absence as an owner observation because documentation
  states it neither way.
- Located and quoted every sentence the implementation round must change, so its scope is fixed
  without line numbers.
- Checked the two consumer repositories read-only as evidence for the admission criteria; neither
  was modified and no change inside them is planned.
- Implementation round run in Codex (GPT-5; exact product tier not exposed to the round),
  2026-08-12, as implementer under accepted handoff `HX-001`.
- Applied all four planned changes in one unit across `SKILL.md`, `references/protocol.md`, and
  `references/routing.md`; no other maintained package or root documentation file changed.
- Corrected Cursor to `/spartan` in all maintained token enumerations, removed it from the default
  host table, added the generic two-path admission rule, made effort guidance model-dependent, and
  scoped the model table while adding declaration-only Grok 4.5 reference data.
- Review round run in Claude Code (Claude Opus 5), 2026-08-12, as reviewer under accepted handoff
  `HX-002`, read-only: the diff was re-checked against the four Planned changes and every acceptance
  criterion was re-run independently rather than accepted from the implementer's record.

## Evidence

- `cursor.com/docs/skills` (2026-08-12): explicit invocation by typing `/` in Agent chat and
  selecting the skill; `disable-model-invocation` governs automatic use only; discovery paths include
  `.agents/skills/`, `.cursor/skills/`, `.claude/skills/`, `.codex/skills/` and their user-level
  forms.
- `cursor.com/docs/models` (2026-08-12): current Composer is Composer 2.5; Grok 4.5 listed as
  "jointly trained by Cursor and SpaceXAI", with effort selection and Fast mode requiring Pro or
  higher.
- `cursor.com/docs/models/cursor-composer-2-5` (2026-08-12): "Cursor's own agentic model"; a faster
  variant is documented and is the product default; no effort or reasoning selector documented either
  way.
- `cursor.com/docs/models/grok-4-5` (2026-08-12): "high (the default), medium, and low"; "Effort
  switching and Fast mode are available on Pro and higher plans"; Start plan (India only) fixed at
  medium in non-fast mode; "a joint model from Cursor and SpaceXAI".
- Owner observation (2026-08-12): the Cursor product UI shows no effort selector for Composer 2.5,
  only a Fast toggle.
- `wc -l agent-skill/skills/spartan/SKILL.md`: 87 lines, so the 200-line ceiling is not at risk.
- `grep -rn "Cursor" agent-skill/skills/spartan/`: ten maintained mentions - two in `SKILL.md` (the
  `description` frontmatter and the invocation sentence), one in `references/protocol.md` (the
  invocation clause), and seven in `references/routing.md` (the host-table row, the
  optional-third-host paragraph, the vendor paragraph, the model-reference preamble date clause, the
  effort paragraph, the Composer model row, and the two-block delivery paragraph).
- `grep -n "Agent hosts\|| Role |"
  /Users/angelopublio/Documents/DEV/astro/angelopublio-astro/AGENTS.md`: no host declaration; the
  file mentions Spartan only as handoff truth. Read-only.
- `grep -n -A8 "| Role |" /Users/angelopublio/Documents/DEV/vercel/na5-orbit/AGENTS.md`: declares
  `| planner | Claude Code |`, `| implementer | Cursor |`,
  `| reviewer, independent-reviewer, verifier | Codex |`, followed by "Roles not listed here follow
  the Spartan package default." Read-only.
- Root `README.md` read in full: documents `/spartan:spartan` (Claude Code) and `$spartan` (Codex),
  names Cursor as a skills-CLI-detected agent and as `implementer` in the three-host repository
  declaration example, and states no Cursor invocation style - so no live contradiction exists today.
- This repository's `AGENTS.md`: no host-role declaration, which is itself an instance of the
  no-preference case in the acceptance criteria.
- `awk` over the default host table: `2`; the only data rows are Claude Code and Codex. Applying the
  generic admission rule to the settled no-preference observations for this repository and
  `angelopublio-astro` yields no Cursor recommendation.
- Applying the implemented declaration path to the settled `na5-orbit` instruction
  `| implementer | Cursor |` resolves through the single Cursor model row to
  `Composer 2.5, no user-selectable effort`; focused `grep` matched that exact string.
- `grep -n "Grok 4.5" references/routing.md` matched only the declaration-only prose; an `awk`
  table-row check returned `0`. The prose records low/medium/high, high as the documented default,
  routine-to-medium and material-or-higher-to-high routing, Pro-or-higher effort switching, Start
  plan medium gating, and joint vendor attribution.
- Focused invocation checks: all three maintained token enumerations give Cursor `/spartan`;
  `grep -rn "as Cursor does\|how Cursor is invoked\|a host such as Cursor" agent-skill/` returned
  no matches. The token-less capability sentence occurs once each in `SKILL.md` and
  `references/protocol.md`, names no host, and the three fenced `Invocation:` placeholder lines have
  the same SHA-1 and are unchanged by `git diff`.
- Admission checks: the host table has exactly two rows; removed-phrase `grep` returned no matches;
  inspection of the replacement paragraph shows both admission paths, the record-which-path
  requirement, and no host name in its operative clauses.
- `wc -l agent-skill/skills/spartan/SKILL.md`: `87`, below the 200-line preference.
- `git diff -U0` and `git diff --exit-code -- agent-skill/skills/spartan/assets/task-template.md`:
  only the existing invocation clause changed in the protocol; controlled values, template
  frontmatter, roles, verdicts, phases, statuses, and fenced shape blocks are unchanged.
- `grep -rnE '\$[0-9]|/M input|/M output|per[- ]token' agent-skill/`: no matches.
- `claude plugin validate --strict .`, `claude plugin validate --strict agent-skill`, and
  `git diff --check`: all exited `0`.
- `git diff --exit-code -- README.md`: exited `0`; root `README.md` is unchanged.
- `git status --short`: only the three planned package files are modified; this task artifact
  remains the pre-existing untracked handoff file and was updated as required.

Reviewer re-verification (Claude Code, 2026-08-12), all run independently of the implementer's
record:

- `git diff --numstat -- agent-skill/`: `1/1` `SKILL.md`, `1/1` `references/protocol.md`, `9/6`
  `references/routing.md` - three files, and the protocol change is the single invocation clause,
  so no controlled value, role, verdict, or handoff-contract clause moved.
- `git diff --exit-code` over `assets/task-template.md`, `assets/spartan-readme.md`,
  `agents/openai.yaml`, and root `README.md`: all exited `0`.
- Removed-phrase sweep over `agent-skill/` for "as Cursor does", "how Cursor is invoked", "a host
  such as Cursor", "belongs to a repository that has it available", "Optional third host", and
  "calibrates its own": no matches.
- `awk` over the default host table: `2` data rows. `` grep -rn '`/spartan` for Cursor' ``: matches in
  all three maintained files. The token-less branch survives in all three, phrased "discovers skills
  from `SKILL.md` but exposes no invocation token" and naming no host.
- `grep -rn "direct prompt otherwise>" agent-skill/skills/spartan/`: `3` fenced placeholders present;
  `git diff -U0` contains `0` changed lines matching them, so the shape blocks are untouched.
- `grep -n "Grok 4.5" references/routing.md`: one prose line, `0` of which begin with `|`. The
  July 2026 Codex and Claude Code verification clause is intact and unrestated.
- `grep -rnE '\$[0-9]|/M input|/M output|per[- ]token' agent-skill/`: no matches.
  `LC_ALL=C grep -rnP '[^\x00-\x7F]'` over the skill folder: no matches.
- `wc -l agent-skill/skills/spartan/SKILL.md`: `87`. `git diff --check`: clean.
  `claude plugin validate --strict .` and `claude plugin validate --strict agent-skill`: both
  "Validation passed".

## Review

Verdict: APPROVE

Reviewed in Claude Code (Claude Opus 5) on 2026-08-12, read-only, against the Codex implementation
round - cross-vendor, so producer and reviewer vendors differ.

Findings:

- No blocking findings. Every checked acceptance criterion is supported by a re-run check, and the
  diff stays inside the three planned files with no scope drift.
- No package contradiction remains. The fenced `Invocation:` placeholders still enumerate Codex and
  Claude Code without naming Cursor, which is the settled decision rather than a defect: they are
  non-exhaustive convention examples, and the authoritative sentence beside each now gives Cursor
  `/spartan`.
- Root `README.md`'s three-host example, which assigns `implementer` to Cursor, reads correctly under
  the new rule as a repository declaration admitting a non-default host, so leaving it unchanged is
  right.
- The `SKILL.md` `description` frontmatter still names "Grok Build". That was recorded as an
  out-of-scope observation with its own proposed follow-up task and is not a finding against this
  change.

## Blockers

None.

## Open Questions

None. Every question this round encountered was decided and recorded above: whether the Cursor row
leaves the host table (yes), whether it stays in the model table (yes, as the package's default model
recommendation for a host admitted by declaration), whether the fenced `Invocation:` placeholders
change (no), whether `references/protocol.md`'s effort MUST changes (no), and whether root
`README.md` changes (no, absent a contradiction). The `SKILL.md` `description` frontmatter is not an
open question here; it is a recorded observation with its own proposed follow-up task.

## Next Action

None. Every acceptance criterion is satisfied with recorded evidence, the review verdict is
`APPROVE`, and no blocker remains. The three package files are modified in the working tree and
uncommitted; committing is the owner's explicit call, not a completion requirement.

## Next Handoff

No outstanding handoff.
