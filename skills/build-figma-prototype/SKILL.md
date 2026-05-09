---
name: build-figma-prototype
description: "Prompt template for building a clickable PixiJS prototype end-to-end from a Figma design file."
version: 0.1.0
metadata:
  hermes:
    category: prompt
---
# Build Figma Prototype

## Context

I need to build an interactive clickable prototype from a Figma design.

| Variable | Value |
|----------|-------|
| **Figma file key** | `{{FIGMA_FILE_KEY}}` |
| **Root node ID** | `{{FIGMA_ROOT_NODE}}` |
| **Project name** | `{{PROJECT_NAME}}` |
| **Viewport** | `{{VIEWPORT}}` *(default: 360×640)* |

The prototype should use **PixiJS v8 + Vite + TypeScript** and faithfully replicate the Figma screens using real exported PNG assets.

Refer to the `code-generation` and `code-analysis` skills for implementation patterns and code review conventions.

## Workflow Note
- Use the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`) to select the agent for each step.
- For end-to-end coordination, you can select the repo's **orchestrator** first.
- Use Hermes parallel-skill execution for independent investigation or QA tracks only when parallel work helps.
- Use `@` for file and path mentions, not agent selection.

---

## Steps

### 1 — Explore Design

**Role:** Code Investigator

Select the code-investigator agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Using the `code-analysis` skill, explore the Figma file `{{FIGMA_FILE_KEY}}` starting from root node `{{FIGMA_ROOT_NODE}}`.
>
> Produce a structured report containing:
> - A table mapping every top-level screen node ID → proposed `SceneKey` name → scene class name
> - A flat list of all visual assets required (alias, source node ID, recommended filename under `public/assets/`)
> - A list of interactive elements per scene (buttons, navigation targets)
> - Figma reference screenshots saved or URLs noted for each screen
> - Any design tokens (colours, font sizes, spacing) to extract into `src/theme/`

---

### 2 — Scaffold & Implement Scenes

**Role:** Code Writer

Branch: `feat/{{PROJECT_NAME}}-prototype`

Select the code-writer agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Using the investigation report from Step 1 and the `code-generation` skill, on branch `feat/{{PROJECT_NAME}}-prototype`:
>
> 1. Scaffold a fresh PixiJS v8 + Vite + TypeScript project at `projects/sources/{{PROJECT_NAME}}/` (run `npm create vite@latest` + `npm install pixi.js`).
> 2. Create the full directory structure: `src/scenes/`, `src/theme/`, `src/types/`, `public/assets/`.
> 3. Implement `BaseScene`, `SceneManager`, and `main.ts` with the asset-loading manifest and `__sceneManager` dev hook — viewport `{{VIEWPORT}}`.
> 4. Implement every scene class identified in Step 1 using **programmatic `Graphics`** as placeholder art (rectangles in approximate Figma colours). Mark each placeholder with a `// TODO: replace with Sprite` comment.
> 5. Wire all navigation interactions between scenes.
> 6. Run `npm run build` and fix all TypeScript errors before finishing.

---

### 3 — Export Assets & Replace Placeholders

**Role:** Code Writer

Stay on the same branch.

Select the code-writer agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Using the `code-generation` skill, replace every programmatic placeholder with real Figma assets:
>
> 1. For each asset in the manifest from Step 1, download the PNG from its Figma design-context URL into `public/assets/` following the `<scene>-<element>.png` naming convention.
> 2. Validate every file: `file public/assets/**/*.png` — re-download any that fail.
> 3. Add each asset alias to the `Assets.load([…])` array in `main.ts`.
> 4. In each scene, remove `Graphics` placeholders and replace them with `new Sprite(Assets.get('alias'))`, setting `x / y / width / height` from `figma-get_design_context` node coordinates.
> 5. Run `npm run build` and fix all TypeScript errors.

---

### 4 — Code Review

**Role:** Code Reviewer

Select the code-reviewer agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Review the implementation on branch `feat/{{PROJECT_NAME}}-prototype` against the `code-generation` and `code-analysis` skills.
>
> Focus on:
> - Correct PixiJS v8 API usage (`Assets.get`, async `app.init`, `eventMode: 'static'`)
> - No remaining `Graphics` placeholders for static visual elements
> - `SceneManager.navigate()` called correctly from all interactive elements
> - All assets validated and loaded before any scene is instantiated
> - TypeScript types complete — no `any`, no missing `SceneKey` entries
> - `npm run build` passes cleanly

---

### 5 — Apply Feedback & Open PR

**Role:** Code Writer

Stay on the same branch.

Select the code-writer agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Apply all review feedback from Step 4 on branch `feat/{{PROJECT_NAME}}-prototype`.
> Run `npm run build` to confirm the build is clean.
> Then push the branch and open a PR:
> ```bash
> git push -u origin feat/{{PROJECT_NAME}}-prototype
> gh pr create \
>   --title "feat: PixiJS prototype for {{PROJECT_NAME}}" \
>   --body "## Summary\nClickable prototype built from Figma file `{{FIGMA_FILE_KEY}}`. Implements scenes: $(git --no-pager diff --name-only origin/main... | grep Scene | tr '\n' ', ')." \
>   --head feat/{{PROJECT_NAME}}-prototype
> ```
> Report the PR URL.

---

### 6 — Visual QA

Use the coordinating agent you selected for the workflow. If you want repo-standard coordination, keep the orchestrator on point for this QA loop.

For **each scene** in the prototype:

1. Start the dev server: `npm run dev -- --port 5180 --host` inside `projects/sources/{{PROJECT_NAME}}/`.
2. Navigate to the scene via Chrome DevTools MCP console:
   ```js
   window.__sceneManager.navigate('<sceneKey>')
   ```
3. Take a browser screenshot with `chrome-devtools-take_screenshot`.
4. Take the Figma reference screenshot with `figma-get_screenshot` using the scene's `nodeId` and `{{FIGMA_FILE_KEY}}`.
5. Compare the two screenshots side-by-side. For each gap found, route a targeted fix to the **Code Writer** role:
   - Missing sprite → add asset + `Sprite` to scene
   - Position/size mismatch → adjust `x / y / width / height` to match Figma coordinates
   - Remaining `Graphics` shape → replace with `Sprite`
6. Repeat until all scenes pass visual QA, then comment results on the PR.
