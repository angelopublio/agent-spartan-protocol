# Agent Spartan Protocol

> Spartan coordinates information, not processes.

Agent Spartan Protocol is a portable, human-mediated handoff protocol for software work performed across authenticated coding-agent hosts. It uses one repository-local Markdown task artifact to preserve context and one concise prompt to transfer the next bounded action.

The protocol is intentionally not an orchestrator. It does not launch models, manage credentials, keep a daemon alive, retry work, monitor sessions, coordinate concurrency, or adopt patches automatically.

## Why it exists

Coding agents already know how to inspect repositories, edit files, run checks, and review diffs. The missing piece is a small, durable contract that lets a human move work between hosts without copying the full conversation or building an execution platform.

Spartan provides that contract:

1. The current host reads or creates `spartan/tasks/NNNN-<slug>.md`.
2. It performs one useful, bounded round.
3. It records decisions, work, concise evidence, blockers, and the next action.
4. It returns one short English handoff referencing the task file.
5. The human chooses and starts the next host.

## Authorities

- Repository files, the current diff, and check results are the truth of implementation.
- The task Markdown is the truth of handoff.
- Chat history is disposable context.
- `spartan/tasks/` remains public and version-controlled as living proof that the protocol can carry real work without hidden state.

Phase and status fields describe the current snapshot. They are not an executable state machine. The task file is not an event log, database, audit ledger, or transcript.

## Package

The portable skill lives in [`agent-skill/`](agent-skill/):

```text
agent-skill/
|- .claude-plugin/
|  `- plugin.json
|- CHANGELOG.md
|- version.txt
|- SKILL.md
|- agents/
|  `- openai.yaml
|- references/
|  |- protocol.md
|  `- routing.md
`- assets/
   `- task-template.md
```

`agents/openai.yaml` is optional Codex interface metadata. The behavior contract remains in portable Markdown shared by all hosts.

The plugin manifest carries the package's passive Semantic Version. It does not add runtime behavior to the skill.

Project-local discovery uses symbolic links to the same source folder:

```text
.agents/skills/spartan -> ../../agent-skill
.claude/skills/spartan -> ../../agent-skill
```

Codex uses the `.agents` link, Claude Code uses the `.claude` link, and compatible hosts may use the same canonical source. These links install no runtime and create no divergent skill copies.

## Install on macOS

Keep this repository at a stable absolute path. The recommended personal installation creates one symbolic link for Codex and one for Claude Code; both links resolve to the same canonical `agent-skill/` folder.

Set the repository path for the current terminal session, create the personal skill directories, and create the links. If this repository is stored elsewhere, replace the `SPARTAN_REPO` value before running the commands:

```bash
SPARTAN_REPO="/Users/angelopublio/Documents/DEV/agent-spartan-protocol"

mkdir -p "$HOME/.agents/skills"
mkdir -p "$HOME/.claude/skills"

ln -s "$SPARTAN_REPO/agent-skill" "$HOME/.agents/skills/spartan"
ln -s "$SPARTAN_REPO/agent-skill" "$HOME/.claude/skills/spartan"
```

Do not add `-f` to `ln`. If either destination already exists, inspect it before changing anything:

```bash
ls -ld "$HOME/.agents/skills/spartan" "$HOME/.claude/skills/spartan"
```

Verify that both hosts can resolve the canonical skill:

```bash
test -f "$HOME/.agents/skills/spartan/SKILL.md" && echo "Codex skill link resolves"
test -f "$HOME/.claude/skills/spartan/SKILL.md" && echo "Claude Code skill link resolves"
```

Open a new Codex or Claude Code session after installation so the host refreshes skill discovery. The personal locations make Spartan available in every project for the current macOS user:

- Codex: `$HOME/.agents/skills/spartan`
- Claude Code: `$HOME/.claude/skills/spartan`

Do not copy the skill into both locations. The symbolic links preserve one source of truth. Moving this repository later will break the links; recreate them with the new absolute path.

### Project-local alternative

Use a project-local installation only when Spartan should be discoverable in one project rather than for the whole user account. From the target project root:

```bash
SPARTAN_REPO="/Users/angelopublio/Documents/DEV/agent-spartan-protocol"

mkdir -p .agents/skills .claude/skills
ln -s "$SPARTAN_REPO/agent-skill" .agents/skills/spartan
ln -s "$SPARTAN_REPO/agent-skill" .claude/skills/spartan
```

Absolute project-local links are machine-specific and normally should not be committed. Avoid installing the same skill both personally and project-locally unless you are deliberately testing discovery behavior.

## Use Spartan inside a project

Start the coding host with the target project as its working folder. Spartan stores continuation state in that target project under `spartan/tasks/`; it does not store the target project's task state in this protocol repository. The `spartan/README.md` file explains this durable task namespace.

In Codex, mention the skill explicitly with `$spartan`:

```text
Use `$spartan`.

Implement a small, reversible change that fixes the empty-state message and updates the relevant regression test.
```

In Claude Code, invoke the project command with `/spartan` and provide the task:

```text
/spartan

Review the current implementation for the acceptance criteria recorded in the active Spartan task.
```

To continue an existing task in either host, reference its exact artifact instead of repeating its history:

```text
Open `spartan/tasks/NNNN-<slug>.md`.

Act as the next role recorded in the task, perform its one bounded next action, update the same task file, and return only the next handoff or a completion notice.
```

The human must start every new host session and manually transfer every handoff. Spartan never opens or invokes another platform.

## Releases

The installable package uses Semantic Versioning, starting at `0.1.0`. Release automation lives in repository CI, outside `agent-skill/`; it prepares the changelog and package metadata, then creates the tag and GitHub Release after its release pull request is merged. See [`RELEASING.md`](RELEASING.md) for the operator flow.

The `protocol` value copied into a newly created task is only a birth-stamp. Spartan performs no version negotiation or network check, and package releases never rewrite existing tasks or version an adopting repository.

## Non-goals

This project does not provide:

- cross-provider model invocation;
- API-key or subscription management;
- workers, daemons, schedulers, polling, or crash recovery;
- queues, locks, concurrency control, or worktree allocation;
- automatic retries or correction loops;
- secret scanners, sandboxes, or policy engines;
- event ledgers, projections, patch digests, or transactional adoption;
- external actions initiated automatically by Spartan, such as commit, push, pull request, merge, or deployment. Repository release CI is separate from the portable protocol and its installable package.

If a requirement needs those guarantees, it belongs in a separate runtime or workbench rather than this protocol.

## Portability test

The decisive test is deletion:

> If the skill is removed, do the task files remain understandable, and can a human continue the work manually?

If the answer is no, the feature has crossed from protocol into runtime.

## Current status

Version `0.1.0` is the validated static-package baseline. The bootstrap received explicit approval from a fresh Codex verifier and an independent Claude Code reviewer; see the completed [`spartan/tasks/0001-bootstrap-v0-1.md`](spartan/tasks/0001-bootstrap-v0-1.md).

The next phase is real-project dogfooding: one small reversible task completed in one host, one material change with fresh-context review, and one high-impact plan or review handed manually across vendors. Do not add protocol features until repeated trial evidence justifies them.

See [`docs/DOGFOODING.md`](docs/DOGFOODING.md) for the sequence and exit criteria.
