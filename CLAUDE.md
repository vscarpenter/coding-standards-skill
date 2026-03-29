# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A Claude Code skill package that provides comprehensive coding standards and agentic behavior guidelines. There is no application code, build system, or test suite — the deliverables are markdown files.

## Repository Structure

- `.claude/skills/coding-standards/SKILL.md` — The skill definition (YAML frontmatter + full standards). This is auto-loaded by Claude Code when installed in a project.
- `.claude/commands/qspec.md` — `/qspec` slash command: generates a feature spec.
- `.claude/commands/qcheck.md` — `/qcheck` slash command: skeptical staff engineer review.
- `coding-standards.md` — Standalone reference copy of the same content as SKILL.md, without YAML frontmatter. Kept in sync manually.
- `README.md` — Installation and usage instructions for consumers.

## Key Conventions

- **SKILL.md and coding-standards.md must stay in sync.** SKILL.md has YAML frontmatter (`name`, `description`, `version`, `author`); `coding-standards.md` is the same body content without it. When editing standards, update both files.
- **Version is tracked in two places:** the `version` field in SKILL.md frontmatter and the "Version" line at the bottom of README.md. Bump both when releasing.
- The document is organized into 10 numbered Parts. Preserve this structure when adding or modifying sections.
- Conventional commit messages are used (e.g., `docs: ...`, `feat: ...`).

## Git Workflow

- **Main branch is protected.** All changes go through PRs. The repo owner (admin) can bypass review requirements; collaborators cannot.
- **Squash merge only.** Merge commits and rebase merges are disabled. PR title becomes the commit title, PR body becomes the commit message.
- **1 approving review required** before merging. Stale reviews are dismissed on new pushes.
- **Linear history enforced.** No merge commits in the history.
- **Conversation resolution required.** All review threads must be resolved before merging.
- **Branches auto-delete** after merge.
- Conventional commit messages are used (e.g., `docs: ...`, `feat: ...`).

## Editing Workflow

Since this is a docs-only repo, there are no build or lint commands. The workflow is:
1. Create a feature branch (`docs/...`, `feat/...`, etc.).
2. Edit the standards in SKILL.md (the source of truth with frontmatter).
3. Mirror the body content to `coding-standards.md` (no frontmatter).
4. Update README.md if installation steps, usage, or version changed.
5. Open a PR for review — direct pushes to main are not allowed.
