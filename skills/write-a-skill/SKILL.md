---
name: write-a-skill
description: "Create reusable agent skills with clear triggers, concise instructions, progressive disclosure, and optional reference resources."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Write a Skill Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Skill scoping** — decide whether the need is a reusable capability or an agent persona.
- **Trigger design** — write descriptions that tell agents exactly when to load the skill.
- **Instruction structure** — create concise workflows, checklists, and examples.
- **Resource discipline** — include reference material or scripts only when they are necessary and safe for the repository boundary.

## Workflow
1. Gather task/domain, use cases, needed tools, and reference material.
2. Draft `skills/<skill-name>.skill.md` using local front matter: `description` and `name`.
3. Keep the main skill focused; split large or rarely used material only if the repo convention supports it.
4. Register the skill in `registry.yaml`.
5. Assign it conservatively to agents that need the capability.
6. Validate front matter, registry consistency, and documentation updates.

## When to Use
- Creating or revising a skill in this agent-fabric repository.
- Reviewing whether proposed agent behavior belongs in a skill instead of an agent.

