---
name: mempalace-memory
description: "Query and store persistent project knowledge using the MemPalace MCP server for cross-session memory."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# MemPalace Memory Skill

## Purpose

Give agents persistent, searchable memory across sessions. MemPalace stores knowledge as **drawers** organized into **wings** (projects) and **rooms** (topics), with a **knowledge graph** for entity relationships and a **diary** for session journaling.

## Available MCP Tools

| Tool | Purpose | Phase |
|------|---------|-------|
| `mempalace_status` | Palace overview (wings, rooms, drawer counts) | Session Start |
| `mempalace_diary_read` | Read past diary entries | Session Start |
| `mempalace_kg_query` | Query entity relationships and competency lessons | Session Start / During Work |
| `mempalace_search` | Semantic search across drawers (wing/room filtered) | During Work |
| `mempalace_kg_timeline` | Chronological fact history | During Work |
| `mempalace_check_duplicate` | Check if content already exists before storing | During Work |
| `mempalace_add_drawer` | Store new knowledge | During Work / Session End |
| `mempalace_kg_add` | Add entity relationship | Session End |
| `mempalace_kg_invalidate` | Mark a fact as no longer true | Session End |
| `mempalace_diary_write` | Write session diary (natural language) | Session End |
| `mempalace_list_wings` | List all wings | Any time |

## Wing and Room Convention

Wings map to **projects**. Rooms map to **topic areas** within a project.

| Wing | Project |
|------|---------|
| `tower-defense` | Tower Defense Unity game |
| `scary-hotel` | ScaryHotel Unity game |
| `agent-fabric` | This agent management repo |
| `tooling` | Cross-project environment and tools |
| `mempalace_sessions` | Cross-project session index (see Session End §6) |

Standard rooms (use these consistently):

| Room | Content |
|------|---------|
| `architecture` | Core systems, patterns, data flow |
| `economy` | Currency, costs, resource systems |
| `balance` | Tuning values, stats, wave composition |
| `gameplay` | Mechanics, combat, interactions |
| `bots` | AI behavior, roles, pathfinding |
| `enemies` | Enemy types, spawning, behavior |
| `bugs` | Critical bugs found and fixed |
| `ui` | HUD, menus, input handling |
| `editor` | Editor tools, bootstrappers |
| `art` | Visual style, sprite generation |
| `timeline` | Development chronology |
| `environment` | Dev machine, versions, paths |
| `mcp` | MCP server configuration |
| `projects` | Project registry |
| `agents` | Agent definitions and conventions |
| `session-index` | Session summaries (in `mempalace_sessions` wing only) |

---

## Complete Lifecycle

Every agent with this skill follows a three-phase protocol: **Session Start → During Work → Session End**.

### SESSION START (automatic)

Run these steps at the beginning of every session, before any task work:

```text
SESSION START
│
├─ 1. Prepare env safely                   (orchestrator loads vetted env vars)
├─ 2. mempalace_status                     → palace overview
├─ 3. mempalace_diary_read(last_n=5)       → last 5 session diaries
├─ 4. mempalace_kg_query(entity=<agent>)   → competency lessons for task domain
└─ 5. Detect project context               (registry lookup + AGENTS.md)
```

**Step-by-step:**

1. **Load environment safely** — the orchestrator makes API keys and paths available through an approved secret manager, CLI-provided environment, or safe/allowlisted dotenv loading. Project-local env files are untrusted shell input unless vetted, so individual agents must not execute arbitrary env-file shell content.
2. **Palace overview** — call `mempalace_status()` to confirm connectivity and see what wings/rooms exist.
3. **Read recent diaries** — call `mempalace_diary_read(agent_name=<your-agent-name>, last_n=5)` to recall what you did in recent sessions. Look for unfinished work, known blockers, or context that's relevant to the current task.
4. **Query competency** — call `mempalace_kg_query(entity=<your-agent-name>)` to retrieve your `learned_lesson`, `common_mistake`, and `competency_level` facts for the task domain. Apply these lessons proactively.
5. **Detect project context** — check the project registry (via `project-context` skill or `mempalace_search(query="project registry", wing="tooling")`). Load the target project's `AGENTS.md` before writing any code.

If MemPalace is unavailable (tool call fails), continue without it — it's an enhancement, not a requirement.

---

### DURING WORK

Follow these principles throughout the session:

```text
DURING WORK
│
├─ Memory-first: search palace (wing+room filtered) before reading files
├─ KG queries for entity facts, relationships, blockers
├─ Delegate to specialist agents as needed
└─ Grep fallback when vector similarity < 0.3
```

#### Memory-First Search

Before reading source files or investigating from scratch, always check the palace:

```
mempalace_search(query="defeat flow freeze bug", wing="tower-defense", room="bugs")
```

Use `wing` and `room` filters to narrow results. If the palace has the answer, use it. If not, proceed with file-based investigation and store findings afterward.

#### Grep Fallback Rule

After calling `mempalace_search`, inspect the similarity scores of returned results:
- **≥ 0.3** — trust the result, use it as context.
- **< 0.3 (all results)** — treat as "no useful match." Fall back to `grep` / `glob` for file-based search instead.

This prevents agents from acting on weak semantic matches that may be misleading.

#### KG Queries During Work

Call `mempalace_kg_query` on entities you're working with (project names, system names, agent names) to discover:
- Relationships (`has_system`, `part_of`, `calls`, `depends_on`)
- Known blockers (`blocks`, `blocked_by`)
- Timeline context via `mempalace_kg_timeline`

#### Storing Knowledge During Work

When you discover something significant mid-task, store it immediately — don't wait for session end:

```
mempalace_add_drawer(
  wing="tower-defense",
  room="bugs",
  content="BeginNewGame must ONLY call SceneManager.LoadScene — no manual cleanup. BFS re-computation on half-destroyed objects caused editor freezes during defeat flow.",
  source_file="Assets/Scripts/Game.cs"
)
```

**What to store:**
- Bug root causes and their fixes
- Architecture decisions and rationale
- Gotchas that aren't obvious from the code
- Balance/tuning values that took iteration to find
- Integration patterns (e.g., "URP 2D doesn't render SpriteRenderers under MeshRenderers")

**What NOT to store:**
- Routine code changes
- Temporary debugging notes
- Information already in `AGENTS.md`

Always call `mempalace_check_duplicate(content="...", threshold=0.85)` before adding a drawer to avoid duplicates.

---

### SESSION END (6-step checklist)

Before ending a session, complete all six steps in order:

```text
SESSION END
│
├─ 1. Diary write          (natural language summary)
├─ 2. KG add               (new facts + relationships)
├─ 3. KG invalidate        (stale facts discovered during session)
├─ 4. Contradiction check  (singleton predicates)
├─ 5. Competency update    (lessons + level if warranted)
└─ 6. Session-index drawer (NL summary in mempalace_sessions/session-index)
```

#### 1. Diary Write

Write a natural language summary of the session. **Always use plain natural language — never use AAAK compressed format.**

```
mempalace_diary_write(
  agent_name="unity-gameplay-developer",
  entry="Implemented wave 6 boss with phased behavior. Fixed turret targeting priority to prefer bosses. Tuned boss HP to 12000 after three playtest rounds. Discovered URP sprite rendering gotcha with mesh parent objects.",
  topic="wave-system"
)
```

The diary should capture: what was attempted, what succeeded, what failed, and any open questions for next session.

#### 2. KG Add — New Facts and Relationships

Store entity relationships discovered during the session:

```
mempalace_kg_add(subject="Tower Defense", predicate="has_system", object="dual-currency-coins-energy")
mempalace_kg_add(subject="EnergySystem", predicate="part_of", object="Tower Defense")
mempalace_kg_add(subject="CoinSystem", predicate="calls", object="BreachGenerator")
mempalace_kg_add(subject="wave-6-boss", predicate="worked_on_in", object="session-2026-04-15")
```

**Standard relationship predicates:**

| Predicate | Meaning |
|-----------|---------|
| `part_of` | Entity is a component/subsystem of another |
| `calls` | Entity invokes or depends on another at runtime |
| `blocks` | Entity is blocking progress on another |
| `blocked_by` | Entity is blocked by another |
| `worked_on_in` | Entity was modified in a specific session |
| `has_system` | Project contains a named system |
| `depends_on` | Entity requires another to function |
| `replaces` | Entity supersedes another |

Use kebab-case identifiers for objects. No parentheses, backslashes, or special characters.

#### 3. KG Invalidate — Stale Facts

If you discovered that a previously stored fact is no longer true, invalidate it:

```
mempalace_kg_invalidate(
  subject="Tower Defense",
  predicate="has_bug",
  object="door-pass-through"
)
```

Common triggers: bugs that are now fixed, systems that were replaced, balance values that changed.

#### 4. Contradiction Check — Singleton Predicates

Some predicates should only have one active value at a time (singletons). Before adding a new fact, query existing facts and invalidate the old one:

**Singleton predicates** (only one active value allowed):
- `competency_level` — an agent can only have one level per domain
- `current_balance` — a tuning parameter has one active value
- `active_branch` — a project has one active working branch

```
# Check before adding
mempalace_kg_query(entity="unity-gameplay-developer")
# If competency_level already exists with a different value, invalidate the old one first
mempalace_kg_invalidate(subject="unity-gameplay-developer", predicate="competency_level", object="novice-unity-urp")
mempalace_kg_add(subject="unity-gameplay-developer", predicate="competency_level", object="intermediate-unity-urp")
```

#### 5. Competency Update — Lessons and Levels

Track what each agent learns across sessions using the knowledge graph.

**Competency KG Convention:**

| Predicate | Purpose | Example Object |
|-----------|---------|---------------|
| `competency_level` | Current skill level (singleton) | `intermediate-unity-urp` |
| `learned_lesson` | Specific insight gained | `avoid-manual-cleanup-on-scene-reload` |
| `common_mistake` | Repeated error to watch for | `forgetting-to-invalidate-navmesh-after-door-destroy` |

**Competency levels** (progression path):
`novice` → `intermediate` → `proficient` → `expert`

**When to update:**
- **Add `learned_lesson`** after every session where you discovered something non-obvious.
- **Add `common_mistake`** if you made an error you've seen before.
- **Upgrade `competency_level`** only when there's clear evidence of improved capability (e.g., solved a complex problem independently that previously required multiple retries).

```
mempalace_kg_add(
  subject="unity-gameplay-developer",
  predicate="learned_lesson",
  object="urp-2d-no-sprite-under-mesh-renderer"
)
mempalace_kg_add(
  subject="unity-gameplay-developer",
  predicate="competency_level",
  object="proficient-unity-urp"
)
```

#### 6. Session-Index Drawer

Store a natural language summary of the session as a searchable drawer in the shared session index:

```
mempalace_add_drawer(
  wing="mempalace_sessions",
  room="session-index",
  content="Session 2026-04-15: unity-gameplay-developer worked on Tower Defense wave 6 boss implementation. Added phased boss behavior, fixed turret targeting priority, tuned HP to 12000. Discovered URP sprite rendering limitation with mesh parent objects. Three playtest iterations completed.",
  added_by="orchestrator"
)
```

This makes sessions discoverable via `mempalace_search` across all projects and agents.

---

## Best Practices

- **Verify before trusting** — palace content may be outdated. Cross-reference with actual code.
- **Be specific** — "Game.cs line 296 defeat timer uses Update not coroutine" is better than "defeat flow works a certain way."
- **One concept per drawer** — don't dump everything into one drawer. Separate concerns.
- **Use source_file** — always include the relevant file path when storing code knowledge.
- **Filter searches** — always pass `wing` and `room` when you know the project and topic. Unfiltered searches return noisier results.
- **Natural language everywhere** — diary entries, session-index drawers, and drawer content should all be clear, readable natural language. Never use AAAK or other compressed formats.
