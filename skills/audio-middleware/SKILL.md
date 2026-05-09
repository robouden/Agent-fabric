---
name: audio-middleware
description: "Game audio integration patterns for FMOD Studio and Wwise: event architecture, state/parameter design, mixer hierarchy, spatial audio, and runtime budget enforcement."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Audio Middleware Skill

## Capabilities
- **FMOD Studio event, snapshot, bus, and VCA design** — structure FMOD projects with well-organized event folders, snapshot priority/ducking, bus mixing hierarchy, and VCA groupings for runtime mix control.
- **Wwise Actor-Mixer, Event, State, and RTPC hierarchy** — build Wwise projects with logical Actor-Mixer hierarchies, typed Events, State Groups for music/ambient transitions, and RTPC curves for continuous parameter-driven audio.
- **Unity/Unreal/Godot middleware SDK integration** — integrate FMOD or Wwise SDKs into game engine projects: handle initialization, bank loading, event instance lifecycle, and shutdown correctly across scene loads.
- **3D spatializer and attenuation curves** — configure distance-based attenuation, spread, and 3D panning for in-world audio sources; tune falloff curves to match environment scale and player movement speed.
- **Adaptive music transitions (horizontal/vertical re-sequencing)** — design interactive music systems using horizontal re-sequencing (section transitions) and vertical re-mixing (layer enabling/disabling) driven by game state.
- **Voice budget management and virtualization** — set per-category and global voice limits, configure virtualization steal priorities, and test behavior when the budget is exceeded in worst-case scenarios.
- **Audio memory and streaming budget analysis** — profile compressed bank size, decoded sample memory, and streaming bandwidth; design bank partitioning to fit platform memory constraints.

## Best Practices
1. **Model audio as state machines — tie events to gameplay states not ad hoc triggers** — audio that fires from `PlaySound()` calls scattered through gameplay code is unmaintainable; instead, have audio respond to well-defined gameplay state transitions.
2. **Name events with a consistent hierarchy (category/subcategory/name)** — example: `character/player/footstep_concrete`; consistent naming enables batch editing, searching, and handoff between audio designer and programmer.
3. **Define voice limits and virtualization priorities before content scales** — adding voice budgets after the project has hundreds of events requires auditing every event; establish the policy early and enforce it in review.
4. **Always set max distance and falloff on 3D events** — events without a max distance keep emitter references alive forever; incorrect falloff causes audio popping or events audible from the entire level.
5. **Test on target platform RAM and CPU budgets early** — PC development hides memory and CPU audio cost; profile on console or mobile targets as early as possible to catch bank loading and decode cost before it becomes a blocker.

## When to Use
- Designing FMOD or Wwise project structure: event hierarchy, buses, snapshots, States, RTPCs, or bank partitioning.
- Integrating FMOD or Wwise SDK into Unity, Unreal, or Godot: bank loading, event playback, parameter management, and shutdown flows.
- Reviewing audio implementation for correctness, voice budget, memory cost, or 3D spatializer configuration.
- Designing adaptive music systems using horizontal or vertical re-sequencing.
- Diagnosing runtime audio issues: pops, missing sounds, incorrect spatialization, budget overruns, or event lifecycle leaks.
