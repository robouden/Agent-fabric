---
name: game-development
description: "Coordinate phased PixiJS game-development delivery across design, implementation, runtime validation, QA, and PR handoff."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Game Development Skill

This skill activates the full game-development agent pipeline. It is used by the `orchestrator` whenever a task spans multiple game-development domains: design, scene architecture, implementation, art generation, backend wiring, runtime validation, testing, and quality review.

It supports both **full end-to-end delivery** and **phase-gated delivery**. Large game tasks do not need to ship as one monolithic pass: the orchestrator may split work across smaller, reviewable phases, but each merged runtime-affecting phase must be followed by explicit local validation before more work continues.

## Capabilities

- **Build new game features** — design, architect, implement, validate, and test any feature from mechanic spec to working code
- **Deliver work in phased slices** — split large game changes into smaller PRs with explicit phase gates and post-merge validation between phases
- **Fix gameplay and runtime/UI bugs** — investigate root cause first, implement with the engine-appropriate specialist, validate with tests, live browser checks, and review gates
- **Add or regenerate art assets** — generate UI art, backgrounds, icons, and textures using the `openai-image-generation` skill
- **Redesign screens or game flows** — update scene graphs, layouts, transitions, and affected browser flows end-to-end
- **Add narrative or level content** — integrate lore, dialogue, level flow, and encounter pacing into the game
- **Run a full QA pass** — test coverage, visual QA, runtime validation, build validation, and code review across all changed components
- **Coordinate multi-agent parallel work** — dispatch design, art, backend, and QA agents simultaneously where phase dependencies allow it

## Team Composition

| Agent | Role in game development |
|-------|--------------------------|
| `game-designer` | Writes the GDD, mechanic specs, player experience goals, progression tuning, and design acceptance criteria |
| `narrative-designer` | Authors lore, dialogue scripts, branching narrative structure, and story beats |
| `level-designer` | Defines level flow, encounter pacing, spatial readability plans, and zone briefs |
| `pixijs-architect` | Designs PixiJS scene graphs, layer boundaries, entity-component patterns, state machines, and asset management strategy |
| `pixijs-prototype-specialist` | Preferred implementer for PixiJS runtime/UI work: scenes, containers, interactions, animations, browser validation, and visual QA |
| `code-investigator` | Investigates runtime, gameplay, and integration bugs before implementation starts; produces root-cause findings and likely fix scope |
| `code-writer` | Wires backend APIs, data models, game-state persistence, server-side logic, and non-engine-specific implementation work |
| `tester` | Writes and runs unit, integration, and e2e tests to validate gameplay loops, regressions, and feature behaviour |
| `code-reviewer` | Enforces quality gates: security, performance, correctness, and code standards before any PR is merged |
| `orchestrator` | Coordinates phases, tracks exit gates, synthesises outputs, manages PRs, and enforces post-merge validation checkpoints |

## Workflow

Work proceeds through **six reusable phases**. A task may use all six in one PR or split delivery across multiple smaller PRs. Phases 2 and 3 may run **in parallel** when the architecture spec is complete. If a runtime-affecting PR is merged before the overall feature is finished, the **Post-merge Local Validation Checkpoint** must pass before the next phase begins.

### Phase 0 — Design
**Owner:** `game-designer` (+ `narrative-designer`, `level-designer` as needed)

1. `game-designer` creates or updates the GDD, mechanic spec, and player experience goals.
2. `narrative-designer` adds story beats and dialogue structure if the feature involves narrative.
3. `level-designer` adds a level brief or encounter plan if the feature involves level content.
4. Deliverable: a reviewed, approved design spec document (stored in `plan.md` or the project's docs directory).

**Exit gate:** Design spec reviewed and approved by the orchestrator (or the user) before Phase 1 begins.

---

### Phase 1 — Architecture
**Owner:** `pixijs-architect`

1. `pixijs-architect` reads the design spec.
2. Designs the PixiJS scene graph, layer boundaries, state machines, and asset management strategy.
3. Documents the architecture in a brief technical spec (scene hierarchy, data flow, asset list, affected routes/flows).

**Exit gate:** Architecture spec reviewed against the design spec; no unresolved conflicts.

---

### Phase 2 — Implementation *(can run in parallel with Phase 3)*
**Owner:** engine-appropriate specialist (`pixijs-prototype-specialist` for PixiJS runtime/UI work)

1. Reads the design spec and architecture spec.
2. Keep engine-specific implementation in the engine lane. For PixiJS scenes, runtime bugs, browser UI flows, and combat interactions, prefer `pixijs-prototype-specialist` over a generic `code-writer`.
3. Generates required art assets using the `openai-image-generation` skill when needed (see Art Style Anchor section below).
4. Implements scenes, containers, interactions, animations, and frontend state changes.
5. Runs the frontend build gate:
   ```bash
   npm run typecheck && npm run build
   ```
6. Runs an implementer smoke-test in the live browser on the affected route/flow:
   - load the actual route/scene locally
   - verify the intended interaction path, not just static rendering
   - inspect browser console and network for runtime failures
   - confirm the local frontend is targeting the intended backend/port
   - confirm localhost is serving the intended branch/commit/build rather than a stale dev server
7. If runtime validation is blocked by missing backend services or wrong environment wiring, record that as an **environment blocker**, not as a product defect.

**Exit gate:** Build passes + affected browser flow validated locally + any environment blockers explicitly documented.

---

### Phase 3 — Backend *(can run in parallel with Phase 2)*
**Owner:** `code-writer`

1. Reads the design spec and API contracts from the architecture spec.
2. Implements backend APIs, data models, game-state persistence, and any server-side logic.
3. Runs the server test gate:
   ```bash
   ./gradlew test
   ```
   *(adjust to the project's actual backend test command — e.g., `npm test`, `pytest`, `go test ./...`)*

**Exit gate:** All backend tests pass.

---

### Phase 4 — Testing, Runtime Validation & Quality Gate
**Owners:** `tester`, then engine specialist/orchestrator, then `code-reviewer`

1. `tester` writes or extends unit, integration, and e2e tests covering the feature or bug fix and its edge cases.
2. `tester` runs the relevant test suite and confirms all tests pass.
3. The engine specialist or orchestrator reruns the affected flow in a final runtime gate after implementation and tests are complete.
4. During runtime validation, check the browser console, network activity, backend target/port, and whether localhost is serving the intended branch/commit/build.
5. If backend availability prevents runtime validation, log the blocker explicitly and distinguish it from a product defect.
6. `code-reviewer` performs a full quality review: security, performance, correctness, and standards compliance.
7. `code-reviewer` must give an explicit "approved" verdict before the PR is created.

**Exit gate:** Tests pass + live runtime validation completed (or blocker documented) + `code-reviewer` sign-off.

---

### Phase 5 — PR & Handoff
**Owner:** `orchestrator`

1. Orchestrator confirms all phase exit gates are met.
2. Creates (or consolidates) the PR using `gh pr create` with a conventional commit message.
3. Reports the PR URL to the user.
4. Leaves the PR **open** — never auto-merges. The user reviews and merges.
5. If the feature will continue in later phases after this PR, wait for the user's merge, then run the **Post-merge Local Validation Checkpoint** before proceeding.

---

## Post-merge Local Validation Checkpoint

Run this checkpoint **after every user-merged runtime-affecting phase** before starting the next phase of work.

1. Pull or switch to the merged branch/commit locally.
2. Start the game locally and confirm the served build matches the intended branch/commit.
3. Exercise the actual affected route/flow in the browser.
4. Check browser console and network panels for errors, failed requests, and stale assets.
5. Confirm the frontend is pointing at the expected backend host/port/environment.
6. If validation is blocked by backend availability or environment wiring, document the blocker clearly and stop treating it as a product defect.

**Checkpoint result:** Either (a) merged phase validated locally, or (b) environment blocker documented with enough detail for the next agent/user to resolve it.

## When to Use This Skill

Use the full game-development pipeline when the task touches **two or more** of: design, art, implementation, backend, runtime validation, or QA.

| Task type | When to use this skill |
|-----------|------------------------|
| New game feature (mechanic, screen, or flow) | ✅ Full pipeline — all relevant phases |
| Phase-gated delivery of a large feature | ✅ Split into smaller PRs, but run the post-merge local validation checkpoint after each merged runtime phase |
| Visual redesign of an existing screen | ✅ Phases 1–5 (skip Phase 0 if design spec already exists) |
| Art pipeline task (new icons, backgrounds, textures) | ⚠️ Art-only subset — `pixijs-prototype-specialist` only |
| Gameplay or runtime/UI bug fix | ⚠️ Bug-fix subset — `code-investigator` + engine specialist (`pixijs-prototype-specialist` for Pixi) + `tester` + live browser validation + `code-reviewer` |
| Narrative or dialogue content only | ⚠️ Content subset — `narrative-designer` + `code-writer` (if wiring needed) |
| Level design document | ⚠️ Design subset — `level-designer` only |
| Backend API or data model only | ⚠️ Backend subset — `code-writer` + `tester` + `code-reviewer` |
| Full game vertical slice or new game project | ✅ Full pipeline — all phases, all agents |

## Agent Selection Guidance

### Full team (all agents)
Use when building a complete new feature, redesigning a major screen, or starting a new game project.

### Art-only task
Only `pixijs-prototype-specialist` is needed. Skip Phases 0, 1, and 3.
Provide a clear art brief: subject, dimensions, style anchor, and target directory.

### Bug fix
1. `code-investigator` — root-cause analysis and investigation report
2. engine/domain specialist — apply the fix (`pixijs-prototype-specialist` for PixiJS runtime/UI bugs)
3. `tester` — add or extend regression coverage
4. live browser validation — verify the affected route/flow locally, plus console/network/backend alignment
5. `code-reviewer` — quality gate
6. `orchestrator` — create PR and leave it open for user merge

### Backend-only task
1. `code-writer` — implement API or data model changes
2. `tester` — validate with tests
3. `code-reviewer` — quality gate

### Design-only task
Dispatch `game-designer`, `narrative-designer`, or `level-designer` as appropriate.
No code agents needed unless the design immediately feeds into implementation.

## Quality Gates (Summary)

| Phase / Checkpoint | Gate | Command / Criterion |
|--------------------|------|---------------------|
| Phase 0 — Design | Design spec approved | Reviewed by orchestrator or user |
| Phase 1 — Architecture | Architecture reviewed against spec | No unresolved conflicts |
| Phase 2 — Implementation | Frontend build + implementer smoke-test | `npm run typecheck && npm run build` ✅ + affected browser flow smoke-tested + backend/port/build alignment confirmed |
| Phase 3 — Backend | Server tests | `./gradlew test` ✅ (or project equivalent) |
| Phase 4 — Testing & Review | Tests + final runtime gate + reviewer sign-off | Relevant tests green + console/network checked in final validation + `code-reviewer` approved |
| Phase 5 — PR & Handoff | PR created, left open | PR URL reported to user |
| Post-merge Local Validation | Merged phase revalidated locally | Localhost serving intended commit + affected flow checked + blocker documented if environment prevents validation |

## Art Style Anchor

All AI art generation tasks **must use the project's locked style anchor phrase** to maintain visual cohesion across all generated assets.

### Where to find the style anchor
1. Check `plan.md` in the project root — the style anchor is documented in the art/visual section.
2. Check the project's `AGENTS.md` — it may contain a `## Art Style` section.
3. If no style anchor exists, ask the `game-designer` to define one and store it in `plan.md` before any art generation begins.

### Style anchor format
```
<theme>, <art style>, <color palette>, <mood/atmosphere>, "no text, no watermarks"
```

**Example:**
> `"dark knight RPG game UI, iron and steel aesthetic, dark fantasy, dark navy blue and steel grey, moody atmospheric lighting, no text, no watermarks"`

**Rule:** Never generate art without a style anchor. Inconsistent art styles across assets break visual cohesion and require regeneration — this costs more time than defining the anchor upfront.

## Best Practices

1. **Design first, build second** — never start implementation without a reviewed design spec. Undocumented assumptions cause rework across all phases.
2. **Art anchor before art generation** — lock the style anchor phrase before any `openai-image-generation` call. All assets in a project must share the same anchor.
3. **Use the engine specialist for engine bugs** — when an engine/domain specialist exists, they own that implementation lane. For Pixi runtime/UI work, prefer `pixijs-prototype-specialist` over a generic `code-writer`.
4. **Run phases in order, parallelise safely** — Phases 2 and 3 can run in parallel only after Phase 1 (architecture) is complete. Never skip phases or run them out of order.
5. **Validate the real runtime flow, not just the code diff** — every runtime-affecting change needs live browser validation on the affected route/flow, plus console and network checks.
6. **Check environment alignment before calling something broken** — wrong backend target, unavailable backend services, or stale local dev servers are environment blockers until proven otherwise.
7. **Quality gate before proceeding** — each exit gate must be explicitly confirmed, not assumed. A "probably fine" is not a pass.
8. **Support phased delivery deliberately** — large game work may ship across multiple PRs, but each merged phase needs a local validation checkpoint before more work starts.
9. **One PR per delivered slice** — keep each feature slice in a coherent branch and PR. Avoid partial PRs that leave the codebase in a broken state.
10. **Never merge your own PRs** — all PRs are left open for user review and merge. Report the PR URL to the user and stop.
11. **Propagate context forward** — each phase must receive the outputs of the previous phase as explicit context. Agents must not re-derive design decisions that have already been made.

## When to Use
- Building a new game feature end-to-end (mechanic, screen, flow, or system)
- Delivering a large game feature in reviewable phases instead of one monolithic pass
- Running a full visual redesign of an existing game screen
- Generating and integrating a batch of new art assets
- Fixing a gameplay or rendering bug with investigation, specialist implementation, and live runtime validation
- Adding narrative content, dialogue, or level data to a game
- Starting a new PixiJS game project or vertical slice from scratch
- Performing a full QA and quality gate pass on a game feature branch
