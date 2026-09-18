Review all changed files as a skeptical staff engineer. Apply the full coding-standards skill.

For every major code change, evaluate:

1. Does this follow our coding standards?
2. Are there comprehensive tests with positive and negative cases, written before the implementation (check commit order)?
3. Is error handling adequate with typed errors?
4. Does this maintain existing codebase patterns?
5. Are there any security concerns?
6. Is the code maintainable and readable by a junior engineer?
7. Are types properly annotated on all function signatures?
8. Is logging/observability adequate for production?
9. Was a verification method defined and does it pass?
10. Does every spec acceptance criterion have a corresponding passing test?
11. Does this pass the exit checklist for its tier (Part 7 of the skill)?

Report all findings. Tag each as BLOCKING, IMPORTANT, or NIT. Do not filter or self-censor based on perceived severity.

Do not rewrite the code. Return a structured list of findings.
