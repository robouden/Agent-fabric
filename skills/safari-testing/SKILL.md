---
name: safari-testing
description: "Test local web apps in Safari via MCP/browser automation with environment checks, flow verification, canvas-aware QA, and blocker reporting."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Safari Testing Skill

This skill captures a repeatable Safari QA workflow for local web apps, especially when validating WebKit-specific behavior, mobile-style presentation, and canvas-heavy experiences such as PixiJS games.

## Capabilities
- **Environment readiness checks** — confirm Hermes CLI auth, Safari MCP availability, local dev server status, and Safari automation permissions before spending time on flaky runs.
- **Origin and URL strategy** — choose the correct local host (`localhost` vs `127.0.0.1`) and keep browser/backend origins aligned to avoid false-negative CORS, cookie, or session issues.
- **Flow verification in Safari** — validate screen-by-screen user flows, route changes, modals, and interaction outcomes with explicit checkpoints.
- **Runtime diagnostics** — inspect Safari console output, failed network requests, status codes, asset errors, and JavaScript exceptions alongside visual behavior.
- **Visual evidence capture** — collect screenshots for key states, regressions, and blocker reports so findings are easy to review asynchronously.
- **Canvas-first validation** — test PixiJS/canvas apps using screenshots, visible text, URL state, network activity, and app-level signals when DOM selectors are sparse.
- **Blocker reporting** — summarize exact repro steps, expected vs actual behavior, environment details, and missing prerequisites when the flow cannot be completed.

## Best Practices
1. **Check the environment before testing the feature** — verify the target server is running, the intended URL loads, the Hermes CLI session has the needed MCP/browser tooling, and Safari automation permissions are granted. Do not start detailed QA until the stack is reachable.
2. **Pick one loopback hostname and use it consistently** — `http://localhost:3000` and `http://127.0.0.1:3000` are different origins in browser security rules. If the frontend talks to a local API, use the hostname the app/server is configured to allow and keep it consistent across browser URL, API base URL, cookies, and WebSocket endpoints.
3. **Suspect origin mismatches first when Safari behaves differently** — if login, fetches, CORS preflights, cookies, or WebSocket connections fail only in Safari, compare the exact origin tuple (`scheme + host + port`) for the page and every network dependency before assuming an application bug.
4. **Test flows as checkpoints, not as one long blur** — define the critical screens or state transitions up front (landing, login, dashboard, battle screen, modal, error state, etc.) and verify each one with visible evidence, console cleanliness, and network health.
5. **Review console and network together** — a blank screen or stuck state often pairs a visible symptom with a console exception, failed asset load, CORS rejection, or 4xx/5xx API response. Capture all of them as one finding.
6. **Capture screenshots at meaningful states** — take screenshots for the initial load, each major transition, and any failure state. Prefer reporting that ties each image to a step and expected outcome.
7. **Treat canvas apps as visual systems, not DOM forms** — for PixiJS/WebGL apps, rely on screenshots, scene text, loading indicators, URL changes, console output, and network requests when the accessibility tree or DOM gives little leverage.
8. **Use app-level observability when selectors are weak** — if the canvas surface hides internal state, lean on visible labels, debug overlays, request patterns, save/load events, or logged state transitions instead of inventing brittle DOM assumptions.
9. **Report blockers with exact environment context** — include the URL used, hostname choice, browser/tool availability, reproduction step, console/network evidence, and why the blocker prevents further validation.
10. **Separate product bugs from environment blockers** — “Safari MCP unavailable,” “server not running,” and “CORS configured only for localhost while browser uses 127.0.0.1” are different classes of failure and should be reported differently.

## Safari QA Checklist
- Confirm the local app server is running and reachable in Safari.
- Confirm the Safari MCP/browser automation workflow is available in the current Hermes CLI session.
- Record the exact URL under test, including whether it uses `localhost` or `127.0.0.1`.
- Exercise the target flow step by step instead of skipping directly to the reported bug.
- Inspect console messages after the initial load and after each major interaction.
- Inspect network failures, especially blocked fetch/XHR/WebSocket/asset requests and CORS preflights.
- Capture screenshots for success states and failure states.
- For canvas apps, verify the rendered scene, visible HUD/text, and interaction response even if DOM nodes are minimal.
- End with either a clean validation summary or a blocker report with evidence.

## Blocker Report Format
Use this structure when Safari validation cannot be completed:

```md
### Safari QA Blocker
- **Step**: <where the flow stopped>
- **URL**: <exact URL, including localhost vs 127.0.0.1>
- **Expected**: <what should have happened>
- **Actual**: <what happened instead>
- **Console / Network Evidence**: <key error text, status code, failed request, or note that tooling was unavailable>
- **Impact**: <which remaining checks could not be completed>
- **Next Step**: <specific fix or environment action needed>
```

## When to Use
- Reproducing or validating Safari/WebKit-specific bugs on local web apps
- Performing browser QA for login, onboarding, dashboard, or gameplay flows in Safari
- Testing PixiJS or other canvas-first apps where DOM-based automation is limited
- Investigating reports that behave differently between Chrome and Safari
- Collecting screenshots, console logs, and network evidence for PRs or bug reports

## Anti-Patterns to Avoid
- ❌ Switching between `localhost` and `127.0.0.1` mid-investigation without noting it
- ❌ Declaring a product bug before confirming the local server and Safari tooling are actually available
- ❌ Relying only on DOM selectors for canvas-heavy apps
- ❌ Reporting “doesn’t work in Safari” without console, network, or screenshot evidence
- ❌ Capturing only the final error state without documenting the last successful checkpoint
