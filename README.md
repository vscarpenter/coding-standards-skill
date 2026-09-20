# Coding Standards Skill for Claude Code

Comprehensive code standards and agentic behavior guidelines, packaged as a Claude Code skill with companion user-invoked skills and a standard set of subagents. Version 15.0 is model-neutral and matched to current Claude Code mechanics: hooks that actually block (exit 2, stdin JSON, `SessionStart` on compaction), commands folded into skills, guidance on where state belongs now that Claude Code has auto memory, and the five subagents the standards reference shipped in the repo.

## What's Included

```
.claude/
  skills/
    coding-standards/
      SKILL.md            # The standards. Model-invoked for any development work.
    qspec/SKILL.md        # /qspec  — generate a feature spec + test stubs
    qcheck/SKILL.md       # /qcheck — skeptical staff engineer review
    tdd/SKILL.md          # /tdd    — start a red/green/refactor cycle
  agents/
    build-validator.md    # build, lint, type check, tests; reports GREEN/RED
    code-simplifier.md    # refactor step: applies the elegance check in place
    security-reviewer.md  # read-only security findings by severity
    tdd-enforcer.md       # proves tests preceded, and actually test, the code
    verify-app.md         # runs the app and verifies the change end to end
coding-standards.md       # Standalone reference copy (same content, no frontmatter)
```

## Install

The repo is structured so you can drop the `.claude/` directory straight into any project or into your user config. Pick one:

### Option A: Global install (one-liner, recommended for solo devs)

Applies the skills and agents to every project you open with Claude Code.

```bash
git clone --depth 1 https://github.com/vscarpenter/coding-standards-skill.git /tmp/cs \
  && mkdir -p ~/.claude/skills ~/.claude/agents \
  && cp -r /tmp/cs/.claude/skills/* ~/.claude/skills/ \
  && cp /tmp/cs/.claude/agents/*.md ~/.claude/agents/ \
  && rm -rf /tmp/cs
```

To update later, rerun the same command. If you installed an earlier version, delete the old `~/.claude/commands/{qspec,qcheck,tdd}.md` files; the skills replace them.

### Option B: Per-project install (recommended for teams)

Commit the skills and agents into your repo so every teammate using Claude Code gets them automatically.

```bash
git clone --depth 1 https://github.com/vscarpenter/coding-standards-skill.git /tmp/cs \
  && cp -r /tmp/cs/.claude . \
  && rm -rf /tmp/cs \
  && git add .claude/ \
  && git commit -m "chore: add coding-standards skill, commands, and agents"
```

## Usage

### The Skill (automatic)

Once installed, Claude Code lists the coding-standards skill's description in every session and loads the full skill when development work starts. There is nothing to invoke. Run `/skill-doctor` to see what it costs in context and how often it fires.

The skill covers:
- **Agentic behavior** — codebase orientation, spec-driven development, verification-first workflow, parallel tool execution, outcome-defined task exits, session handoff, self-improvement loop, context management, and where state belongs (`tasks/` files versus Claude Code auto memory)
- **TDD as default** — red/green/refactor is mandatory for non-trivial logic; acceptance criteria become the first failing tests
- **Code quality** — naming, types, structure, dependency management, the four-point elegance check
- **Testing & errors** — test isolation, typed error handling, behavior-based test names, Arrange-Act-Assert
- **Security & supply chain** — input validation, parameterized queries, least privilege, auditing AI-installed dependencies, lockfile discipline
- **Git workflow** — conventional commits, branch naming, PR standards, code review norms
- **Architecture** — ADRs for significant decisions
- **Task management** — todo tracking, Definition of Done (with red-before-green and per-AC coverage gates), lessons learned
- **Prompt engineering** — prompt structure, patterns, anti-patterns
- **Claude Code primitives** — skills (model-invoked and user-invoked), bundled skills such as `/code-review` and `/verify`, subagents, and hooks that block correctly, consolidated in Part 9

### User-Invoked Skills (on demand)

These only run when you type them. They stay out of the skill listing, so they cost no context until used.

**`/qspec <feature>`** — Generate a spec before writing any code. Produces goal, inputs/outputs, constraints, edge cases, acceptance criteria, and empty test stubs that map to each criterion. Saves to `tasks/spec.md` and waits for approval before implementing.

**`/tdd <behavior>`** — Start a red/green/refactor cycle. Claude writes a failing test first, confirms it fails for the right reason, pauses for your approval, writes the minimal implementation to make it pass, then proposes a refactor.

**`/qcheck`** — Skeptical staff engineer review of all changed files. Checks tests, error handling, types, observability, security, the elegance check, and the Definition of Done. Flags logic implemented before its tests. Reports every finding tagged BLOCKING, IMPORTANT, or NIT.

`/qcheck` grades a diff against these standards. Claude Code's bundled `/code-review` hunts for bugs. Run both before opening a PR.

### Subagents

The five agents in `.claude/agents/` are the "standard agent files" the skill refers to. Claude delegates to them on its own when their descriptions match the task, or you can ask for one by name ("run build-validator"). `tdd-enforcer` runs in an isolated worktree, so commit before invoking it. `security-reviewer` keeps project-scoped memory and gets better at your codebase over time.

## Customizing

### Add project-specific rules

Add a `CLAUDE.md` to your project root for rules specific to your codebase. Put path-scoped conventions in `.claude/rules/*.md`. The skill's Self-Improvement Loop prompts Claude to update `CLAUDE.md` and `tasks/lessons.md` after every correction, so they grow organically over time.

### Configure hooks

The skill recommends formatting, dependency-audit, context-reinject, and verification-gate hooks. Add these to your `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "f=$(jq -r '.tool_input.file_path // empty'); [ -n \"$f\" ] && npx biome format --write \"$f\"; exit 0"
          }
        ]
      },
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "if": "Bash(npm install *)",
            "command": "npm audit --audit-level=high || { echo 'npm audit found high-severity issues' >&2; exit 2; }"
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
            "command": "cat tasks/todo.md tasks/lessons.md 2>/dev/null; exit 0"
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
            "command": "jq -e '.stop_hook_active' >/dev/null && exit 0; npm test -- --passWithNoTests && npx tsc --noEmit || { echo 'Tests or type check failed. Fix before stopping.' >&2; exit 2; }"
          }
        ]
      }
    ]
  }
}
```

Three mechanics matter here, and each one was wrong in earlier versions of this README:

1. **Exit 2 blocks; exit 1 does not.** A hook that ends in `|| exit 1` reports a non-blocking error and Claude carries on without seeing it. The `Stop` gate above uses exit 2, which keeps Claude working until tests and type checks pass. The `stop_hook_active` guard stops it looping when tests cannot be fixed.
2. **Hooks read stdin JSON.** There is no `$CLAUDE_FILE_PATH`. The edited file comes from `jq -r '.tool_input.file_path'`.
3. **`PostCompact` output never reaches Claude.** To re-inject `tasks/` after compaction, use `SessionStart` with the `compact` matcher, whose stdout is added to context.

Replace the formatter and test/type-check commands with your project's equivalents (`prettier`, `black`, `gofmt`, `pytest`, `go test`, etc.). The hooks need `jq` on your PATH.

### Add or adjust agents

Edit the files in `.claude/agents/` or add your own. See Part 9 (Claude Code Primitives) → Subagents in the skill for guidance on tool permissions, model selection, preloading skills, memory scope, and `isolation: worktree`.

## Version

Current: **15.0** | Author: Vinny Carpenter
