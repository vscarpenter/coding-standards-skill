# Testing & Error Handling Reference

## Testing Standards

- Write tests BEFORE implementation when feasible (TDD approach)
- Test all public APIs and critical paths (target approximately 80% coverage)
- Use clear behavior-based test names (e.g., `should_return_404_when_user_not_found`)
- Follow Arrange-Act-Assert pattern
- Include positive and negative cases
- Run the full test suite after every modification; do not proceed with failing tests

## Test Isolation & Strategy

- Each test must be independent; no shared mutable state between tests
- Mock external dependencies (network, filesystem, databases) at the boundary, not deep in the call stack
- Prefer fakes and in-memory implementations over mocks when feasible
- Keep unit tests fast (< 100ms each); move slow tests to integration suites
- Use factory functions or builders for test data; never copy-paste fixture objects
- Test behavior, not implementation; tests should survive internal refactors

## Error Handling

- Fail fast with clear messages
- Never swallow exceptions
- Use typed/custom errors for domain-specific failures (distinguish "not found" from "unauthorized" from "validation failed")
- Log with context (no secrets)
- Retry transient failures with exponential backoff; use circuit breakers for dependencies
- Return meaningful error responses to callers: status code, error type, human-readable message
