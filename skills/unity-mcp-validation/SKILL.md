---
name: unity-mcp-validation
description: "Practical Unity MCP workflows for connectivity checks, live editor/runtime validation, and separating gameplay regressions from editor/tooling noise."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Unity MCP Validation Skill

## Capabilities
- **Connectivity and instance verification** — confirm Unity MCP is actually connected to the intended editor instance before trusting any validation result, including stale-session recovery and active-instance checks.
- **Live play mode validation** — use Unity MCP to enter or inspect play mode, verify feature behavior in a running scene, and confirm fixes against real editor/runtime state instead of static code assumptions alone.
- **Scene, hierarchy, and component inspection** — inspect scenes, GameObjects, prefabs, and serialized component state to verify wiring, references, and gameplay-critical configuration.
- **Console and warning triage** — inspect Unity Console output and separate gameplay/runtime regressions from editor-only, tooling-only, or known serializer/deep-serializer warning noise.
- **Runtime UI binding checks** — validate live UI state, bindings, button targets, missing references, and active/inactive hierarchy conditions while the game is actually running.
- **Mainline-aware validation** — validate against the real merged `main` state whenever possible, not just an isolated branch snapshot or batch-mode run that can miss integration problems.
- **Fallback validation workflows** — work around editor lock or read-only behavior with temp-copy validation, safe repro scenes, and other non-destructive checks when the live project cannot be edited directly.

## Best Practices
1. **Verify the bridge first** — check Unity MCP connectivity, target the correct Unity instance, and refresh state before concluding that a fix passed or failed.
2. **Prefer live validation over batch-only confidence** — batch mode is useful, but gameplay fixes should be confirmed in the real editor and runtime loop, especially for scene wiring, UI bindings, and interaction-heavy systems.
3. **Validate the real integration point** — whenever practical, test on the actual merged `main` result so branch drift, scene divergence, or package/config differences do not produce false confidence.
4. **Treat warnings as evidence, not verdicts** — deep serializer warnings, editor/tooling warnings, and package noise should be investigated, then classified clearly as blocking regressions or non-blocking environment chatter.
5. **Respect editor safety constraints** — when Unity opens files read-only, locks assets, or blocks direct mutation, use temp-copy or inspection-first workflows rather than forcing risky edits.
6. **Use official references when tool behavior is unclear** — prefer the official Unity MCP documentation and tool descriptions when choosing workflows or interpreting tool limitations.

## When to Use
- Building or debugging Unity gameplay features that need live play mode confirmation, not just static code review.
- Checking scene references, prefab wiring, runtime UI bindings, or console output after Unity feature changes.
- Investigating whether a Unity warning is a real gameplay regression or only editor/tooling noise.
- Validating fixes on real merged `main` before claiming a Unity task is done.
- Working in Unity projects where editor locks, read-only files, or serializer caveats make direct validation tricky.

## References
- Official Unity MCP docs: https://github.com/CoplayDev/unity-mcp
