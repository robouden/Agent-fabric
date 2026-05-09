---
name: code-analysis
description: "Structured workflow for investigating code — understanding architecture, tracing logic, detecting issues, and reporting findings."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Code Analysis Skill

## Capabilities
- **Investigate code** — trace control flow, data flow, and side effects
- **Map architecture** — identify layers, modules, dependencies, and boundaries
- **Detect issues** — find bugs, anti-patterns, security risks, and dead code
- **Understand conventions** — extract the project's patterns, naming, and style rules
- **Assess quality** — evaluate maintainability, testability, and complexity

## Workflow

Follow these steps in order. Adjust depth based on the scope of the analysis.

### Step 1 — Define Scope
Before reading code, clarify what you are analyzing:
- **Target** — specific files, a module, a feature, or the entire project?
- **Goal** — understanding architecture? finding bugs? preparing for a refactor?
- **Depth** — surface-level overview or deep line-by-line investigation?

### Step 2 — Gather Context
Build a mental model of the project before diving into details:
- Identify the language, framework, and build system
- Read project config files (package.json, pom.xml, pubspec.yaml, docker-compose.yml)
- Map the directory structure to understand the architecture (src/, lib/, domain/, data/, etc.)
- Identify entry points (main files, route definitions, API controllers)
- Check for existing documentation (README, ADRs, inline docs)

### Step 3 — Trace the Code
Investigate the target code systematically:
- **Control flow** — follow execution from entry point through all branches
- **Data flow** — track how data is created, transformed, passed, and stored
- **Dependencies** — map what the code imports and what depends on it
- **Error paths** — trace what happens when things fail (exceptions, null, timeouts)
- **Side effects** — identify mutations, I/O, network calls, state changes

### Step 4 — Identify Issues
Evaluate the code against these dimensions:

| Dimension | What to look for |
|-----------|-----------------|
| **Correctness** | Logic errors, off-by-one, null/undefined risks, race conditions, unhandled edge cases |
| **Security** | Injection, auth bypass, sensitive data exposure, insecure defaults |
| **Performance** | N+1 queries, unnecessary allocations, blocking calls, missing pagination |
| **Maintainability** | God classes, deep nesting, duplicated logic, unclear naming, tight coupling |
| **Testability** | Hard-coded dependencies, global state, untestable side effects |
| **Consistency** | Deviations from the project's own established patterns and conventions |

### Step 5 — Report Findings
Present results using severity levels:

- 🔴 **Critical** — bugs, security vulnerabilities, data loss risks — must fix
- 🟡 **Warning** — code smells, potential issues, fragile logic — should fix
- 🟢 **Suggestion** — improvement opportunities, better patterns available
- ⚪ **Observation** — context or notes, no action required

For each finding, include:
1. **What** — the issue or observation
2. **Where** — file path and line reference
3. **Why** — why it matters (impact)
4. **How** — recommended fix or next step (when applicable)

## Anti-Patterns to Avoid
- ❌ Reporting style/formatting issues that a linter should catch
- ❌ Flagging conventions as "wrong" when they are consistent within the project
- ❌ Listing every possible issue without prioritization
- ❌ Making assumptions about intent without tracing the actual code
- ❌ Providing vague findings like "this could be improved" without specifics

## When to Use
- Before refactoring — understand what exists and why
- During code review — find issues systematically
- When investigating bugs — trace root cause through the codebase
- When onboarding — build a mental model of an unfamiliar project
- Before implementing a feature — understand the area you will change

