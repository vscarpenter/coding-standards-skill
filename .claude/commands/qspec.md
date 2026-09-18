Write a spec for the described feature. Follow the Spec-Driven Development standards from the coding-standards skill.

Before requesting approval, run the unknowns interview: ask up to three questions per turn, highest blast radius first (data models, architecture, public interfaces). Fold the answers into the spec.

Include:
- **Goal:** One sentence describing what this does and why
- **Inputs / Outputs:** What goes in, what comes out, what format
- **Constraints:** Performance, security, compatibility, or size requirements
- **Edge Cases:** Empty inputs, nulls, concurrent calls, failure modes
- **Out of Scope:** Explicit list of what this version does not handle
- **Acceptance Criteria:** Checkable statements that prove the implementation is correct
- **Test Stubs:** Draft test function names with empty bodies, at least one per acceptance criterion

Save the spec to `tasks/spec.md`.

Do not write any code. Wait for spec approval before proceeding.
