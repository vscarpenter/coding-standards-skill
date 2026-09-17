---
name: qcheck
description: Review all changed files as a skeptical staff engineer against the coding-standards skill and the Definition of Done. Returns findings tagged by severity. Does not rewrite code.
disable-model-invocation: true
---
Review all changed files as a skeptical staff engineer. Apply the full coding-standards skill.

Changed files are `git diff` against the base branch plus any uncommitted changes. Read the surrounding code where needed; do not report on code you have not read.

For every major code change, evaluate:

1. Does this follow our coding standards?
2. Are there comprehensive tests with positive and negative cases?
3. Were tests written before implementation? Check commit order and whether each test fails without its implementation.
4. Is error handling adequate with typed errors?
5. Does this maintain existing codebase patterns?
6. Are there any security concerns?
7. Is the code maintainable and readable by a junior engineer?
8. Are types properly annotated on all function signatures?
9. Is logging/observability adequate for production?
10. Was a verification method defined and does it pass?
11. Does this pass the four-point elegance check?
12. Does this meet the Definition of Done?

Report ALL findings. Tag each as BLOCKING, IMPORTANT, or NIT. Do not filter or self-censor based on perceived severity. For each finding give the file and line, what is wrong, and the smallest fix.

Do not rewrite the code. Return a structured list of findings, then a one-line verdict: APPROVE, APPROVE WITH NITS, or REQUEST CHANGES.
