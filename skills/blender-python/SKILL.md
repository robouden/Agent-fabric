---
name: blender-python
description: "Blender Python scripting for add-ons, asset automation, batch export workflows, and validation tools using bpy."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Blender Python API Skill

## Capabilities
- **`bpy.ops`, `bpy.data`, and `bpy.context` usage patterns** — understand when to use the data API (`bpy.data`) for direct object manipulation vs. the operator API (`bpy.ops`) for interactive commands, and how context overrides enable batch operations.
- **Blender add-on registration (`bl_info`, `register`/`unregister`)** — write correctly structured add-ons with `bl_info` metadata, idempotent `register()`/`unregister()` functions, and class registration for operators, panels, and properties.
- **Custom UI panels (`bpy.types.Panel`)** — create `VIEW3D_PT_*`, `PROPERTIES_PT_*`, and other panel subclasses that surface add-on controls where artists expect them without polluting unrelated panels.
- **Modal operators and timers** — implement `RUNNING_MODAL` operators for long-running or interactive tasks, using `context.window_manager.event_timer_add()` for progress feedback without blocking the UI thread.
- **Batch mesh/material/texture processing scripts** — iterate over `bpy.data.objects`, `bpy.data.materials`, and `bpy.data.images` to apply transforms, rename, remap, or validate assets across an entire scene or library.
- **FBX/glTF/OBJ export automation** — script `bpy.ops.export_scene.fbx()`, `bpy.ops.export_scene.gltf()`, and related operators with explicit settings to ensure deterministic, reproducible exports from CI pipelines.
- **Asset validation (naming conventions, poly count, UV checking)** — write validators that scan meshes for missing UVs, non-manifold geometry, naming violations, or poly-count budget overruns and report results as structured output.
- **Blender 4.x API compatibility awareness** — track breaking changes between Blender versions (e.g., material node API changes, bmesh vs. edit-mode mesh access) and write version-conditional code where needed.

## Best Practices
1. **Prefer `bpy.data` over `bpy.ops` for non-interactive scripting** — `bpy.ops` is context-sensitive and fragile in headless or batch runs; `bpy.data` provides direct, reliable access to mesh, material, and object data without requiring a specific UI state.
2. **Always save a copy before destructive batch operations** — batch scripts that modify meshes or materials can cause irreversible changes; save or version the `.blend` file before running any destructive operation in production.
3. **Use `bl_info` version field to track add-on compatibility** — keep `bl_info["blender"]` updated to the minimum supported version and `bl_info["version"]` bumped on each release so users know when an update is needed.
4. **Write operators that are undoable (use `UNDO` flag)** — add `bl_options = {'REGISTER', 'UNDO'}` to operators that modify scene data so artists can Ctrl+Z through add-on actions without surprise.
5. **Test in headless/background mode for CI pipeline scripts** — run `blender --background --python script.py` in CI to validate export and validation scripts work without a display server; scripts that require the UI will silently fail in CI.

## When to Use
- Writing Blender Python add-ons: operators, panels, properties, and registration boilerplate.
- Automating asset export pipelines: batch FBX/glTF export, LOD generation, or material baking from script.
- Building mesh and material validation tools for game art production quality gates.
- Creating batch processing scripts to enforce naming conventions, poly budgets, or UV layout requirements.
- Integrating Blender processing steps into CI/CD pipelines for automated asset validation.
