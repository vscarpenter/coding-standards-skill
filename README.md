# Coding Standards Skill

A Claude Code skill that enforces consistent coding standards and agentic behavior guidelines across every project. Built for AI-assisted development workflows.

> *Code should be safe to modify, easy to reason about, and boring to maintain. When in doubt, simplify.*

## What It Does

This skill gives Claude Code a comprehensive set of development standards that apply automatically to every coding session. Instead of dropping a `coding-standards.md` file into each repository, the skill loads these guidelines globally and yields to project-specific conventions (CLAUDE.md, CONTRIBUTING) when they exist.

**Core capabilities:**

- **Agentic behavior guidelines.** Read-before-write orientation, ambiguity handling, context management, incremental progress, and self-review before presenting code.
- **Code quality standards.** Naming conventions, type safety, structure and abstraction rules, performance awareness, logging and observability patterns, and configuration management.
- **Testing and error handling.** TDD approach, test isolation strategy, behavior-based test naming, and typed error handling patterns.
- **Security standards.** Input validation, parameterized queries, least-privilege principles, secret management.
- **Git workflow.** Conventional commits, branch naming, and the standard commit-push-PR workflow.
- **Verification shortcuts.** Type `qcheck` for a skeptical code review or `qcode` to implement with full verification.
- **Subagent delegation.** Guidelines for when and how to use subagents effectively, including model selection by task complexity.

## Skill Structure

```
coding-standards/
├── SKILL.md                    # Core standards (always loaded when triggered)
└── references/
    ├── testing.md              # Testing and error handling (loaded on demand)
    ├── security.md             # Security standards (loaded on demand)
    └── git-workflow.md         # Commit and branch conventions (loaded on demand)
```

The skill uses progressive loading. The main SKILL.md contains the agentic behavior guidelines, code quality standards, and quick reference. The reference files are loaded only when Claude is working on testing, security, or git operations, keeping context usage efficient.

## Installation

### Claude Code (global, recommended)

Copy the skill folder to your global skills directory:

```bash
# Clone the repo
git clone https://github.com/vscarpenter/coding-standards-skill.git

# Copy to global skills directory
mkdir -p ~/.claude/skills
cp -r coding-standards-skill/coding-standards ~/.claude/skills/coding-standards
```

The skill is now available in every Claude Code session across all your projects.

### Claude Code (project-level)

Add the skill to a specific project so it travels with the repo:

```bash
# From your project root
mkdir -p .claude/skills
cp -r /path/to/coding-standards-skill/coding-standards .claude/skills/coding-standards

# Commit it
git add .claude/skills/
git commit -m "chore: add coding-standards skill"
```

Anyone who clones the repo gets the skill automatically.

### Claude Code (via npx)

```bash
npx add-skill vscarpenter/coding-standards-skill
```

### Claude.ai (upload)

1. Download the `coding-standards.skill` file from the [Releases](https://github.com/vscarpenter/coding-standards-skill/releases) page.
2. In Claude.ai, go to **Settings > Capabilities** and enable **Code execution and file creation**.
3. Go to **Customize > Skills** and upload the `.skill` file.
4. Toggle the skill on.

For Team and Enterprise plans, organization Owners can provision the skill for all users through organization settings.

### Claude API

Skills can also be used via the Claude API with the code execution tool. See the [Skills API Quickstart](https://docs.anthropic.com) for details.

## Usage

Once installed, the skill triggers automatically whenever Claude Code is writing, reviewing, refactoring, or committing code. No manual invocation required.

### Verification Shortcuts

**`qcheck`** — Runs a skeptical senior engineer review against every major code change:

1. Does this follow our coding standards?
2. Are there comprehensive tests?
3. Is error handling adequate?
4. Does this maintain existing patterns?
5. Are there any security concerns?
6. Is the code maintainable and readable?
7. Are types properly annotated?
8. Is logging/observability adequate for production?

**`qcode`** — Implements with full verification:

- All new tests pass
- Existing tests still pass
- Linting/formatting tools run
- Type checking passes
- Code follows established patterns
- Self-review completed (no dead code, debug statements, or TODOs)

### Key Principles

The skill enforces these priorities in order:

1. **Simplicity over cleverness.** Prefer clarity to novelty.
2. **Build small, iterate fast.** Deliver working code before optimizing.
3. **Code for humans.** Readable by a junior engineer without scrolling to other files.
4. **Prefer boring tech.** Stability over hype.
5. **Standard lib over external.** Use the language's standard library unless it requires more than 2x the code.

### Project-Specific Overrides

The skill respects a clear hierarchy: **project-specific files take priority.** If a repository has its own CLAUDE.md, CONTRIBUTING, or style guide, the skill defers to those for project-specific conventions and governs everything else.

## Customization

Fork this repo and modify the skill to match your team's standards. Key files to customize:

- **SKILL.md** — Core principles, naming rules, function length limits, and the verification shortcuts.
- **references/testing.md** — Test coverage targets, isolation strategy, and mocking preferences.
- **references/security.md** — Security requirements specific to your domain.
- **references/git-workflow.md** — Commit types, branch naming format, and PR workflow.

## Contributing

1. Fork the repository.
2. Create a feature branch: `feat/your-improvement`
3. Follow the coding standards defined in this skill (naturally).
4. Submit a pull request with a clear description of the change.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

## Author

**Vinny Carpenter**
VP of Engineering | Cloud, Platform & DevOps
[vinny.dev](https://vinny.dev) | [LinkedIn](https://www.linkedin.com/in/vinnycarpenter/) | [GitHub](https://github.com/vscarpenter)

---

*Document Version 6.0*
