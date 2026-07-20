# Dogfooding the Spartan Protocol

## Purpose

Dogfooding must prove that a fresh agent can continue useful work from the portable skill and one task Markdown without relying on prior chat history. It must not prove that Spartan can automate host execution.

## Bootstrap sequence

### Round 1: author in the current Codex task

Create the initial English documentation, portable skill, task template, and `spartan/tasks/0001-bootstrap-v0-1.md`. Validate static structure and language. This is bootstrap work, not retroactive dogfooding.

### Round 2: verify in a fresh Codex task

Open a new Codex task rooted at this repository. Invoke `$spartan` through the project-local `.agents/skills/spartan` link and reference `spartan/tasks/0001-bootstrap-v0-1.md`.

The verifier remains read-only with respect to the protocol package. It may update only `spartan/tasks/0001-bootstrap-v0-1.md` with evidence, its verdict, and the next handoff. It checks:

- English-only persisted artifacts;
- host-neutral instructions;
- one Markdown file as the only durable task state;
- one concise handoff;
- absence of runtime, dependencies, nested agents, or automatic external actions;
- skill structure and frontmatter validation.

This is the first real dogfood round because the agent starts with cold conversation context and uses the completed skill.

### Round 3: review in Claude Code

Only after the Codex verifier approves, the human opens Claude Code manually. The project-local `.claude/skills/spartan` link exposes the same canonical skill as `/spartan`; the human supplies the English handoff stored in the task file.

Claude acts as `independent-reviewer`, remains read-only, and looks for:

- assumptions that work only in Codex;
- ambiguous routing or completion rules;
- boundary leaks toward runtime behavior;
- task artifacts that require hidden conversation context.

Claude records `APPROVE`, `CHANGES`, or `BLOCKED` in the same task file and returns one next handoff. Claude must not invoke Codex.

### Round 4: amend only when required

If the verdict is `CHANGES`, the human opens a fresh Codex task with that handoff. Codex applies only the accepted findings, validates the package, updates the same task file, and returns it for another read-only review if necessary.

Do not create an automatic correction loop. The human decides whether each additional round is justified.

## First real-project trials

After bootstrap approval, use Spartan on at least three actual project tasks before adding protocol features:

1. one small, reversible implementation completed in one host;
2. one material change with fresh-context review;
3. one high-impact plan or review handed manually across vendors.

Record observed friction in each task artifact. Add a protocol feature only after a recurring failure is demonstrated; do not build for hypothetical automation needs.

## Success criteria

The pilot succeeds when:

- a fresh agent continues correctly using only repository state, the skill, and the task file;
- the same task file works in Codex and Claude Code;
- every round emits one short English handoff;
- the human initiates every host transfer;
- the task Markdown remains a current snapshot rather than a transcript;
- no runtime, daemon, nested LLM, queue, lock, credential logic, or background process appears;
- removing the skill would still leave enough information for manual continuation.

## Stop conditions

Stop and simplify if maintaining Spartan takes more attention than the target project, if handoffs repeatedly duplicate the task file, or if a proposed feature requires monitoring or executing another host. Use the host's native feature manually or treat the requirement as a separate runtime product.
