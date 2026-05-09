---
name: game-design-documentation
description: "Structured game design documentation: GDD outlines, runtime-accurate mechanic specs, tuning tables, playtest checkpoints, and level design briefs."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Game Design Documentation Skill

## Capabilities
- **Game Design Document (GDD) structure and authoring** — produce GDDs with consistent sections: vision statement, core pillars, player experience goals, feature list, and scope boundaries that keep teams aligned.
- **One-page mechanic specs with inputs/outputs/feedback loops** — write focused mechanic documents that describe player input, system response, feedback signals (visual/audio/haptic), and failure modes on a single page.
- **Runtime-accurate combat and state-flow specs** — document slot states, reserve/bag rules, combo resolution, timing formulas, and scene transitions in terms engineering and QA can verify in-game.
- **Tuning tables with named variables and success metrics** — document every tunable parameter with its name, type, current value, valid range, and the player experience metric it affects.
- **Player experience goal framing** — define and document PX goals (how the player should feel at each moment) before specifying mechanics, keeping design decisions grounded in desired player emotion.
- **Playtest hypothesis and checkpoint definitions** — write structured playtest plans with a clear hypothesis, what to observe, success/failure criteria, and what design questions the session is meant to answer.
- **Level design briefs with flow diagrams and encounter breakdowns** — author level briefs specifying intended player path, encounter density, pacing beats, key moment list, and blockout constraints.
- **Narrative beats and branching decision trees** — structure story moments as discrete beats with player choice points, consequence branches, and convergence nodes in a format writers and engineers can implement.

## Best Practices
1. **Write specs that engineers can implement without guessing intent** — if an engineer needs to ask "what did you mean by X?", the spec is incomplete; describe observable system behavior in concrete input/output terms.
2. **Name every tunable variable explicitly** — anonymous magic numbers in design docs become untrackable in code; every tunable value must have a descriptive name that matches what it will be called in the codebase or data tables.
3. **Define measurable success criteria for each feature** — "it should feel good" is not a success criterion; "80% of playtests complete the tutorial without asking for help" is; attach metrics to every feature before building it.
4. **Separate "what" (design intent) from "how" (implementation suggestion)** — design documents should describe the desired player experience and system behavior; implementation details are the engineer's domain unless there is a specific technical constraint to communicate.
5. **Iterate on paper/doc before committing to code** — a design spec reviewed and revised in a document costs hours; the same iteration in code costs days; all ambiguity should be resolved at the spec stage.
6. **Update docs when runtime learnings change intended behavior** — if a playtest, bug-fix session, or implementation spike proves the real combat loop differs from the written spec, update the mechanic rules, timings, and API assumptions together so future agents do not reintroduce stale behavior.
7. **Call out environment blockers separately from product defects** — when backend availability, wrong ports, or local environment drift prevent validation, document that as a validation blocker instead of describing it as intended gameplay behaviour.

## When to Use
- Producing a new Game Design Document, feature spec, or system overview.
- Writing a one-page mechanic spec for an upcoming feature before engineering begins.
- Updating an existing spec so it matches current intended runtime behaviour after implementation or playtest learnings.
- Creating tuning tables for a system that will need designer iteration after implementation.
- Drafting a playtest plan with clear hypotheses and success criteria.
- Authoring level design briefs or narrative beat documents for production handoff.
