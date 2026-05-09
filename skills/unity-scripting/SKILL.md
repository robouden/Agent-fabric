---
name: unity-scripting
description: "Unity C# scripting patterns using MonoBehaviour, ScriptableObjects, event channels, dependency injection, and testable component design."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Unity Scripting Skill

## Capabilities
- **MonoBehaviour lifecycle management** — understand and apply Awake, OnEnable, Start, Update, FixedUpdate, LateUpdate, OnDisable, and OnDestroy ordering across components and scenes.
- **ScriptableObject-driven data and events** — design data containers and event channels as ScriptableObject assets to decouple runtime systems from scene hierarchy.
- **Event channel / message bus patterns** — implement typed event channels (UnityEvent, C# Action, or ScriptableObject-based) to allow publisher/subscriber communication without direct references.
- **Dependency injection via Zenject/VContainer** — structure projects with DI containers so components declare their dependencies explicitly and are testable in isolation.
- **Coroutines and async/await in Unity context** — choose between coroutines and async/await appropriately for IO, animation sequencing, timed events, and network calls while handling Unity's main-thread requirement.
- **Editor scripting and custom inspectors** — write PropertyDrawers, EditorWindows, CustomEditors, and MenuItem-based tools to improve designer workflows and data validation inside the Unity Editor.
- **Addressable asset loading** — implement Addressables-based runtime loading, dependency management, and unload flows to control memory budgets across scenes.

## Best Practices
1. **Prefer ScriptableObjects for shared state over singletons** — ScriptableObject-based shared data is serializable, inspectable, resettable, and can be swapped per-environment without code changes; singletons hide coupling and complicate testing.
2. **Use event channels to decouple systems** — emit events through ScriptableObject or delegate-based channels so publishers and subscribers never need direct references to each other.
3. **Keep MonoBehaviours thin** — MonoBehaviours should wire up and delegate to plain C# classes that contain actual logic; this makes logic testable with NUnit without requiring the Unity runtime.
4. **Use async/await over coroutines for IO-heavy work** — async/await composes more cleanly with Task-based APIs, propagates exceptions properly, and is easier to unit test; reserve coroutines for frame-by-frame animation or legacy code.
5. **Isolate Unity API calls so pure logic can be unit tested** — wrap UnityEngine calls behind interfaces or adapters so plain C# logic under test does not require a running Unity instance.

## When to Use
- Writing or reviewing Unity C# gameplay code, component lifecycles, and system interactions.
- Refactoring MonoBehaviour-heavy code toward cleaner, testable architectures.
- Designing event channels, shared state, and data-driven configuration in Unity.
- Implementing ScriptableObject-based systems for designers to tune without code changes.
- Adding editor tooling, custom inspectors, or addressable loading strategies to Unity projects.
