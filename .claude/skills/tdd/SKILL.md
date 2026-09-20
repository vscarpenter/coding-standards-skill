---
name: tdd
description: Start a red/green/refactor cycle for one behavior, pausing for approval after the failing test and before the refactor. Follows the TDD protocol from the coding-standards skill.
disable-model-invocation: true
argument-hint: <behavior to implement>
---
Start a red/green/refactor cycle for the behavior described in the arguments. Follow the TDD protocol from the coding-standards skill.

Behavior: $ARGUMENTS

Execute the cycle strictly in order:

1. **Red.** Write a failing test for the described behavior. Use a behavior-based name (e.g., `should_return_404_when_user_not_found`). Follow Arrange-Act-Assert. Run the test and confirm it fails for the right reason — not a syntax error, missing import, or typo. Report the failure output.
2. **Pause.** Stop and wait for my approval of the test before writing any implementation. Do not proceed to green without confirmation.
3. **Green.** Write the minimum implementation needed to make the test pass. No extra features, no speculative abstractions, no unrelated edits. Run the test and confirm it passes.
4. **Refactor.** Propose a refactor pass: extract duplication, improve naming, simplify logic. Do not change behavior. Wait for confirmation before applying the refactor.
5. **Repeat.** If the behavior requires multiple cycles, state the next failing test and return to step 1.

Rules:
- Do not write implementation code before the test exists and has been confirmed to fail.
- Do not bundle multiple behaviors into one cycle.
- Solve the problem generally. Do not hard-code to the test cases.
- If you cannot write a failing test first, stop and say the requirement is unclear.
