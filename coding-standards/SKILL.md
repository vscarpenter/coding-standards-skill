---
name: coding-standards
description: Comprehensive coding standards and agentic behavior guidelines for AI-assisted development. Use this skill whenever writing, reviewing, refactoring, or committing code in any language or framework. Also triggers for code reviews, pull request creation, test writing, git operations, debugging sessions, and any task involving code quality assessment. When the user says "qcheck" or "qcode", follow the verification shortcuts defined in this skill. This skill applies to every coding session and every repository, providing the baseline standards that yield to project-specific CLAUDE.md or CONTRIBUTING files when they exist.
---

# Code Standards & Agentic Guidance

Comprehensive guidance for AI-assisted development. These standards apply to every coding session. When a project has its own CLAUDE.md, CONTRIBUTING, or style guide, defer to those for project-specific conventions. These standards govern everything else.

> *Code should be safe to modify, easy to reason about, and boring to maintain. When in doubt, simplify.*

---

## Agentic Behavior Guidelines

These directives govern how you approach complex, multi-step tasks requiring sustained autonomous work. Every directive here is actionable and applies to every coding session.

### Codebase Orientation (Read Before Write)

Before writing any code in an existing project, understand the landscape:

1. Read the project's README, CLAUDE.md, and any CONTRIBUTING docs first.
2. Explore the directory structure to understand the project layout.
3. Identify existing patterns: naming conventions, module organization, error handling style, test structure.
4. Check for existing utilities, helpers, or shared modules before creating new ones.
5. Match the existing code style exactly, even if it differs from these standards.

The existing codebase is the primary style guide. These standards apply to greenfield code or when explicitly refactoring.

### Handling Ambiguity

When requirements are unclear or incomplete:

1. **Ask before assuming.** If a requirement has multiple valid interpretations, ask which one is intended rather than guessing.
2. **State your assumptions.** If you must proceed without clarification, explicitly list every assumption you are making.
3. **Prefer reversible choices.** When guessing, choose the option that is easiest to change later.
4. **Flag scope questions early.** If a task might touch shared code, external APIs, or infrastructure, confirm scope before modifying.

Never silently interpret ambiguous requirements and build an entire solution on an assumption that could be wrong.

### Context Management & Sustained Work

For lengthy tasks, follow these requirements:

1. Before writing code, outline your implementation plan with clear milestones and acceptance criteria for each.
2. Work systematically through each milestone, committing functional changes frequently.
3. Commit at least every significant component or logical unit of work.
4. Monitor your context usage and prioritize committing working code before context exhaustion.
5. Never leave significant work uncommitted.

If you find yourself 80% through context with major uncommitted work, stop adding features and commit immediately.

**Compaction Directive:** When compacting, always preserve the full list of modified files, current task status, test commands, and next steps. Do not discard working state during summarization.

### Reflection After Tool Results

After each tool result, pause to evaluate before proceeding:

- Did the operation succeed or fail?
- Does the output match expectations?
- Are there edge cases or errors to address?
- What is the root cause if results are unexpected?

Take time to analyze results and plan your next action deliberately. If results are unexpected, diagnose the root cause before attempting fixes. Avoid repeated trial-and-error changes without understanding the underlying issue.

### Solution Quality Requirements

Every solution must meet these standards:

- Implement robust, general-purpose logic that handles all valid inputs correctly
- Avoid hardcoded values, magic numbers, or logic tailored to specific test inputs
- Include appropriate error handling and input validation
- Use standard tools and language features rather than external workarounds
- Code should be maintainable, readable, and follow established conventions

Never create solutions that only work for specific test cases. Always implement the actual algorithm or business logic.

### Self-Review Before Presenting

Before presenting code or marking a task as complete, perform a quick self-review:

1. Re-read every changed file. Look for typos, leftover debug statements, and TODO comments.
2. Verify all imports are used and no dead code remains.
3. Confirm naming is consistent across the changeset.
4. Check that error paths are handled, not just the happy path.
5. Ensure the code compiles/runs and tests pass.

Never present code you have not re-read. A 30-second review catches the majority of avoidable mistakes.

### Incremental Progress

Build incrementally to ensure quality:

- Get a minimal working version first, then extend
- Avoid writing large amounts of code before testing any of it
- Run the full test suite after every file modification and fix failures before proceeding
- Do not assume code is correct without execution
- If tests fail, analyze the failure and diagnose root cause before making changes

### Progress Updates

After completing each milestone or significant tool operation, provide a brief summary of what was done, what changed, and what comes next. Do not skip status updates after tool calls.

### Error Learning

After encountering a mistake or suboptimal solution, analyze what went wrong and propose a specific CLAUDE.md update to prevent recurrence. Actively improve the development process.

---

## Code Quality Standards

### Core Principles

1. **Simplicity over cleverness.** Prefer clarity to novelty.
2. **Build small, iterate fast.** Deliver working code before optimizing.
3. **Code for humans.** Code must be readable by a junior engineer without needing to scroll to other files.
4. **Prefer boring tech.** Stability over hype.
5. **Automate consistency.** Enforce linting, tests, and formatting in CI.
6. **Standard lib over external.** Always choose the language's standard library over an external dependency unless the standard library requires more than 2x the code to achieve the same result.

### Naming & Clarity

- Use descriptive names; avoid generic terms like "data", "temp", or single letters
- Target 30 lines or fewer per function with single responsibility; anything over 40 lines is a red flag requiring refactor
- Maximum 3 levels of nesting; use early returns
- Comments explain WHY, not WHAT
- Limit code files to approximately 350-400 lines; split by responsibility

### Type Safety & Static Analysis

- Add type annotations to all function signatures (parameters and return types)
- Use strict/strict-mode compiler settings where available (TypeScript strict, Python mypy strict)
- Prefer typed data structures (interfaces, typed dicts, structs) over untyped maps or generic objects
- Run static analysis and type checking as part of the verification workflow
- Never use `any`, `object`, or equivalent escape hatches without a comment explaining why

### Structure & Abstraction

- Apply DRY only after 3+ repetitions
- Follow YAGNI: do not build for hypothetical futures
- Prefer composition over inheritance
- Duplicate if it is clearer than abstracting
- No magic numbers; use named constants
- Inject dependencies (I/O, time, randomness)

### Performance Awareness

Do not prematurely optimize, but do not write obviously inefficient code either:

- Choose appropriate data structures (use a Set for lookups, not an Array scan)
- Be aware of algorithmic complexity; avoid O(n^2) when O(n) or O(n log n) is straightforward
- Watch for N+1 query patterns in database access
- Avoid unnecessary allocations in hot paths (loops, event handlers)
- Profile before optimizing; measure, do not guess
- Document any intentional performance tradeoffs

### File Boundaries

- Do not modify files outside the current working directory without explicit permission
- Do not edit configuration files, CI/CD pipelines, or infrastructure code unless the task specifically requires it
- When uncertain about scope, ask before modifying files in shared or upstream directories

### Configuration Management

- Use environment variables for deployment-specific values (URLs, ports, feature flags)
- Provide sensible defaults for local development; never require manual setup to run locally
- Separate config from code: no inline connection strings, API keys, or endpoint URLs
- Use a single config module/file per service as the source of truth
- Document every environment variable with its purpose, type, and default value

### Logging & Observability

- Use structured logging (JSON format preferred) with consistent field names
- Include correlation/request IDs for tracing across service boundaries
- Log at appropriate levels: ERROR for failures requiring attention, WARN for degraded states, INFO for significant business events, DEBUG for development
- Never log secrets, tokens, passwords, or PII
- Include enough context to diagnose issues without reproducing them: what operation, what input (sanitized), what outcome

### Guardrails

- Validate inputs, sanitize outputs
- No hard-coded environment values
- Document public APIs with usage examples

### Subagents

- Use subagents liberally to keep main context window clean and focused
- Offload research, exploration, file analysis, and codebase scanning to subagents
- For complex problems, use parallel subagents for independent analysis tasks
- Chain subagents sequentially when tasks have dependencies (plan, implement, test)
- One well-defined task per subagent for focused execution
- Subagents must return concise summaries, not raw output, to preserve main context
- Use read-only tools (Read, Grep, Glob) for research subagents; grant write access only to implementation subagents
- Prefer Haiku-model subagents for simple research, scanning, and exploration tasks to control costs
- Reserve Sonnet or Opus for subagents that reason about architecture or write complex implementations
- Do not use subagents for tasks that take fewer than 3 tool calls; the overhead is not worth it
- During implementation, delegate tasks to available subagents based on their expertise:
  - Use Explore subagent for codebase scanning, pattern discovery, and reading files
  - Use general-purpose subagent for multi-step implementation tasks requiring file modifications
  - Use dedicated review subagents for code quality, security, and test coverage checks

---

## Quick Reference

### Verification Shortcuts

When the user types **"qcheck"**, perform this analysis:

```
You are a SKEPTICAL senior software engineer. For every MAJOR code change:
1. Does this follow our coding standards?
2. Are there comprehensive tests?
3. Is error handling adequate?
4. Does this maintain existing patterns?
5. Are there any security concerns?
6. Is the code maintainable and readable?
7. Are types properly annotated?
8. Is logging/observability adequate for production?
```

When the user types **"qcode"**, do this:

```
Implement your plan and ensure:
- All new tests pass
- Run existing tests to ensure nothing breaks
- Run linting/formatting tools
- Verify type checking passes
- Code follows established patterns
- Self-review completed (no dead code, debug statements, or TODOs)
```

### Quality Checklist

- Understandable in 5 minutes
- Self-explanatory names
- Comprehensive error handling with typed errors
- Simplicity favored over abstraction
- Tests and security checks included
- No hardcoded values or magic numbers
- All work committed before context exhaustion
- Test suite passes after every modification
- Type annotations on all function signatures
- Structured logging for significant operations
- Environment config externalized, not inline

### Red Flags

- Functions exceeding 40 lines
- More than 3 nesting levels
- Unused abstractions or commented-out code
- TODOs without ticket links
- Copy-pasted logic (3+ times requires refactor)
- Hardcoded test values or magic numbers
- Trial-and-error fixes without root cause analysis
- Large uncommitted changes late in context
- Modifying files outside the task's scope
- Missing type annotations on public interfaces
- Untyped `any` or `object` without justification comment
- console.log / print statements left in production code
- Catching and ignoring exceptions silently
- Writing code before reading existing patterns in the codebase

### Prompt Template

Use this template when requesting features:

```
Build [feature] that:
  - Uses clear naming
  - Validates inputs, handles errors
  - Includes tests for core cases
  - Follows [framework] conventions
  - Avoids premature abstraction
  - Keeps functions <30 lines
```

---

## Additional References

For deeper guidance on specific topics, read the corresponding reference file:

- **`references/testing.md`** — Read when writing tests, setting up test infrastructure, or handling errors. Covers TDD approach, test isolation, mocking strategy, and error handling patterns.
- **`references/security.md`** — Read when handling user input, database queries, secrets, or dependency management. Covers input validation, parameterized queries, least-privilege, and dependency scanning.
- **`references/git-workflow.md`** — Read when committing, creating branches, or opening pull requests. Covers conventional commit format, branch naming, and the standard commit-push-PR workflow.

---

*Document Version 1.0 | Vinny Carpenter*
