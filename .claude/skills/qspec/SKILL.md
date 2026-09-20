---
name: qspec
description: Write a feature spec with acceptance criteria and test stubs before any code is written, per the coding-standards skill. Saves to tasks/spec.md and waits for approval.
disable-model-invocation: true
argument-hint: <feature to spec>
---
Write a spec for the feature described below. Follow the Spec-Driven Development standards from the coding-standards skill.

Feature: $ARGUMENTS

Include:
- **Goal:** One sentence describing what this does and why
- **Inputs / Outputs:** What goes in, what comes out, what format
- **Constraints:** Performance, security, compatibility, or size requirements
- **Edge Cases:** Empty inputs, nulls, concurrent calls, failure modes
- **Out of Scope:** Explicit list of what this version does not handle
- **Acceptance Criteria:** Checkable statements that prove the implementation is correct
- **Test Stubs:** Draft test function names with empty bodies, one or more per acceptance criterion, using behavior-based names (`should_return_404_when_user_not_found`)

If the feature description is ambiguous, list the interpretations and ask which is intended before writing the spec.

Save the spec to `tasks/spec.md`.

Do not write any code. Wait for spec approval before proceeding.
