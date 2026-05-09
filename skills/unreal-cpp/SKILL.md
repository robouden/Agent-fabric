---
name: unreal-cpp
description: "Unreal Engine C++ and Blueprint patterns: UPROPERTY/UFUNCTION macros, Gameplay Ability System, component design, and reflection system usage."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Unreal C++ & Blueprints Skill

## Capabilities
- **UPROPERTY/UFUNCTION/UCLASS macro patterns** — apply Unreal's reflection system macros correctly to expose properties and functions to the editor, Blueprints, and network replication.
- **Actor/Component/Subsystem design** — structure gameplay code using Actors as world-placed entities, ActorComponents for reusable behaviors, and UGameInstanceSubsystem/UWorldSubsystem for global services.
- **Gameplay Ability System (GAS) integration** — implement AttributeSets, GameplayAbilities, GameplayEffects, and AbilitySystemComponents for abilities, buffs, cooldowns, and character stats at scale.
- **BlueprintCallable/BlueprintImplementableEvent patterns** — define clean C++/Blueprint boundaries where C++ handles logic and Blueprint handles creative content customization.
- **Unreal smart pointers** — use `TWeakObjectPtr`, `TSharedPtr`, and `TObjectPtr` appropriately to manage object lifetimes and avoid dangling pointer crashes.
- **Delegates and multicast delegates** — declare and bind `FDelegate`, `FMulticastDelegate`, and dynamic multicast delegates for event-driven decoupling between systems.
- **Async loading and soft object references** — use `TSoftObjectPtr` and `FStreamableManager` to load assets on demand and control memory usage in large worlds.
- **Lyra/modular game feature plugins awareness** — understand Unreal's modular GameFeature plugin architecture for adding gameplay content without modifying the base game module.

## Best Practices
1. **Prefer Subsystems over singletons for global state** — `UGameInstanceSubsystem` and `UWorldSubsystem` are lifecycle-managed, testable, and garbage-collected; static singletons cause hard-to-diagnose lifetime and multiplayer authority bugs.
2. **Use GAS for abilities/buffs/cooldowns at scale** — GAS handles replication, prediction, stacking, and complex interaction logic that would otherwise require bespoke solutions; adopt it early rather than migrating later.
3. **Avoid hard references — prefer soft references for streaming** — `TSoftObjectPtr` avoids loading assets at startup and enables streaming-level workflows; hard `UObject*` members force referenced assets to stay resident in memory.
4. **Document all BlueprintCallable functions so designers can use them without C++ knowledge** — include `meta=(ToolTip=...)` and category annotations; designers should never need to open a `.cpp` file to understand a Blueprint node.
5. **Profile with Unreal Insights before optimizing** — measure with the Insights profiler, CPU traces, and GPU visualizer before changing algorithms; premature optimization in Unreal frequently targets the wrong bottleneck.

## When to Use
- Writing Unreal C++ gameplay code for actors, components, subsystems, and ability systems.
- Designing the C++/Blueprint boundary for a feature or system.
- Implementing or reviewing GAS-based abilities, effects, and attributes.
- Reviewing Unreal actor/component architecture for correctness, performance, and replication readiness.
- Advising on async loading, soft references, and memory budget management.
