---
name: refactor
description: "Prompt for refactoring and cleaning up technical debt"
version: 0.1.0
metadata:
  hermes:
    category: prompt
---
# Refactor Code

## Context
The following area needs refactoring: **{{TARGET_AREA}}**

## Reason
{{REASON_FOR_REFACTORING}}

## Constraints
- Must not change existing behavior
- Existing tests must continue to pass

## Workflow Note
- Use the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`) to select the role for each step.
- For multi-step coordination, you can choose the orchestrator workflow.
- Use `@` only for file and path mentions.

## Steps
1. **Tester** — verify existing test coverage and add tests for uncovered code paths to establish a safety net.
2. **Code Reviewer** — identify code smells (duplication, long methods, deep nesting, tight coupling) and produce a prioritized refactoring plan.
3. **Framework/engine specialist** (for Unity gameplay or systems, use **Unity Gameplay Developer**) or **Code Writer** — implement the refactoring changes from the plan, one pattern at a time (extract method, rename, decompose, etc.).
4. **Tester** — verify all tests still pass after each refactoring step.
5. **Code Reviewer** — perform a final review of the refactored code for quality, readability, and adherence to the plan.
