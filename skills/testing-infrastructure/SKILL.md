---
name: testing-infrastructure
description: "Test data management, fixtures, factories, Testcontainers, flakiness handling, and test pyramid strategy."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Testing Infrastructure Skill

## Capabilities
- **Test pyramid strategy** — define appropriate ratios of unit, integration, and e2e tests for the project
- **Test data management** — factories, fixtures, builders, and data generators for consistent test data
- **Testcontainers for integration tests** — spin up real databases, message queues, and services in Docker for integration tests
- **Mock/stub/spy strategies** — choose the right test double for each scenario
- **Test environment setup & teardown** — configure isolated test environments with proper cleanup
- **Flaky test detection & remediation** — identify, quarantine, and fix non-deterministic tests
- **Parallel test execution** — configure test runners for safe parallel execution
- **Code coverage analysis & reporting** — set coverage targets and integrate with CI pipelines
- **Snapshot testing** — capture and compare UI or data snapshots for regression detection
- **Contract testing** — verify API contracts between services with Pact or similar tools

## Best Practices
1. **Follow the test pyramid** — many unit tests, fewer integration tests, few e2e tests
2. **Use factories over fixtures for flexibility** — factories can generate variations; fixtures are static and brittle
3. **Isolate tests (no shared state)** — each test must set up and tear down its own data
4. **Use Testcontainers for real dependencies** — test against real databases and queues in integration tests, not just mocks
5. **Make tests deterministic** — no random values, no time-dependent assertions, no reliance on external services
6. **Name tests descriptively** — use the pattern `should_X_when_Y` or `given_X_when_Y_then_Z`
7. **Clean up test data after each test** — use transactions, truncation, or fresh containers
8. **Run tests in parallel where possible** — reduce CI feedback time by parallelizing test suites
9. **Track flaky tests and fix them immediately** — a flaky test is worse than no test; it erodes trust

## When to Use
- Setting up test infrastructure for a new project
- Designing a test strategy for a feature or service
- Managing test data with factories and fixtures
- Handling flaky tests in CI pipelines
- Implementing integration tests with real databases, queues, or external services
- Establishing coverage targets and reporting

## Anti-Patterns to Avoid
- ❌ Testing implementation details — test behavior and outputs, not internal method calls
- ❌ Shared mutable state between tests — leads to order-dependent failures
- ❌ Sleeping instead of waiting — use polling, events, or explicit waits
- ❌ Ignoring flaky tests — quarantine and fix them; never leave them in the suite
- ❌ Testing only happy paths — always include error cases, edge cases, and boundary conditions
- ❌ Over-mocking — testing mocks instead of real behavior provides false confidence
