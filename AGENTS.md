# Agent Fabric — Project Instructions

This file is the project-level context loaded by Hermes Agent CLI (per the [context-files docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/context-files)). Hermes walks to the git root and reads `AGENTS.md` (or `HERMES.md` / `.hermes.md` if you prefer those names).

## Project Overview

Agent Fabric is a curated collection of Hermes-compatible skills covering coding, infrastructure, game development (Unity, Unreal, Godot, Roblox, PixiJS, Blender), narrative/audio/level design, documentation, security audit, testing, and operational triage. Each skill is a self-contained directory under `skills/`.

This repository is **agent-config-only**. It contains no application source code or third-party dependencies — only skill definitions, templates, and supporting docs.

## Repository Layout

```
Agent-fabric/
├── AGENTS.md                 # this file — project context for Hermes
├── README.md                 # human-facing overview
├── registry.yaml             # human-readable manifest of skills (kept for discoverability;
│                             # Hermes auto-discovers via the skills/ directory)
├── skills/                   # all Hermes skills, one directory per skill
│   ├── <skill-name>/
│   │   └── SKILL.md          # required; YAML frontmatter + body
│   ├── orchestrator/
│   ├── code-writer/
│   ├── codeberg-triage/
│   └── ...
├── templates/                # SKILL.md authoring templates
│   ├── agent-template.md
│   └── skill-template.md
└── docs/                     # supporting documentation
```

## Coordination Pattern

Many skills in this repo carry a "persona" inherited from the previous Copilot CLI agent layout (see the `metadata.hermes.category` field in each `SKILL.md` — values are `agent`, `skill`, or `prompt`). The `orchestrator` skill is intended as the entry point for complex multi-step work; it knows about the other persona-style skills and can suggest delegation.

In Hermes, all of these are loaded the same way — via `-s <skill>` at startup, or installed and invoked as a slash command:

```bash
hermes chat -s orchestrator -q "design and implement a save system for our PixiJS prototype"
hermes chat -s codeberg-triage -q "triage open issues in this repo"
```

## Conventions

- Skill directories use lowercase kebab-case names matching the slash command they expose.
- Frontmatter follows Hermes spec: `name`, `description`, `version` required; `metadata.hermes.category` distinguishes provenance.
- The `version` field starts at `0.1.0` for ported skills; bump per semver on substantive changes.
- Documentation lives under `docs/`.

## Token Efficiency Guidance for Skills

When skill bodies instruct Hermes how to operate:

- Read files with line ranges or grep — don't load whole files when a slice will do.
- Batch independent tool calls into a single turn.
- Prefer concise output; do not echo file contents the user has already seen.
- Stop investigating as soon as you have enough to act.

## Distribution

Codeberg (`https://codeberg.org/YR-Design/Agent-fabric`) is the canonical source. A mirror lives at GitHub (`https://github.com/robouden/Agent-fabric`) so users can install via Hermes's GitHub-only `tap add`:

```bash
hermes skills tap add robouden/Agent-fabric
```

Individual skills can also be installed directly from Codeberg by URL:

```bash
hermes skills install https://codeberg.org/YR-Design/Agent-fabric/raw/branch/main/skills/<name>/SKILL.md
```

## Project Registry

Skills that operate on user codebases (e.g. `code-writer`, `tester`) rely on the `project-context` skill to track which projects exist and where they live on disk. Check that skill before asking the user for paths.

## Repository Protection

- Direct edits to other people's projects must come through their normal review process — do not push to repositories outside of `skills/` here.
- Substantive changes to this repository should go via PR (Codeberg or GitHub mirror).

## See Also

- `README.md` — human-facing overview.
- `registry.yaml` — flat manifest of every skill with its category.
- `docs/` — design notes and adapted-skill provenance.
- Hermes docs: <https://hermes-agent.nousresearch.com/docs>
