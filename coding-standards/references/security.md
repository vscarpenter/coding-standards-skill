# Security Reference

## Input Validation & Output Encoding

- Validate and sanitize all user inputs at the system boundary
- Use allowlists over denylists where possible
- Encode output for the target context (HTML, URL, SQL, shell)
- Never trust client-side validation alone; always validate server-side

## Database & Query Safety

- Use parameterized queries or prepared statements (no string concatenation for SQL)
- Apply least-privilege database roles; application accounts should not have DDL permissions
- Sanitize inputs used in dynamic queries, even with an ORM

## Authentication & Authorization

- Never store plaintext passwords; use bcrypt, scrypt, or argon2
- Enforce least-privilege principles for all access controls
- Validate authorization on every request, not just at login
- Use short-lived tokens and implement token refresh flows

## Secrets Management

- Never commit secrets, API keys, or credentials to version control
- Use environment variables or a secrets manager (Vault, AWS Secrets Manager, etc.)
- Rotate secrets on a regular schedule and immediately after any suspected exposure
- Add secret patterns to `.gitignore` and consider pre-commit hooks for secret detection

## Dependency Security

- Keep dependencies patched and up to date
- Run dependency audits regularly (`npm audit`, `pip audit`, `bundle audit`, `cargo audit`)
- Pin dependency versions in production; use lockfiles
- Review new dependencies before adding them: check maintenance status, known vulnerabilities, and scope of permissions

## Common Vulnerability Awareness

Be aware of these common attack vectors during code review:

- **Injection** (SQL, command, LDAP) — parameterize all queries and commands
- **XSS** (Cross-Site Scripting) — encode output, use Content Security Policy headers
- **CSRF** (Cross-Site Request Forgery) — use anti-CSRF tokens for state-changing operations
- **SSRF** (Server-Side Request Forgery) — validate and allowlist outbound URLs
- **Path Traversal** — never use raw user input in file paths; normalize and validate

## Transport & Configuration

- Enforce HTTPS/TLS for all external communication
- Set security headers: `Content-Security-Policy`, `X-Content-Type-Options`, `Strict-Transport-Security`
- Disable verbose error messages in production; log details server-side only
- Configure CORS restrictively; never use `Access-Control-Allow-Origin: *` in production
