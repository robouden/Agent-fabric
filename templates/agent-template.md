# Agent (Persona) Skill Template

Use this template when porting or authoring a "persona" — a Hermes skill that
carries an agent identity (e.g. `code-writer`, `code-reviewer`, `tester`).
Personas are tagged with `metadata.hermes.category: agent` so the registry can
distinguish them from utility skills.

## How to use

1. Create a new directory under `skills/`: `skills/<your-agent-name>/`
2. Copy the SKILL.md block below into `skills/<your-agent-name>/SKILL.md`
3. Replace placeholders.
4. Add an entry under `agents:` in `registry.yaml`.
5. If your agent should be discoverable by the orchestrator, add it to the
   orchestrator's skill list and ensure relevant utility skills are referenced.

## Naming conventions

- Directory and `name` field both use lowercase kebab-case (e.g. `unity-architect`).
- The `name` becomes the slash command in Hermes (`/unity-architect`).
- `version` starts at `0.1.0`; bump per semver on substantive changes.

## SKILL.md template

```markdown
---
name: <agent-name>
description: "<One-line description of what this agent does and when to use it.>"
version: 0.1.0
metadata:
  hermes:
    category: agent
---
# <Agent Name> Agent

You are the **<Agent Name>** agent — <role description>.

## Responsibilities
- <Primary responsibility>
- <Secondary responsibility>
- <Additional responsibilities>

## Guidelines
1. **<Principle>** — <explanation>
2. **<Principle>** — <explanation>
3. **<Principle>** — <explanation>

## Output Format
- <How the agent should format its responses>
- <Where to place generated artifacts>
```
