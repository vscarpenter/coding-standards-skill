# Git Workflow Reference

## Commit Standards

When asked to commit and push, write a descriptive conventional commit message, bump the version if appropriate, and create a PR unless told otherwise.

**Standard workflow:** commit, push, create PR.

### Conventional Commit Format

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`, `build`

**Examples:**
```
feat(auth): add OAuth2 login flow
fix(api): handle null response from payment provider
refactor(utils): extract date formatting into shared module
docs(readme): add local development setup instructions
```

**Rules:**
- Subject line: imperative mood, lowercase, no period, max 72 characters
- Body: explain what and why, not how
- Breaking changes: add `BREAKING CHANGE:` in footer or `!` after type
- Reference issues when applicable: `Closes #42`

## Branch Naming

Use the format: `<type>/<short-description>` (e.g., `feat/oauth-login`, `fix/null-payment-response`).
