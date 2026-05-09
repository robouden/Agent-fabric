---
name: zoom-out
description: "Give a higher-level map of unfamiliar code areas, relevant modules, callers, and how pieces fit together."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Zoom Out Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Context elevation** — move up one abstraction layer when details are overwhelming.
- **Module mapping** — identify relevant modules, callers, dependencies, and ownership of behavior.
- **Orientation before action** — help agents and users understand where a change fits before editing.

## Workflow
1. Pause detailed implementation.
2. Explore the surrounding modules and callers.
3. Summarize the system map in plain language.
4. Highlight the few seams or entry points most relevant to the user's task.
5. Recommend the next narrow investigation or implementation step.

## When to Use
- The user or agent is unfamiliar with a code area.
- A debugging or refactoring task needs broader context before local changes.

