---
name: godot-gdscript
description: "GDScript patterns for Godot 4: typed scripting, signal-driven architecture, scene composition, and resource management."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Godot GDScript Skill

## Capabilities
- **Typed GDScript** — apply `@export`, type hints, and static typing throughout scripts to catch errors at parse time and improve IDE autocompletion.
- **Signal definition and connection patterns** — declare signals with typed parameters, connect them in `_ready()`, and disconnect on cleanup to prevent memory leaks.
- **Scene/node composition and instantiation** — compose complex scenes from smaller focused sub-scenes, instantiate at runtime with `preload`/`load`, and inject dependencies via `@export` references.
- **Resource and custom Resource types** — create typed Resource subclasses for configuration data, item definitions, and reusable assets rather than raw Dictionaries or hard-coded constants.
- **Autoloads and singletons** — use Godot Autoloads for global services (input manager, audio bus, save system) while keeping gameplay nodes scene-scoped.
- **GDExtension integration awareness** — understand when a GDExtension (C++ or Rust) is the right choice for performance-critical systems, and how to call into it from GDScript.
- **Coroutines with await** — use `await signal` and `await get_tree().create_timer(n).timeout` patterns for sequencing animations, cutscenes, and async operations cleanly.

## Best Practices
1. **Always use static typing** — add type annotations to all variables, parameters, and return types; enable `--check-only` or use a Godot 4 LSP to catch type errors early.
2. **Connect signals in `_ready()` not in the editor inspector for code-reviewed projects** — code-defined connections are version-control-friendly, searchable with `grep`, and avoid silent disconnection bugs when scenes are reorganized.
3. **Keep scenes focused — prefer composition over inheritance** — a small, single-purpose scene is easier to test, reuse, and maintain than a deep inheritance hierarchy; combine behaviors through child nodes and signals, not subclassing.
4. **Use custom Resources for configuration data instead of Dictionaries** — typed Resources are serializable, inspectable in the Godot editor, and catch property name typos at parse time rather than at runtime.
5. **Avoid deep node path dependencies** — do not hard-code long `$Parent/Child/Grandchild` paths; instead inject references via `@export` or signals so scenes remain portable and refactorable.

## When to Use
- Writing or reviewing Godot 4 GDScript code for gameplay, UI, or tooling.
- Designing scene hierarchies and deciding composition vs. inheritance trade-offs.
- Implementing signal-driven communication between decoupled game systems.
- Building custom Resource types for game data (items, levels, characters).
- Reviewing autoload architecture and global state management in Godot projects.
