---
name: shader-programming
description: "Cross-platform shader authoring for games: GLSL/HLSL/ShaderLab, Shader Graph, Godot shaders, Niagara, and WebGL/PixiJS Filter patterns."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Shader Programming Skill

## Capabilities
- **GLSL vertex/fragment shader authoring (WebGL 1/2)** — write and review GLSL shaders targeting WebGL 1.0 and 2.0 contexts, including `varying`/`in`/`out` syntax differences and extension requirements.
- **HLSL for DirectX/Unity pipelines** — author HLSL shaders for Unity's Built-In, URP, and HDRP render pipelines using both hand-written ShaderLab and HLSL include files.
- **ShaderLab surface shaders and URP/HDRP Lit/Unlit pass structure** — structure ShaderLab shader passes with correct Tags, Stencil/Blend/ZWrite/Cull states, and cbuffer/CBUFFER_START layouts for SRP batching.
- **Unity Shader Graph node authoring and subgraphs** — build modular Shader Graph assets using subgraphs, Custom Function nodes, and property exposure for artist-tweakable parameters.
- **Godot visual shader and inline shader code** — author Godot VisualShader graphs and write inline `shader_type canvas_item / spatial / particles` shaders with Godot's GLSL dialect.
- **Unreal Material Graph and Niagara shader stages** — build parameterized Unreal Material functions, Material Instances, and Niagara GPU simulation stages within Unreal's node-based material system.
- **PixiJS Filter subclass and GLSL string shader integration** — create PixiJS `Filter` subclasses with embedded GLSL strings, uniform management, and `apply()` overrides for WebGL post-processing effects.
- **Performance budgeting** — analyze ALU instruction count, texture fetch count, fill-rate cost, and overdraw impact to hit target GPU frame budgets on mobile and low-end hardware.

## Best Practices
1. **Always define which render pipeline and API level the shader targets** — a shader written for URP/HLSL will not work in HDRP or Built-In without changes; state the target explicitly in comments or documentation before authoring.
2. **Minimize texture samples in the fragment stage** — each texture fetch has latency cost on GPU; pack multiple data channels into a single RGBA texture rather than sampling separate textures for each property.
3. **Avoid dynamic branching on GPU** — `if` statements that vary per-fragment cause warp divergence on GPU; replace with `step()`, `mix()`, `smoothstep()`, or compile-time `#ifdef` variants wherever possible.
4. **Use precision qualifiers (`mediump`/`lowp`) for mobile targets** — `lowp` for colors and normals, `mediump` for UVs and standard math; `highp` only where needed for positions or distance math; incorrect precision causes visible artifacts.
5. **Document exposed parameters with ranges and artistic intent** — list every uniform, its type, its expected range, and its visual effect in a comment block so artists and future maintainers can tune without reading shader math.
6. **Test on low-end hardware before shipping** — desktop GPUs hide shader performance issues; always profile on target mobile or low-spec hardware to measure real fill-rate and ALU cost.

## When to Use
- Authoring or reviewing shaders for Unity (ShaderLab/HLSL/Shader Graph), Godot (GLSL/VisualShader), Unreal (Material Graph/HLSL), or WebGL/PixiJS (GLSL/Filter).
- Debugging visual artifacts: Z-fighting, alpha sorting, precision errors, seam artifacts, banding, or incorrect blending.
- Optimizing shader cost: reducing texture samples, eliminating dynamic branching, targeting mobile precision budgets.
- Building reusable shader libraries, subgraphs, or material functions for a team's art pipeline.
