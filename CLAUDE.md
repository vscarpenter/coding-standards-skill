# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A Claude Code skill package that provides comprehensive coding standards and agentic behavior guidelines. There is no application code, build system, or test suite — the deliverables are markdown files.

## Repository Structure

- `.claude/skills/coding-standards/SKILL.md` — The skill definition (YAML frontmatter + full standards). Model-invoked: Claude loads it when development work starts.
- `.claude/skills/qspec/SKILL.md` — `/qspec` user-invoked skill: generates a feature spec.
- `.claude/skills/qcheck/SKILL.md` — `/qcheck` user-invoked skill: skeptical staff engineer review.
- `.claude/skills/tdd/SKILL.md` — `/tdd` user-invoked skill: starts a red/green/refactor cycle.
- `.claude/agents/*.md` — The five standard subagents the skill references (`build-validator`, `code-simplifier`, `security-reviewer`, `tdd-enforcer`, `verify-app`).
- `coding-standards.md` — Standalone reference copy of the same content as SKILL.md, without YAML frontmatter. Kept in sync manually.
- `README.md` — Installation and usage instructions for consumers.

## Key Conventions

- **SKILL.md and coding-standards.md must stay in sync.** SKILL.md has YAML frontmatter (`name`, `description`, `metadata.version`, `metadata.author`); `coding-standards.md` is the same body content without it. When editing standards, update both files. Regenerate with:
  `awk 'BEGIN{c=0} c<2 && /^---$/{c++; next} c>=2' .claude/skills/coding-standards/SKILL.md > coding-standards.md`
- **Version is tracked in four places:** `metadata.version` in SKILL.md frontmatter, the `v15.0` in the SKILL.md H1, the "Document Version" line at the bottom of the standards, and the "Version" line at the bottom of README.md. Bump all when releasing.
- The document is organized into 10 numbered Parts. Preserve this structure when adding or modifying sections.
- **Claims about Claude Code mechanics (hook events, exit codes, frontmatter fields, bundled skills) must be checked against the current docs at code.claude.com/docs before they ship.** v14 shipped hooks that did not work because of unverified assumptions.
- Keep the standards model-neutral. Do not name a specific Claude model in a directive.
- The `/qspec`, `/qcheck`, and `/tdd` skills use `disable-model-invocation: true`. They are user-invoked only.
- Conventional commit messages are used (e.g., `docs: ...`, `feat: ...`).

## Git Workflow

- **Main branch is protected.** All changes go through PRs. The repo owner (admin) can bypass review requirements; collaborators cannot.
- **Squash merge only.** Merge commits and rebase merges are disabled. PR title becomes the commit title, PR body becomes the commit message.
- **1 approving review required** before merging. Stale reviews are dismissed on new pushes.
- **Linear history enforced.** No merge commits in the history.
- **Conversation resolution required.** All review threads must be resolved before merging.
- **Branches auto-delete** after merge.

## Editing Workflow

Since this is a docs-only repo, there are no build or lint commands. The workflow is:
1. Create a feature branch (`docs/...`, `feat/...`, etc.).
2. Edit the standards in SKILL.md (the source of truth with frontmatter).
3. Regenerate `coding-standards.md` with the awk command above and confirm `diff` is empty.
4. Update README.md if installation steps, usage, or version changed.
5. Open a PR for review — direct pushes to main are not allowed.
