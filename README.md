# Coding Standards Skill for Claude Code

Comprehensive code standards and agentic behavior guidelines, packaged as a Claude Code skill with companion slash commands. Version 10.1 promotes red/green/refactor TDD to the default workflow and adds agentic guidance for parallel tool execution, bash-first multi-step ops, and supply-chain vigilance.

## What's Included

```
.claude/
  skills/
    coding-standards/
      SKILL.md            # The skill (auto-loaded by Claude Code)
  commands/
    qspec.md              # /qspec  — generate a feature spec + test stubs
    qcheck.md             # /qcheck — skeptical staff engineer review
    tdd.md                # /tdd    — start a red/green/refactor cycle
coding-standards.md       # Standalone reference copy (same content, no frontmatter)
```

## Install

The repo is structured so you can drop the `.claude/` directory straight into any project or into your user config. Pick one:

### Option A: Global install (one-liner, recommended for solo devs)

Applies the skill and commands to every project you open with Claude Code.

```bash
git clone --depth 1 https://github.com/vscarpenter/coding-standards-skill.git /tmp/cs \
  && mkdir -p ~/.claude/skills ~/.claude/commands \
  && cp -r /tmp/cs/.claude/skills/coding-standards ~/.claude/skills/ \
  && cp /tmp/cs/.claude/commands/*.md ~/.claude/commands/ \
  && rm -rf /tmp/cs
```

To update later, rerun the same command.

### Option B: Per-project install (recommended for teams)

Commit the skill into your repo so every teammate using Claude Code gets it automatically.

```bash
git clone --depth 1 https://github.com/vscarpenter/coding-standards-skill.git /tmp/cs \
  && cp -r /tmp/cs/.claude . \
  && rm -rf /tmp/cs \
  && git add .claude/ \
  && git commit -m "chore: add coding-standards skill and slash commands"
```

## Usage

### The Skill (automatic)

Once installed, the coding-standards skill triggers automatically whenever Claude Code is doing development work. There is nothing to invoke — Claude loads the skill and follows its guidelines for every coding task.

The skill covers:
- **Agentic behavior** — codebase orientation, spec-driven development, verification-first workflow, parallel tool execution, bash-first multi-step ops, outcome-defined task exits, session handoff, self-improvement loop, context management
- **TDD as default** — red/green/refactor is mandatory for non-trivial logic; acceptance criteria become the first failing tests
- **Code quality** — naming, types, structure, performance, accessibility, logging, dependency management
- **Testing & errors** — test isolation, typed error handling, behavior-based test names, Arrange-Act-Assert
- **Security & supply chain** — input validation, parameterized queries, least privilege, auditing AI-installed dependencies, lockfile discipline
- **Git workflow** — conventional commits, branch naming, PR standards, code review norms
- **Architecture** — ADRs for significant decisions
- **Task management** — todo tracking, Definition of Done (with red-before-green and per-AC coverage gates), lessons learned
- **Prompt engineering** — prompt structure, patterns, anti-patterns
- **Claude Code tooling** — subagents (including a `tdd-enforcer` example), custom agents, hooks, skills, slash commands

### Slash Commands (on demand)

**`/qspec`** — Generate a spec for a feature before writing any code. Produces goal, inputs/outputs, constraints, edge cases, acceptance criteria, **and empty test stubs** that map to each criterion. Saves to `tasks/spec.md` and waits for approval before implementing.

**`/tdd`** — Start a red/green/refactor cycle. Claude writes a failing test first, confirms it fails for the right reason, then writes the minimal implementation to make it pass, then refactors.

**`/qcheck`** — Skeptical staff engineer review of all changed files. Checks tests, error handling, types, observability, security, and the Definition of Done. Also flags logic that appears to have been implemented before its tests were written.

## Customizing

### Add project-specific rules

Add a `CLAUDE.md` to your project root for rules specific to your codebase. The skill's Self-Improvement Loop prompts Claude to update `CLAUDE.md` after every correction, so it grows organically over time.

### Configure hooks

The skill recommends formatting, context-handoff, and verification-gate hooks. Add these to your `.claude/settings.json`:

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
    ],
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "npm test -- --passWithNoTests && npx tsc --noEmit || exit 1"
          }
        ]
      }
    ]
  }
}
```

Replace the formatter and test/type-check commands with your project's equivalents (`prettier`, `black`, `gofmt`, `pytest`, `go test`, etc.). The `Stop` hook is the verification gate that blocks Claude from marking a task complete until tests and type checks pass.

### Add custom agents

Create reusable agent definitions in `.claude/agents/`. The skill includes a `tdd-enforcer` example that verifies red/green cycles were followed on a changeset.

```bash
mkdir -p .claude/agents
```

See the Subagents section of the skill for the full example and best practices.

## Version

Current: **10.1** | Author: Vinny Carpenter
