---
name: grill-me
description: "Interview the user relentlessly about a plan or design, one question at a time, until shared understanding is reached."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Grill Me Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Decision-tree exploration** — walk through assumptions, branches, dependencies, and edge cases.
- **Recommended answers** — provide a proposed answer with each question to speed alignment.
- **Codebase-backed questioning** — answer questions by inspecting the codebase when possible instead of asking the user.

## Best Practices
1. Ask one question at a time.
2. Prefer questions that unblock later decisions.
3. Stop asking once the design is specific enough to plan or implement.
4. Do not use the skill as a substitute for code investigation when the repository can answer the question.

## When to Use
- The user asks to be grilled, stress-test a plan, pressure-test a design, or expose hidden assumptions.

