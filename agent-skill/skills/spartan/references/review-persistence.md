# Review persistence and handoff ownership

Normative examples for the ownership rules in `protocol.md`. Each row maps one input
situation to exactly one required prompt shape, artifact edit, and identifier
outcome. Decide from the artifact and the returned result only.

A round is **technically read-only** when its host, session, sandbox, or launcher
cannot write repository files, and **writable** otherwise. The distinction is not
the role name. These rows apply to `reviewer`, `independent-reviewer`, and
`verifier`.

## Read-only return value

A technically read-only review or verifier round is complete when it returns this
structured result and writes nothing:

- exactly one verdict: `APPROVED`, `CHANGES_REQUESTED`, or `BLOCKED`;
- findings and the evidence behind them;
- host, model, effort, and vendor attribution it actually ran.

It consumes the addressed identifier, applies compare-before-change, and reports a
mismatch instead of acting. It does not set `handoff_id`, does not set or clear
`next_handoff_id`, and does not touch `## Next Handoff`.

## Writable persistence

The next explicitly authorized writable round persists that result in one atomic
edit and advances the envelope once:

1. transcribe the verdict unchanged and the findings faithful in substance;
2. name the reviewing host, model, effort, and vendor;
3. mark the `## Review` entry as adopted, not authored;
4. set `handoff_id` to the identifier the read-only round consumed;
5. update `current_role`, `next_role`, `phase`, `status`, and `updated_at`;
6. either issue one new identifier with a fully regenerated envelope, or set
   `next_handoff_id: none` and clear the envelope.

`handoff_id` MUST already equal the consumed identifier before `next_handoff_id`
is set to `none`. Do not mint an identifier for the review being recorded.

A writable follow-up prompt names one authorization: **persist only**, **correct
only**, or **persist and correct** (persistence first, one advance).

## Prompt permission

The advisory block includes `- Permission: read-only | writable` and is
regenerated whole with Role, Handoff, and Permission.

A **read-only** prompt MUST NOT contain `update the same task file`, `record your
verdict`, `Return only the next handoff` as a write or envelope instruction, or
any build, format, generate, snapshot, fixture, or install step. It may name only
checks the repository proves read-only. A write failure from a check the round
could not run is not a product defect and is not grounds for `BLOCKED`.

A **writable** prompt keeps the requirement to update the same task file.

### Shipped read-only prompt

```text
Recommended execution (human decides):
- Host: Cursor (repository binds reviewer.plan to Cursor)
- Model and effort: claude-opus-5-thinking-high at high effort
- Role: reviewer
- Handoff: HX-004
- Permission: read-only
- Invocation: `/spartan`, passing the prompt block below as the argument
```

```text
Open `spartan/tasks/NNNN-slug.md` (handoff HX-004).

Act as reviewer. Review the plan as a whole. Return exactly one APPROVED, CHANGES_REQUESTED, or BLOCKED verdict with findings, evidence, and the host, model, effort, and vendor you actually ran. Change no repository file. Run only checks that write nothing. A write failure from a check you could not run is not a product defect.
```

## Conformance set

| ID | Input | Required outcome |
| --- | --- | --- |
| P-APPROVED | Plan review, read-only, returns `APPROVED`, consumed `HX-004`; artifact `handoff_id: HX-003`, `next_handoff_id: HX-004` | Reviewer writes nothing. Steady state: `handoff_id` still `HX-003`. Persist-only successor adopts `HX-004` into `handoff_id`, transcribes `APPROVED` as adopted, issues `HX-005` for `implementer` with `Permission: writable`. |
| P-CHANGES | Plan review, read-only, returns `CHANGES_REQUESTED`, consumed `HX-004`; same prior ids | Reviewer writes nothing. Persist-only successor adopts `HX-004`, transcribes `CHANGES_REQUESTED` as adopted, issues `HX-005` for `planner` with `Permission: writable`. |
| P-BLOCKED | Plan review, read-only, returns `BLOCKED`, consumed `HX-004`; same prior ids | Reviewer writes nothing. Persist-only successor adopts `HX-004`, transcribes `BLOCKED` as adopted, sets `status: blocked`, `next_role: human-operator`, `next_handoff_id: none`, clears the envelope. |
| I-APPROVED | Implementation review, read-only, returns `APPROVED`, consumed `HX-010`; artifact `handoff_id: HX-009`, `next_handoff_id: HX-010` | Reviewer writes nothing. Persist-only successor adopts `HX-010`, transcribes `APPROVED` as adopted, and either completes (`next_handoff_id: none`) or issues one new writable envelope. |
| I-CHANGES | Implementation review, read-only, returns `CHANGES_REQUESTED`, consumed `HX-010`; same prior ids | Reviewer writes nothing. Persist-and-correct successor first adopts `HX-010` and transcribes `CHANGES_REQUESTED` as adopted, then applies the named findings to product files, then issues **one** new identifier (`HX-011`) for the next review. Not two advances. |
| I-BLOCKED | Implementation review, read-only, returns `BLOCKED`, consumed `HX-010`; same prior ids | Reviewer writes nothing. Persist-only successor adopts `HX-010`, transcribes `BLOCKED` as adopted, sets `status: blocked`, `next_role: human-operator`, `next_handoff_id: none`, clears the envelope. |
| ADOPT | Returned result: `CHANGES_REQUESTED`, three findings, host Cursor, model `claude-opus-5-thinking-high`, effort high, vendor Anthropic, consumed `HX-004` | `## Review` reads `Verdict: CHANGES_REQUESTED`, findings faithful in substance, execution named, entry marked adopted. Verdict spelling unchanged. |
| VALIDATE | Returned result has no verdict, or names `HX-003` while artifact proposes `HX-004` | Do not persist. Record the discrepancy and stop. Do not rewrite a verdict. |
| STEADY | Read-only reviewer consumed `HX-013`; artifact still `handoff_id: HX-012`, `next_handoff_id: HX-013` | Not a stale paste. Documented gap between consumption and persistence. Writable successor adopts `HX-013` once. |
| AGREE | Any outstanding proposal after a writable regeneration | `next_role`, advisory `- Role:`, prompt `Act as <role>` agree; `next_handoff_id`, advisory `- Handoff:`, prompt `(handoff HX-NNN)` agree; advisory includes `- Permission:` as part of the whole block. |
| RO-CHECK | Read-only prompt that would have asked for a build or `npm test` that writes | Prompt names only read-only checks. An `EPERM` / `mkdtemp` write failure is not a product defect and is not `BLOCKED`. |
| PLACEHOLDER | Identified task; PENDING `## Review` section the current producer round is responsible for | Producer writes nothing in `## Review`; body remains the shipped placeholder — `Verdict: PENDING`, `Findings:`, and `- None recorded.` |
| LEGACY | Artifact `0001` or any identifier-less legacy task; pre-correction `## Review` written by the reviewer itself | No retrofit. Both verdict spellings remain valid historical record. No round rewrites or re-attributes an old `## Review`. |

No row is resolvable two ways. No row requires information outside the artifact and
the returned result.

## Reviewer checklist

- [ ] Every P-* and I-* row states the return value, the writable edit, and the
      resulting `handoff_id` / `next_handoff_id` pair.
- [ ] A technically read-only round can satisfy every rule addressed to it without
      writing a file.
- [ ] Adopted entries name execution and read as adopted.
- [ ] Advancement is once per writable round; identifiers are never skipped or
      reused; the high-water mark is not lowered.
- [ ] Four-way role and identifier agreement plus `- Permission:` hold on every
      outstanding proposal.
- [ ] The shipped read-only prompt contains none of the forbidden write lines.
