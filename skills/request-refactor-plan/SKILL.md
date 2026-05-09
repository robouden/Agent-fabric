---
name: request-refactor-plan
description: "Create a detailed refactor plan broken into tiny safe commits and capture it as a Codeberg/GitHub issue or planning artifact."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Request Refactor Plan Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Refactor scoping** — turn vague refactor intent into exact in-scope and out-of-scope changes.
- **Option challenge** — verify the user's assumptions against the repo and present alternatives.
- **Tiny-commit planning** — break work into small commits that leave the system working after each step.
- **Testing decisions** — identify current coverage and decide what must protect the refactor.

## Workflow
1. Ask for the problem and any solution ideas.
2. Explore the repo to verify the current state.
3. Present alternative approaches and trade-offs.
4. Interview until scope, interfaces, and testing strategy are clear.
5. Produce a refactor plan with problem statement, solution, tiny commits, decisions, testing, and out-of-scope notes.
6. File a Codeberg/GitHub issue only when appropriate for the target repo and user workflow.

## When to Use
- Planning non-trivial refactors, creating refactor RFCs, or preparing safe incremental work for agents.

