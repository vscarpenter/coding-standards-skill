# CLAUDE.md — Coding Standards Skill

This repository contains a Claude Code skill for enforcing coding standards. When contributing, follow the standards defined in the skill itself.

## Project Structure

- `coding-standards/SKILL.md` — Core skill file (agentic guidelines + code quality standards)
- `coding-standards/references/` — Progressive-load reference files (testing, security, git workflow)
- `README.md` — Public-facing documentation and installation instructions
- `CHANGELOG.md` — Version history following Keep a Changelog format

## Contributing Guidelines

- All content is Markdown. There is no build step or runtime code.
- Keep SKILL.md under 300 lines. Move detailed guidance to reference files.
- Reference files should be self-contained and loadable independently.
- Follow conventional commit format for all commits (see `references/git-workflow.md`).
- Test changes by installing the skill locally (`~/.claude/skills/coding-standards`) and verifying behavior in a Claude Code session.

## Style

- Use imperative voice for guidelines ("Validate inputs" not "Inputs should be validated").
- Be specific and actionable. Avoid vague advice like "write clean code."
- Every guideline should be something a developer can verify in a code review.
- Prefer bullet lists over prose for scanability.

## Version Policy

- Use semantic versioning. Bump the version in CHANGELOG.md, SKILL.md footer, and README.md footer.
- Breaking changes to the skill format or removed guidelines require a major version bump.
