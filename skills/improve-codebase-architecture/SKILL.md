---
name: improve-codebase-architecture
description: "Find codebase deepening opportunities that improve locality, leverage, testability, and AI navigability."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Improve Codebase Architecture Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Architecture friction discovery** — identify shallow modules, weak seams, tangled callers, and low-locality behavior.
- **Deep module framing** — propose smaller interfaces that hide more implementation complexity.
- **Domain-informed naming** — use project context docs and ADRs when naming seams and modules.
- **Refactor candidate reporting** — present opportunities before designing or implementing changes.

## Vocabulary
- **Module** — anything with an interface and implementation.
- **Interface** — everything callers must know to use the module, not just type signatures.
- **Depth** — leverage created by a small interface hiding substantial behavior.
- **Seam** — a place behavior can vary without editing callers.
- **Adapter** — a concrete implementation satisfying an interface at a seam.
- **Locality** — change, bugs, and knowledge concentrated in one place.

## Workflow
1. Read `CONTEXT.md`, `CONTEXT-MAP.md`, and relevant ADRs if they exist.
2. Explore organically for friction: repeated caller knowledge, pass-through modules, missing test seams, and coupling across seams.
3. Apply the deletion test: if deleting the module removes complexity, it was likely shallow; if complexity spreads to callers, it had leverage.
4. Present numbered opportunities with files/modules, problem, solution, and benefits.
5. Ask which candidate to explore before proposing concrete interfaces or code changes.

## When to Use
- Architecture reviews, refactor discovery, testability improvements, or making unfamiliar codebases easier for agents to navigate.

