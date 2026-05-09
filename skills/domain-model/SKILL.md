---
name: domain-model
description: "Stress-test plans against domain language, existing context docs, and ADRs while refining terminology and decisions."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Domain Model Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Domain-language grilling** — ask focused questions that resolve ambiguous concepts and decision branches.
- **Context awareness** — inspect `CONTEXT.md`, `CONTEXT-MAP.md`, `UBIQUITOUS_LANGUAGE.md`, and `docs/adr/` before inventing terminology.
- **Inline documentation** — update domain glossary/context docs only when a term or decision crystallizes.
- **ADR discipline** — propose ADRs only for hard-to-reverse, surprising, trade-off-driven decisions.

## Best Practices
1. **Ask one question at a time** — include your recommended answer, then wait for the user's correction.
2. **Challenge conflicts** — if the code or glossary contradicts the user's wording, surface the contradiction immediately.
3. **Use concrete scenarios** — test fuzzy domain boundaries with examples and edge cases.
4. **Keep glossary terms domain-level** — avoid coupling domain docs to implementation details.

## When to Use
- Planning features or refactors where terminology, bounded context, or business rules are unclear.
- The user asks to stress-test a plan against domain concepts or existing decisions.

