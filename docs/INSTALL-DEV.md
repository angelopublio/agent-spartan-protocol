# Development / fallback install (symlinks)

Use this path when working on the protocol itself, or when a host cannot use the marketplace or the skills CLI. Clone the repository to a stable location and create one symbolic link per host; both links resolve to the same canonical `agent-skill/` folder.

From the repository root:

```bash
SPARTAN_REPO="$(pwd)"

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

Open a new Codex or Claude Code session after installation so the host refreshes skill discovery. The personal locations make Spartan available in every project for the current user:

- Codex: `$HOME/.agents/skills/spartan`
- Claude Code: `$HOME/.claude/skills/spartan`

With a personal symlink install, the skill is invoked without a plugin namespace: `/spartan` in Claude Code and `$spartan` in Codex.

Do not copy the skill into both locations. The symbolic links preserve one source of truth. Moving the repository later will break the links; recreate them with the new absolute path.

## Project-local alternative

Use a project-local installation only when Spartan should be discoverable in one project rather than for the whole user account. From the target project root:

```bash
SPARTAN_REPO="/path/to/agent-spartan-protocol"

mkdir -p .agents/skills .claude/skills
ln -s "$SPARTAN_REPO/agent-skill" .agents/skills/spartan
ln -s "$SPARTAN_REPO/agent-skill" .claude/skills/spartan
```

Absolute project-local links are machine-specific and normally should not be committed. Avoid installing the same skill both personally and project-locally unless you are deliberately testing discovery behavior.
