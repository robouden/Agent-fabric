---
name: diagnose
description: "Disciplined diagnosis loop for hard bugs and performance regressions: feedback loop, reproduce, hypothesize, instrument, fix, and regression-test."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Diagnose Skill

> Adapted from `mattpocock/skills` at `383b6a06d59c4ce0ffcb14112bfd91265a86cf91` (MIT). See `docs/imported-skills.md`.

## Capabilities
- **Feedback-loop first debugging** — build a deterministic pass/fail signal before theorizing.
- **Falsifiable hypotheses** — rank 3–5 possible causes with predictions before probing.
- **Targeted instrumentation** — add minimal tagged probes that distinguish hypotheses, then remove them.
- **Regression capture** — convert the repro into a durable test at the right seam when possible.

## Workflow
1. **Build the loop** — failing test, HTTP/CLI harness, browser automation, captured trace replay, fuzz loop, or structured human-in-the-loop repro.
2. **Reproduce** — confirm the loop shows the user's exact symptom, not a nearby failure.
3. **Hypothesize** — list ranked, falsifiable causes and the observation that would confirm or reject each.
4. **Instrument** — change one variable at a time; use unique debug prefixes such as `[DEBUG-a4f2]`.
5. **Fix and test** — write the regression test first if a correct seam exists, apply the minimal fix, and rerun the original loop.
6. **Clean up** — remove probes and throwaway harnesses, then document the root cause.

## When to Use
- Bugs, failing tests, broken UI/API behavior, flaky behavior, or performance regressions.
- Any request phrased as "diagnose", "debug", "investigate", "throwing", or "failing".

