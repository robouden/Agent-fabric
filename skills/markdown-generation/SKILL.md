---
name: markdown-generation
description: "Generate well-structured Markdown documents with tables, code blocks, Mermaid diagrams, and front matter."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Markdown Generation Skill

## Capabilities
- **Structured documents** — README files, guides, ADRs
- **API documentation** — endpoint docs, parameter tables
- **Diagrams** — Mermaid flowcharts, sequence diagrams, ER diagrams
- **Tables** — comparison tables, feature matrices
- **Front matter** — YAML metadata for templates and prompts

## Best Practices
1. Use a clear heading hierarchy (H1 → H2 → H3, don't skip levels).
2. Include a table of contents for documents longer than 3 sections.
3. Use code blocks with language hints for syntax highlighting.
4. Use Mermaid for diagrams — they render natively on GitHub.
5. Keep lines under 120 characters for readability in diffs.
6. Use relative links for internal references.

## Mermaid Diagram Types
- `graph TD` — flowcharts and architecture diagrams
- `sequenceDiagram` — interaction flows
- `classDiagram` — class relationships
- `erDiagram` — data models
- `gantt` — project timelines
- `stateDiagram-v2` — state machines

## When to Use
- Creating or updating README files.
- Writing architecture and design documents.
- Generating API documentation.
- Creating agent and skill definition files.

