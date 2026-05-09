---
name: qa
description: "Run conversational QA sessions where user-reported bugs are clarified, scoped, and filed as durable Codeberg/GitHub issues."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# QA Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Conversational bug intake** — let the user report issues naturally, then ask only a few focused clarifying questions.
- **Background context gathering** — inspect relevant code and domain language to write better issues without overloading the user.
- **Issue scoping** — decide whether a report is one issue or should be split into independent slices.
- **Durable issue writing** — describe observable behavior, expectations, reproduction steps, and domain context without brittle file references.

## Workflow
1. Capture expected vs actual behavior and reproduction steps.
2. Explore enough of the codebase to learn domain wording and behavior boundaries.
3. Split multi-symptom reports only when the parts are independently fixable or verifiable.
4. Create Codeberg/GitHub issues with the forge API (Forgejo on Codeberg, `gh` on GitHub) when the user has authorized filing issues in the target repo.
5. Share issue URLs and continue until the user says QA is done.

## When to Use
- Manual QA sessions, bug-report filing, acceptance testing notes, or user-guided product validation.

