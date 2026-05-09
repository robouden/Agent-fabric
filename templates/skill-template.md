# Utility Skill Template

Use this template for ordinary utility skills — reusable capabilities that
agent-style skills can compose (e.g. `file-operations`, `code-analysis`,
`docker`). These are tagged with `metadata.hermes.category: skill`.

## How to use

1. Create a new directory under `skills/`: `skills/<your-skill-name>/`
2. Copy the SKILL.md block below into `skills/<your-skill-name>/SKILL.md`
3. Replace placeholders.
4. Add an entry under `skills:` in `registry.yaml`.
5. Reference the skill in any agent's `skills:` array in the registry.

## Naming conventions

- Directory and `name` field both use lowercase kebab-case.
- The `name` becomes the slash command in Hermes (`/<skill-name>`).
- `version` starts at `0.1.0`; bump per semver on substantive changes.

## Optional supporting files

A skill directory may contain (per Hermes spec):
- `references/` — extended docs the skill can pull in
- `templates/` — output formats
- `scripts/` — helper scripts
- `assets/` — supplementary files

## SKILL.md template

```markdown
---
name: <skill-name>
description: "<One-line description of what this skill enables.>"
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# <Skill Name> Skill

## Capabilities
- **<Capability>** — <what it does>
- **<Capability>** — <what it does>

## Best Practices
1. **<Practice>** — <explanation>
2. **<Practice>** — <explanation>

## When to Use
- <Scenario where this skill is needed>
- <Another scenario>
```
