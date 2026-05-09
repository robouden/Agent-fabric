---
name: to-prd
description: "Synthesize the current conversation and codebase context into a product requirements document and optionally file it as a GitHub issue."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# To PRD Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Context synthesis** — convert existing conversation context into a PRD without re-interviewing unnecessarily.
- **Module sketching** — identify major modules or behaviors likely to change.
- **User-story expansion** — write extensive actor-goal-benefit stories.
- **Decision capture** — record implementation, testing, and out-of-scope decisions in durable language.

## Workflow
1. Explore the repo if current codebase context is insufficient.
2. Sketch major modules or behavior areas and look for deep-module opportunities.
3. Check with the user on module and testing expectations before finalizing.
4. Draft a PRD with problem statement, solution, user stories, implementation decisions, testing decisions, out of scope, and notes.
5. File as a GitHub issue only when appropriate and authorized.

## When to Use
- The user asks to create a PRD from the conversation, prepare a product spec, or turn planning context into an issue.

