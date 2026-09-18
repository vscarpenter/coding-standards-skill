# Coding Standards Skill for Claude Code

Comprehensive code standards and agentic behavior guidelines, packaged as a Claude Code skill with companion slash commands. Version 18.0 reorganizes the standards into loading layers. Judgment rules stay in a short global `CLAUDE.md`, process text loads on demand from commands and skills, and mechanical rules move to hooks and lint config. The release also adds task tiers, a hard-to-reverse action list, a comprehension gate for non-trivial changes, and tiered exit checklists.

## What's Included

```
.claude/
  skills/
    coding-standards/
      SKILL.md            # The skill (auto-loaded by Claude Code)
  commands/
    qspec.md              # /qspec: generate a feature spec + test stubs
    qcheck.md             # /qcheck: skeptical staff engineer review
    tdd.md                # /tdd: start a red/green/refactor cycle
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

Once installed, the coding-standards skill triggers automatically whenever Claude Code is doing development work. There is nothing to invoke. Claude loads the skill and follows its guidelines for every coding task.

The skill covers:
- **Runtime loading architecture** (Part 0): which rules load every session, on demand, or through tooling, plus the precedence order when directives conflict
- **The agentic lifecycle** (Part 1): codebase orientation, task tiers, the unknowns interview, spec-driven development, scope discipline, a deviations ledger with halt conditions, hard-to-reverse action safety, session handoff, and a comprehension gate for non-trivial work
- **Non-negotiable invariants** (Part 2): simplicity, dependency discipline, security, error handling, and red/green/refactor
- **Mechanical rules** (Part 3): formatting, commit format, size limits, type annotations, and coverage, each mapped to the tool that enforces it
- **Architecture** (Part 4): ADRs for decisions that are hard to reverse
- **Prompt engineering** (Part 5): prompt structure, canonical prompt sources, and anti-patterns
- **Claude Code primitives** (Part 6): slash commands, skills, subagents, agent teams, and hooks
- **Tiered exit checklists** (Part 7): what must be true before a task is done, by tier

### Slash Commands (on demand)

**`/qspec`** generates a spec before writing any code. It runs a short unknowns interview (up to three questions, highest blast radius first), then produces goal, inputs/outputs, constraints, edge cases, out of scope, acceptance criteria, and empty test stubs that map to each criterion. The spec saves to `tasks/spec.md`, and Claude waits for approval before implementing.

**`/tdd`** starts a red/green/refactor cycle. Claude writes a failing test first, confirms it fails for the right reason, then writes the minimal implementation to make it pass, then refactors.

**`/qcheck`** runs a skeptical staff engineer review of all changed files. It checks tests, error handling, types, observability, security, acceptance-criteria coverage, and the exit checklist for the task's tier. Every finding comes back tagged BLOCKING, IMPORTANT, or NIT.

## Customizing

### Keep the core layer short

Part 0 of the skill describes a core layer: the judgment rules Claude must hold in every session, kept under 600 words in the body of your global `~/.claude/CLAUDE.md`. The standards refer to that file as `CLAUDE-core.md`. Everything else loads on demand through commands and skills, or a hook enforces it. When a tool can check a rule, move it to a hook instead of adding it to the core layer.

### Add project-specific rules

Add a `CLAUDE.md` to your project root for rules specific to your codebase. The skill's self-improvement loop has Claude distill each session's implementation notes into `tasks/lessons.md` or `CLAUDE.md`, so the file grows organically over time.

### Configure hooks

The skill expects hooks, not prose, to enforce mechanical rules. Hooks receive event data as JSON on stdin, so read the file path with `jq` rather than an environment variable. Add these to your `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "file=$(jq -r '.tool_input.file_path // empty'); [ -n \"$file\" ] && npx biome format --write \"$file\" || true"
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "matcher": "compact",
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
        "hooks": [
          {
            "type": "command",
            "command": "npm test -- --passWithNoTests && npx tsc --noEmit || exit 2"
          }
        ]
      }
    ]
  }
}
```

Replace the formatter and test/type-check commands with your project's equivalents (`prettier`, `black`, `gofmt`, `pytest`, `go test`, etc.). The `SessionStart` hook re-injects your task files after a compaction. The `Stop` hook is the verification gate that blocks Claude from finishing until tests and type checks pass; exit code 2 is what makes a hook block. Commit message format is a git concern rather than a Claude Code hook, so enforce it with commitlint in a `commit-msg` git hook.

### Add custom agents

Create reusable agent definitions in `.claude/agents/` for work you delegate more than once: parallel reads, fan-out research, or isolated implementation. Read-only agents can run on `haiku`. Agents that write files should use `sonnet` or `opus` with `isolation: worktree`. See Part 6 (Claude Code Primitives) in the skill for the full guidance.

### Standing approval for push and PR workflows

The hard-to-reverse action list in Part 1 requires confirmation before Claude pushes code or opens a PR. A user-invoked workflow that includes those steps carries approval for that run. The standards name the author's git-flow-automation skill as the example; substitute your own commit-push-PR command.

## Version

Current: **18.0** | Author: Vinny Carpenter
