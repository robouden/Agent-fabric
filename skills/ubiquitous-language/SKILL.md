---
name: ubiquitous-language
description: "Extract and maintain a DDD-style ubiquitous language glossary from conversation and codebase context."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Ubiquitous Language Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Term extraction** — identify domain-relevant nouns, verbs, relationships, and lifecycle concepts.
- **Ambiguity detection** — flag overloaded terms and competing synonyms.
- **Canonical glossary writing** — propose clear terms, definitions, aliases to avoid, relationships, and example dialogue.
- **Glossary maintenance** — update an existing `UBIQUITOUS_LANGUAGE.md` or equivalent domain glossary when understanding evolves.

## Best Practices
1. Be opinionated: pick one canonical term and list weaker aliases.
2. Keep definitions to one sentence and meaningful to domain experts.
3. Show relationships and cardinality where obvious.
4. Skip generic programming concepts unless they carry domain meaning.
5. Do not overwrite project language without surfacing the change to the user.

## When to Use
- Domain modeling, DDD discussions, glossary creation, or plans where naming ambiguity is blocking progress.

