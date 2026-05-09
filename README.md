# Agent Fabric

A curated catalog of [Hermes Agent CLI](https://hermes-agent.nousresearch.com/docs/user-guide/cli) skills covering software engineering, infrastructure, game development, narrative/audio/level design, documentation, security, testing, and operational triage. The repository contains **only skill configuration** — no application source code.

> **Origin:** Forked from [`aazenkoff/copilot-agent-fabric`](https://github.com/aazenkoff/copilot-agent-fabric) by @aazenkoff. The upstream targets GitHub Copilot CLI; this fork ports the entire skill set to Hermes Agent CLI on Codeberg. See the original repo for the pre-Hermes design intent and history.

Canonical home: [Codeberg](https://codeberg.org/YR-Design/Agent-fabric) · GitHub mirror (used for `hermes skills tap add`): [robouden/Agent-fabric](https://github.com/robouden/Agent-fabric).

## Architecture

```
Agent-fabric/
├── AGENTS.md                            # Project context loaded by Hermes (per context-files spec)
├── README.md                            # This file
├── registry.yaml                        # Human-readable manifest of every skill
├── skills/                              # All Hermes skills, one directory per skill
│   ├── orchestrator/SKILL.md            # 🎯 Coordinator persona for complex workflows
│   ├── agent-creator/SKILL.md           # 🆕 Authoring persona for new skills
│   ├── code-writer/SKILL.md             # ✍️  Code implementation persona
│   ├── code-reviewer/SKILL.md           # 🔍 Code review persona
│   ├── tester/SKILL.md                  # 🧪 Testing persona
│   ├── codeberg-triage/SKILL.md         # 🏷️  Codeberg/Forgejo issue triage
│   ├── file-operations/SKILL.md         # 📂 Read/write/search files
│   ├── docker/SKILL.md                  # 🐳 Container management
│   ├── kubernetes/SKILL.md              # ☸️  K8s manifests and deploy
│   └── ... (101 skills total)
├── templates/                           # Authoring templates
│   ├── agent-template.md                # For persona-style skills
│   └── skill-template.md                # For utility skills
└── docs/                                # Supporting documentation
```

Each skill directory contains a `SKILL.md` with Hermes-spec frontmatter (`name`, `description`, `version`, `metadata.hermes.category`). The `category` distinguishes provenance: `agent` (persona), `skill` (utility), or `prompt` (workflow template).

## Quick Start

### Prerequisites

- [Hermes Agent CLI](https://hermes-agent.nousresearch.com/docs) installed and configured
- A Hermes-compatible model provider configured in `~/.hermes/config.yaml`

### Install as a tap (GitHub mirror)

`hermes skills tap add` is GitHub-only per the Hermes docs, so installation goes through the GitHub mirror:

```bash
hermes skills tap add robouden/Agent-fabric
```

This makes every skill available as a slash command (e.g. `/orchestrator`, `/code-writer`, `/codeberg-triage`).

### Install individual skills directly

To pull a single skill straight from Codeberg:

```bash
hermes skills install https://codeberg.org/YR-Design/Agent-fabric/raw/branch/main/skills/orchestrator/SKILL.md --name orchestrator
```

### Use a skill in a session

```bash
hermes chat -s orchestrator -q "design and implement a save system for our PixiJS prototype"
hermes chat -s codeberg-triage -q "triage the open issues in this repo"
hermes chat -s code-reviewer -s tester -q "review the diff and add tests for the changed paths"
```

### Recommended Coordination Pattern

For multi-step work, start with the `orchestrator` skill — it knows about the persona-style skills and can suggest delegation. This is a **repo convention**, not a Hermes default.

## Available Persona Skills (formerly "agents")

The catalog includes core software-engineering personas plus a full game-development specialist roster (Unity, Unreal, Godot, PixiJS, Roblox, Blender). Tagged in registry as `category: agent`; full list in [`registry.yaml`](registry.yaml).

| Persona | Category | Notable bundled skills |
|---|---|---|
| **orchestrator** | meta | project-context, game-development, to-prd, to-issues, codeberg-triage, qa |
| **agent-creator** | meta | write-a-skill |
| **code-writer** | development | file-operations, code-generation, dependency-management, tdd, … |
| **code-reviewer** | quality | file-operations, code-analysis, security-audit, improve-codebase-architecture, … |
| **documenter** | documentation | file-operations, markdown-generation, edit-article, to-prd, ubiquitous-language |
| **devops** | operations | terminal-commands, docker, ci-cd, kubernetes, observability |
| **researcher** | analysis | web-search, code-analysis, zoom-out |
| **tester** | quality | code-generation, terminal-commands, testing-infrastructure, tdd, diagnose, qa |
| **code-investigator** | analysis | terminal-commands, code-analysis, diagnose, triage-issue, zoom-out |
| **seo-aeo-expert** | marketing | web-search, code-analysis, code-generation, markdown-generation, performance-optimization |
| **game-audio-engineer** | development | audio-middleware, elevenlabs-audio-generation, performance-optimization |
| **game-designer** | analysis | game-design-documentation, markdown-generation, testing-infrastructure |
| **level-designer** | analysis | game-design-documentation, markdown-generation, testing-infrastructure |
| **narrative-designer** | analysis | game-design-documentation, markdown-generation |
| **technical-artist** | development | shader-programming, openai-image-generation, png-optimization |
| **pixijs-prototype-specialist** | development | pixijs, frontend-frameworks, safari-testing, openai-image-generation |
| **pixijs-architect** | development | pixijs, code-generation, performance-optimization |
| **pixijs-multiplayer-engineer** | development | pixijs, api-design, testing-infrastructure |
| **pixijs-shader-developer** | development | pixijs, shader-programming, performance-optimization |
| **pixijs-tooling-developer** | development | pixijs, dependency-management, png-optimization |
| **unity-architect** | development | unity-scripting, unity-mcp-validation, testing-infrastructure |
| **unity-editor-tool-developer** | development | unity-scripting, unity-mcp-validation, terminal-commands |
| **unity-gameplay-developer** | development | unity-scripting, unity-mcp-validation, unity-game-testing |
| **unity-multiplayer-engineer** | development | unity-scripting, api-design, performance-optimization |
| **unity-shader-graph-artist** | development | unity-scripting, shader-programming |
| **godot-gameplay-scripter** | development | godot-gdscript, testing-infrastructure |
| **godot-multiplayer-engineer** | development | godot-gdscript, api-design, performance-optimization |
| **godot-shader-developer** | development | godot-gdscript, shader-programming |
| **unreal-multiplayer-architect** | development | unreal-cpp, api-design, performance-optimization |
| **unreal-systems-engineer** | development | unreal-cpp, dependency-management, performance-optimization |
| **unreal-technical-artist** | development | unreal-cpp, shader-programming, performance-optimization |
| **unreal-world-builder** | development | unreal-cpp, performance-optimization |
| **blender-addon-engineer** | development | blender-python, terminal-commands, testing-infrastructure |
| **roblox-avatar-creator** | development | luau, markdown-generation |
| **roblox-experience-designer** | analysis | luau, database-operations, api-design |
| **roblox-systems-scripter** | development | luau, database-operations, api-design |

## Available Utility Skills

Reusable capability skills (`category: skill`). Full descriptions in [`registry.yaml`](registry.yaml).

| Skill | Purpose |
|---|---|
| project-context | Persistent registry of named local project paths |
| file-operations | Read, write, search, navigate files |
| terminal-commands | Execute shell commands and scripts |
| code-generation | Generate code following project patterns |
| code-analysis | Analyze structure, detect issues |
| web-search | Search docs and best practices |
| security-audit | Vulnerability and secret scanning |
| dependency-management | Manage deps, check CVEs |
| markdown-generation | Markdown with diagrams |
| docker | Dockerfiles, compose, containers |
| ci-cd | Pipelines (Forgejo Actions on Codeberg, GitHub Actions on the mirror) |
| kubernetes | K8s manifests, ingress, deployment |
| git-workflow | Branch, commit, push, PR via git + forge API |
| database-operations | Schema, migrations, query optimization |
| api-design | REST/GraphQL, OpenAPI, versioning |
| frontend-frameworks | React/Vue/Angular/Svelte patterns |
| landing-page-creation | Marketing microsites with semantic responsive UX |
| pixijs | PixiJS v8 patterns: logical resolution, viewport fitting, safe areas |
| observability | Logging, metrics, tracing, alerting |
| performance-optimization | Profiling, caching, load testing |
| testing-infrastructure | Fixtures, Testcontainers, test pyramid |
| safari-testing | Safari/WebKit QA via MCP, console/network diagnostics |
| openai-image-generation | Game UI assets and concept art via OpenAI Images |
| reference-based-image-generation | Visually consistent image sets from a reference |
| elevenlabs-audio-generation | Short game-ready SFX and bark voice via ElevenLabs |
| unity-scripting | Unity C# patterns |
| unity-mcp-validation | Live editor/runtime validation via Unity MCP |
| unity-game-testing | Play mode coverage, console triage, screenshots |
| godot-gdscript | GDScript patterns for Godot 4 |
| unreal-cpp | UE C++/Blueprint patterns and GAS |
| shader-programming | GLSL/HLSL/ShaderLab cross-platform |
| audio-middleware | FMOD/Wwise integration |
| game-design-documentation | GDD outlines, mechanic specs, playtest checkpoints |
| luau | Roblox Luau patterns |
| blender-python | Blender add-ons and asset automation via bpy |
| game-development | Phased PixiJS delivery template |
| png-optimization | Lossy/lossless PNG compression |
| mempalace-memory | Persistent cross-session memory via MemPalace MCP |
| disciplined-coding | Deliberate-thinking behavioral guidelines |
| caveman | Ultra-compressed terse mode |
| design-an-interface | Compare radically different interface designs |
| diagnose | Disciplined diagnosis loop for hard bugs |
| domain-model | Stress-test plans against domain language |
| edit-article | Restructure and tighten article drafts |
| codeberg-triage | Codeberg/Forgejo issue triage via the API |
| grill-me | One-question-at-a-time interview |
| improve-codebase-architecture | Find locality/leverage/testability wins |
| qa | Conversational QA → durable issues |
| request-refactor-plan | Refactor as tiny safe commits |
| tdd | Vertical red-green-refactor cycles |
| to-issues | PRD → independently grabbable issues |
| to-prd | Conversation → product requirements doc |
| triage-issue | Bug → root cause → TDD fix plan |
| ubiquitous-language | DDD glossary maintenance |
| write-a-skill | Author new Hermes skills |
| zoom-out | High-level map of unfamiliar code |

## Workflow Skills (formerly "prompts")

Reusable end-to-end task templates (`category: prompt`).

| Workflow | Purpose |
|---|---|
| build-feature | End-to-end feature delivery |
| build-figma-prototype | Figma design → interactive prototype |
| create-agent | Author a new persona-style skill |
| deploy-to-production | Production rollout checklist |
| refactor | Plan and execute a refactor in safe commits |
| review-code | Structured code review |
| setup-auth | Stand up authentication |
| setup-database | Schema, migrations, seeding, backups |
| setup-infra | CI, deployment, observability from scratch |

## Code Quality Workflow

All code changes flow through a code review cycle:

**Implementation persona → code-reviewer → fix issues → verify → done**

This keeps quality consistent and surfaces issues before merge. See [Architecture Guide](docs/architecture.md#code-quality-workflow) for the diagram.

## MCP Server Support

Hermes can extend skills via [Model Context Protocol](https://modelcontextprotocol.io/) servers. The catalog references these where useful:

- **Chrome DevTools MCP** — Browser automation, UI testing, performance audits
- **Safari MCP (optional/local)** — Safari/WebKit automation when configured
- **Docker MCP** — Container management, isolated environments
- **Figma MCP** — Design-to-code, asset extraction, visual QA, Code Connect
- **Unity MCP** — Live Unity Editor/runtime inspection
- **MemPalace MCP** — Persistent cross-session memory (semantic search, KG, diary)

See [Docker MCP Guide](docs/docker-mcp-guide.md), [Figma MCP Guide](docs/figma-mcp-guide.md), and the [Unity MCP project](https://github.com/CoplayDev/unity-mcp) for setup.

## Documentation

| Document | Description |
|---|---|
| [AGENTS.md](AGENTS.md) | Project context loaded by Hermes |
| [Architecture](docs/architecture.md) | System design and coordination model |
| [Agent Guide](docs/agent-guide.md) | How to select, use, create, and customize personas |
| [Docker MCP Guide](docs/docker-mcp-guide.md) | Docker MCP setup and reference |
| [Figma MCP Guide](docs/figma-mcp-guide.md) | Figma MCP setup and reference |
| [PixiJS LLM Reference](docs/pixijs-llms-reference.md) | PixiJS v8 guidance |
| [MemPalace Guide](docs/mempalace-guide.md) | Persistent cross-session memory |

## Customization

### Adding a new persona

Use the `agent-creator` skill: `hermes chat -s agent-creator -q "create a persona for a new role"`. Or manually:

1. Copy [`templates/agent-template.md`](templates/agent-template.md) into `skills/<your-persona-name>/SKILL.md`
2. Fill in responsibilities, guidelines, output format
3. Add an entry under `agents:` in [`registry.yaml`](registry.yaml)
4. If applicable, add it to the orchestrator's coordination references

### Adding a new utility skill

1. Copy [`templates/skill-template.md`](templates/skill-template.md) into `skills/<your-skill-name>/SKILL.md`
2. Fill in capabilities, best practices, and when-to-use
3. Add an entry under `skills:` in [`registry.yaml`](registry.yaml)
4. Optionally reference it from any persona's `skills:` array

See the [Agent Guide](docs/agent-guide.md) for detail.

## License

MIT.

Persona/skill content adapted from upstream sources where noted (e.g. `mattpocock/skills`, `msitarzewski/agency-agents`); see individual SKILL.md files for attribution.
