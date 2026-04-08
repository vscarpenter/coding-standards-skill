# Coding Standards Skill for Claude Code

Comprehensive code standards and agentic behavior guidelines, packaged as a Claude Code skill with companion slash commands.

## What's Included

```
.claude/
  skills/
    coding-standards/
      SKILL.md            # The skill (auto-loaded by Claude Code)
  commands/
    qspec.md              # /qspec — generate a feature spec
    qcheck.md             # /qcheck — skeptical staff engineer review
coding-standards.md       # Standalone reference copy (same content, no frontmatter)
```

## Install

### Option A: Copy into your project (recommended for teams)

Copy the `.claude/` directory into your repo root and commit it. Every team member using Claude Code on this repo will automatically get the skill and commands.

```bash
# From your project root
cp -r /path/to/CodingStandards/.claude .claude

# Commit so the whole team gets it
git add .claude/
git commit -m "chore: add coding-standards skill and slash commands"
```

### Option B: Install globally (for personal use across all projects)

Copy into your user-level Claude config so the skill applies to every project.

```bash
# Skill
mkdir -p ~/.claude/skills/coding-standards
cp .claude/skills/coding-standards/SKILL.md ~/.claude/skills/coding-standards/SKILL.md

# Slash commands
mkdir -p ~/.claude/commands
cp .claude/commands/qspec.md ~/.claude/commands/qspec.md
cp .claude/commands/qcheck.md ~/.claude/commands/qcheck.md
```

## Usage

### The Skill (automatic)

Once installed, the coding-standards skill triggers automatically whenever Claude Code is doing development work. There is nothing to invoke — Claude loads the skill and follows its guidelines for every coding task.

The skill covers:
- **Agentic behavior** — codebase orientation, spec-driven development, verification-first workflow, session handoff, self-improvement loop, context management
- **Code quality** — naming, types, structure, performance, accessibility, logging, dependency management
- **Testing & errors** — TDD, test isolation, typed error handling
- **Security** — input validation, parameterized queries, least privilege
- **Git workflow** — conventional commits, branch naming, PR standards, code review norms
- **Architecture** — ADRs for significant decisions
- **Task management** — todo tracking, Definition of Done checklist, lessons learned
- **Prompt engineering** — prompt structure, patterns, anti-patterns
- **Claude Code tooling** — subagents, custom agents, hooks, skills, slash commands

### Slash Commands (on demand)

**`/qspec`** — Generate a spec for a feature before writing any code.

```
> /qspec
```

Claude will produce a structured spec with goal, inputs/outputs, constraints, edge cases, out of scope, and acceptance criteria. It saves to `tasks/spec.md` and waits for your approval before implementing.

**`/qcheck`** — Run a skeptical staff engineer review on all changed files.

```
> /qcheck
```

Claude reviews the changeset against the full coding standards, checking tests, error handling, types, observability, security, and the Definition of Done. Returns a structured list of blocking issues vs. suggestions.

## Customizing

### Add project-specific rules

Add a `CLAUDE.md` to your project root for rules that are specific to your codebase. The skill's Self-Improvement Loop will prompt Claude to update `CLAUDE.md` after every correction, so it grows organically over time.

### Configure hooks

The skill recommends three hooks. Add these to your `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npx biome format --write $CLAUDE_FILE_PATH || true"
          }
        ]
      }
    ],
    "PostCompact": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "cat tasks/todo.md tasks/lessons.md 2>/dev/null || echo 'No task files found.'"
          }
        ]
      }
    ]
  }
}
```

Replace `npx biome format` with your project's formatter (`prettier`, `black`, `gofmt`, etc.).

### Add custom agents

Create reusable agent definitions in `.claude/agents/`:

```bash
mkdir -p .claude/agents
```

See the Subagents section of the skill for examples and best practices.

## Version

Current: **10.1** | Author: Vinny Carpenter
