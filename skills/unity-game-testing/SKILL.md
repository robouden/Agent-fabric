---
name: unity-game-testing
description: "Run structured Unity playtests with Unity MCP, covering play mode flows, scene traversal, console triage, screenshots, and blocker reporting."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Unity Game Testing Skill

This skill defines a repeatable workflow for testing Unity games through live editor and play mode sessions. It is intended for gameplay validation, regression checks, and exploratory playtesting where static code review is not enough.

## Capabilities
- **Environment readiness checks** — confirm Unity MCP connectivity, target the correct editor instance, and make sure the intended scene or test setup is loaded before starting playtests.
- **Structured play mode sessions** — enter play mode, exercise planned gameplay routes, and verify navigation, combat, interactions, pickups, triggers, and win/fail states in a controlled order.
- **Scene and hierarchy reconnaissance** — inspect the active scene, locate key GameObjects, spawn points, exits, hazards, UI roots, and gameplay-critical references before testing.
- **Console and warning triage** — monitor Unity console output during and after play sessions, separating real gameplay regressions from editor or tooling noise.
- **Visual evidence capture** — collect screenshots at major checkpoints and failure states so findings can be reviewed asynchronously.
- **Coverage-aware reporting** — report what was tested, what was not tested, which flows passed, which failed, and which areas remain blocked by environment or content gaps.
- **Blocker-focused handoff** — document exact repro steps, observed state, and next actions when testing cannot continue because of missing setup, broken scene wiring, or unavailable content.

## Best Practices
1. **Verify the target scene and bridge first** — confirm Unity MCP is connected to the intended editor instance and that the correct scene, branch, and runtime setup are active before trusting any playtest result.
2. **Inspect before you play** — review the scene hierarchy, key objects, and likely traversal routes up front so the playtest covers meaningful flows rather than wandering blindly.
3. **Test by gameplay checkpoints** — break the session into explicit checkpoints such as spawn, traversal, combat encounter, pickup, trigger, checkpoint, victory, and fail state.
4. **Watch console output during the run** — a playtest result is incomplete without checking console errors, warnings, and missing-reference signals that may explain visible behavior.
5. **Capture evidence at the moment of failure** — take screenshots and note the exact step, state, and expected outcome when something breaks instead of summarizing only from memory afterward.
6. **Separate product bugs from environment blockers** — missing scenes, absent prefabs, editor connection issues, and incomplete content should be reported as blockers, not misclassified as gameplay defects.
7. **End with a structured report** — summarize tested paths, pass/fail outcomes, console findings, blockers, and suggested next steps so the next agent or user can act immediately.

## Unity Playtest Checklist
- Confirm Unity MCP is connected and targeting the correct editor instance.
- Confirm the intended scene is loaded and saved.
- Inspect the hierarchy for player spawn, enemies, exits, interactables, loot, traps, triggers, and UI roots.
- Enter play mode and exercise the requested gameplay path or test focus.
- Check the Unity console during and after the run.
- Capture screenshots for important checkpoints and all failure states.
- Record which gameplay areas were covered and which were skipped.
- Exit play mode cleanly and summarize findings in a report.

## Report Format
Use this structure when reporting a Unity playtest:

```md
### Unity Playtest Report
- **Focus**: <requested test scope>
- **Scene / Setup**: <scene name, important setup notes>
- **Covered Areas**: <paths, encounters, systems, UI states tested>
- **Passes**: <behaviors that worked as expected>
- **Failures**: <bugs or broken flows, with exact repro steps>
- **Console Evidence**: <key errors/warnings or note that console stayed clean>
- **Screenshots**: <captured evidence references>
- **Untested / Blocked**: <areas not reached and why>
- **Next Step**: <specific follow-up action>
```

## When to Use
- Playtesting a Unity gameplay feature after implementation
- Running a regression pass on a Unity scene or encounter flow
- Verifying scene traversal, combat, pickups, traps, or UI updates in play mode
- Investigating player-reported “stuck”, “nothing happens”, or “trigger never fired” issues
- Collecting runtime evidence for a Unity bug report or pull request

## Anti-Patterns to Avoid
- ❌ Declaring a feature working without entering play mode
- ❌ Skipping console review and relying only on visual impressions
- ❌ Testing randomly without defining coverage checkpoints
- ❌ Reporting a failure without repro steps or scene context
- ❌ Treating missing setup or editor connection problems as gameplay bugs
