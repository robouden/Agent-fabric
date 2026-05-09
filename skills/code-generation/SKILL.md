---
name: code-generation
description: "Structured workflow for generating production-quality code aligned with the target project's architecture, patterns, and conventions."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Code Generation Skill

## Capabilities
- **Implement features** — write new modules, endpoints, components, or services
- **Extend existing code** — add functionality to existing files without breaking contracts
- **Scaffold structure** — create boilerplate, project skeletons, and repeating patterns
- **Apply design patterns** — use patterns appropriate to the language and architecture

## Workflow

Follow these steps in order. Do not skip steps — each one feeds the next.

### Step 1 — Understand Context
Before writing any code, gather project context:
- Identify the language, framework, and build tool (package.json, pom.xml, pubspec.yaml, etc.)
- Read the project's existing code in the target area to understand conventions
- Check for linter/formatter configs (.eslintrc, .prettierrc, analysis_options.yaml, checkstyle.xml)
- Identify the architecture pattern in use (MVC, Clean Architecture, hexagonal, etc.)
- Look for existing similar code to use as a reference implementation

### Step 2 — Plan the Change
Before writing code, define:
- **What** files will be created or modified
- **Where** they fit in the existing directory structure
- **How** they integrate with existing modules (imports, DI, routing, etc.)
- **What** edge cases and error scenarios must be handled

### Step 3 — Implement
Write the code following these rules:
1. **Match the project's style exactly** — indentation, naming, import order, file structure
2. **Follow the architecture** — put code in the right layer (domain, data, presentation, etc.)
3. **Reuse existing utilities** — check for shared helpers, constants, base classes before creating new ones
4. **Handle errors consistently** — use the project's established error handling pattern
5. **Keep functions/methods focused** — one responsibility per function
6. **Name things descriptively** — names should explain intent, not implementation
7. **Only comment non-obvious logic** — do not add noise comments

### Step 4 — Validate
After writing code, verify:
- The code compiles/builds without errors (`npm run build`, `./gradlew build`, etc.)
- Existing tests still pass
- The linter reports no new violations
- Imports and dependencies are correctly declared
- No duplicate code was introduced that should use an existing utility

## Anti-Patterns to Avoid
- ❌ Writing code that ignores existing project conventions
- ❌ Hardcoding values that should be constants or config
- ❌ Creating new utility functions when equivalent ones already exist
- ❌ Adding dependencies without checking if the project already has an alternative
- ❌ Skipping error handling for external calls (API, DB, file I/O)
- ❌ Generating boilerplate comments (e.g., `// Constructor`, `// Getters and setters`)

## When to Use
- Implementing new features, endpoints, or components
- Adding utility functions or helper modules
- Scaffolding new services or project structure
- Extending existing modules with new functionality

