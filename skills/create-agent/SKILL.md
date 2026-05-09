---
name: create-agent
description: "Prompt for creating a new specialized agent"
version: 0.1.0
metadata:
  hermes:
    category: prompt
---
# Create New Agent

## Agent Details
- **Name**: {{AGENT_NAME}}
- **Purpose**: {{AGENT_PURPOSE}}
- **Category**: {{CATEGORY}} (meta | development | quality | documentation | operations | analysis)

## Workflow Note
- Use the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`) to select **agent-creator** for the repo changes.
- Keep repository edits with **agent-creator**; other agents may be consulted, but they should not modify this repo directly.
- Use `@` only for file/path mentions.

## Steps
1. **Agent Creator** — create the agent definition file at `skills/{{AGENT_NAME}}.agent.md`.
2. **Agent Creator** — register the agent in `registry.yaml`.
3. **Agent Creator** — update the orchestrator's agent table in `skills/orchestrator/SKILL.md`.
4. **Agent Creator** — update the README if the new agent should be listed for users.
