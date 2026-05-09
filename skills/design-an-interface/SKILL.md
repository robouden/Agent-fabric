---
name: design-an-interface
description: "Generate and compare multiple radically different interface designs for a module before implementation."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Design an Interface Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Design-it-twice exploration** — produce multiple interface shapes because the first idea is rarely best.
- **Parallel option generation** — use subagents or separate reasoning threads for deliberately different designs.
- **Trade-off synthesis** — compare simplicity, depth, flexibility, implementation efficiency, and misuse risk.

## Workflow
1. **Gather requirements** — identify callers, core operations, constraints, and what complexity should be hidden.
2. **Generate alternatives** — create at least three designs with different biases, such as minimal method count, maximum flexibility, or optimized common path.
3. **Show usage** — present each interface with signatures, realistic caller examples, and the complexity hidden behind it.
4. **Compare in prose** — explain where designs diverge and what each makes easy or risky.
5. **Synthesize** — combine the strongest ideas only after the user selects the direction.

## When to Use
- Designing APIs, modules, adapters, service boundaries, or test seams.
- The user asks to "design it twice", compare interface options, or avoid premature implementation.

## Boundaries
- Do not implement during this skill unless the user explicitly approves a chosen design.
- Do not let alternatives collapse into minor variations of the same interface.

