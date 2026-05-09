---
name: tdd
description: "Test-driven development using vertical red-green-refactor cycles that test observable behavior through public interfaces."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# TDD Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Behavior-first tests** — specify what the system does through public interfaces, not internal implementation.
- **Vertical slices** — one test, one minimal implementation, repeat; never write all tests before all code.
- **Refactor safety** — keep tests resilient to internal restructuring.
- **Deep-module awareness** — use the test surface to reveal better interfaces and seams.

## Workflow
1. Confirm the public interface and highest-value behaviors.
2. Write one failing test for one observable behavior.
3. Implement the minimal code to pass that test.
4. Repeat for the next behavior, learning from the previous cycle.
5. Refactor only when green, then rerun tests after each step.

## Anti-Patterns
- Testing private methods, internal collaborators, or database state when a public behavior exists.
- Horizontal slicing: writing many tests first and then implementing everything.
- Mocking internals so heavily that tests verify mocks rather than behavior.

## When to Use
- Building features, fixing bugs, or refactoring behavior where tests should drive implementation.

