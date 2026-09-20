# Coding Standards Skill for Claude Code

Comprehensive code standards and agentic behavior guidelines, packaged as a Claude Code skill with companion user-invoked skills and a standard set of subagents. Version 18.0 reorganizes the standards into loading layers. Judgment rules stay in a short global `CLAUDE.md`, process text loads on demand from skills, and mechanical rules move to hooks and lint config. The release also adds task tiers, a hard-to-reverse action list, a comprehension gate for non-trivial changes, and tiered exit checklists.

## What's Included

```
.claude/
  skills/
    coding-standards/
      SKILL.md            # The standards. Model-invoked for any development work.
    qspec/SKILL.md        # /qspec: generate a feature spec + test stubs
    qcheck/SKILL.md       # /qcheck: skeptical staff engineer review
    tdd/SKILL.md          # /tdd: start a red/green/refactor cycle
  agents/
    build-validator.md    # build, lint, type check, tests; reports GREEN/RED
    code-simplifier.md    # refactor step: simplifies in place without changing behavior
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
- **Runtime loading architecture** (Part 0): which rules load every session, on demand, or through tooling, plus the precedence order when directives conflict
- **The agentic lifecycle** (Part 1): codebase orientation, task tiers, the unknowns interview, spec-driven development, scope discipline, a deviations ledger with halt conditions, hard-to-reverse action safety, session handoff, and a comprehension gate for non-trivial work
- **Non-negotiable invariants** (Part 2): simplicity, dependency discipline, security, error handling, and red/green/refactor
- **Mechanical rules** (Part 3): formatting, commit format, size limits, type annotations, and coverage, each mapped to the tool that enforces it
- **Architecture** (Part 4): ADRs for decisions that are hard to reverse
- **Prompt engineering** (Part 5): prompt structure, canonical prompt sources, and anti-patterns
- **Claude Code primitives** (Part 6): slash commands, skills, subagents, agent teams, and hooks
- **Tiered exit checklists** (Part 7): what must be true before a task is done, by tier

### User-Invoked Skills (on demand)

These only run when you type them. They stay out of the skill listing, so they cost no context until used.

**`/qspec <feature>`** generates a spec before writing any code. It runs a short unknowns interview (up to three questions, highest blast radius first), then produces goal, inputs/outputs, constraints, edge cases, out of scope, acceptance criteria, and empty test stubs that map to each criterion. The spec saves to `tasks/spec.md`, and Claude waits for approval before implementing.

**`/tdd <behavior>`** starts a red/green/refactor cycle. Claude writes a failing test first and confirms it fails for the right reason. It pauses for your approval, writes the minimal implementation to make the test pass, then proposes a refactor.

**`/qcheck`** runs a skeptical staff engineer review of all changed files. It checks tests, error handling, types, observability, security, acceptance-criteria coverage, and the exit checklist for the task's tier. It flags logic implemented before its tests. Every finding comes back tagged BLOCKING, IMPORTANT, or NIT.

`/qcheck` grades a diff against these standards. Claude Code's bundled `/code-review` hunts for bugs. Run both before opening a PR.

### Subagents

The repo ships five agents in `.claude/agents/`. Claude delegates to them on its own when their descriptions match the task, or you can ask for one by name ("run build-validator"). `tdd-enforcer` runs in an isolated worktree, so commit before invoking it. `security-reviewer` keeps project-scoped memory and gets better at your codebase over time.

## Customizing

### Keep the core layer short

Part 0 of the skill describes a core layer: the judgment rules Claude must hold in every session, kept under 600 words in the body of your global `~/.claude/CLAUDE.md`. The standards refer to that file as `CLAUDE-core.md`. Everything else loads on demand through commands and skills, or a hook enforces it. When a tool can check a rule, move it to a hook instead of adding it to the core layer.

### Add project-specific rules

Add a `CLAUDE.md` to your project root for rules specific to your codebase. Put path-scoped conventions in `.claude/rules/*.md`. The skill's self-improvement loop has Claude distill each session's implementation notes into `tasks/lessons.md` or `CLAUDE.md`, so the file grows organically over time.

### Configure hooks

The skill expects hooks, not prose, to enforce mechanical rules. The set below covers formatting, dependency audits, context re-injection, and a verification gate. Add these to your `.claude/settings.json`:

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

Replace the formatter and test/type-check commands with your project's equivalents (`prettier`, `black`, `gofmt`, `pytest`, `go test`, etc.). The hooks need `jq` on your PATH. Commit message format is a git concern rather than a Claude Code hook, so enforce it with commitlint in a `commit-msg` git hook.

### Add or adjust agents

Edit the files in `.claude/agents/` or add your own for work you delegate more than once. See Part 6 (Claude Code Primitives) in the skill for guidance on model selection and `isolation: worktree`.

### Standing approval for push and PR workflows

The hard-to-reverse action list in Part 1 requires confirmation before Claude pushes code or opens a PR. A user-invoked workflow that includes those steps carries approval for that run. The standards name the author's git-flow-automation skill as the example; substitute your own commit-push-PR command.

## Version

Current: **18.0** | Author: Vinny Carpenter
