---
name: luau
description: "Luau scripting patterns for Roblox: typed Luau, ModuleScript architecture, RemoteEvent/Function design, DataStore persistence, and secure server-client boundaries."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Luau (Roblox) Skill

## Capabilities
- **Typed Luau** — apply type annotations, `--!strict` mode, and custom type aliases throughout scripts to catch errors before runtime and improve code readability.
- **ModuleScript composition and require patterns** — structure projects with ModuleScripts as reusable libraries, use `require()` with explicit paths, and avoid circular dependencies through dependency injection.
- **RemoteEvent and RemoteFunction security patterns** — validate all client-sourced data on the server, use coarse-grained remote calls to reduce attack surface, and avoid sending sensitive server state to clients.
- **DataStore and DataStore2/ProfileService persistence** — implement safe persistent data with retry logic, session locking, and atomic updates using ProfileService or equivalent to prevent data loss.
- **Server/client authority boundaries** — clearly separate server-authoritative logic (`ServerScriptService`) from client-predicted presentation (`StarterPlayerScripts`) with explicit ownership at every system boundary.
- **Roblox service patterns** — use `Players`, `ReplicatedStorage`, `ServerStorage`, `RunService`, and `CollectionService` correctly; understand which services are accessible server-side vs. client-side.
- **Knit or similar framework patterns** — structure Roblox projects using Knit or Nevermore for service/controller separation, reducing boilerplate and improving testability.
- **Physics and collision group setup** — configure `PhysicsService` collision groups programmatically for correct entity-vs-entity interaction and to prevent exploitable physics collisions.

## Best Practices
1. **Always use `--!strict` and type annotations** — strict mode catches nil access, type mismatches, and missing properties before the game runs; all new scripts should opt in immediately.
2. **Never trust client data — validate all RemoteEvent payloads on the server** — clients can fire RemoteEvents with arbitrary arguments; always validate type, range, and permission server-side before acting on any client input.
3. **Keep RemoteEvents coarse-grained to reduce network spam** — fire one event that carries structured data rather than many fine-grained events; high event frequency causes bandwidth spikes and increases exploit surface area.
4. **Use ProfileService or equivalent for safe persistent data** — raw DataStore `SetAsync` without session locking can cause data loss when players join on multiple servers; ProfileService handles session locks, retries, and migrations.
5. **Separate server logic (`ServerScriptService`) from shared modules (`ReplicatedStorage`)** — server-only code must never be placed in `ReplicatedStorage` where clients can read it; shared utilities go in `ReplicatedStorage`, server secrets and authority logic stay in `ServerScriptService`.

## When to Use
- Writing or reviewing Roblox Luau scripts for gameplay systems, UI, persistence, or admin tools.
- Designing server/client architecture: deciding what is authoritative, replicated, or predicted.
- Implementing DataStore persistence with session safety and migration support.
- Reviewing RemoteEvent/RemoteFunction security: input validation, rate limiting, and anti-exploit patterns.
- Structuring large Roblox projects with ModuleScript composition and framework patterns.
