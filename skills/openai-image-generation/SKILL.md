---
name: openai-image-generation
description: "Generate game UI assets, concept art, and textures from text prompts using the OpenAI Images API (gpt-image-1 / DALL-E 3)."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# OpenAI Image Generation Skill

## Capabilities
- **Generate game UI assets** — backgrounds, buttons, icons, panels, and HUD elements from text prompts
- **Generate concept art** — hero portraits, character art, and scene illustrations
- **Generate textures and decorative elements** — dividers, borders, frames, and tileable textures
- **Iterate on results** — refine and regenerate images with revised prompts based on visual feedback
- **Save assets to disk** — decode base64 output and write PNG files to the target directory

## Workflow

Follow these steps in order for every image generation task.

### Step 1 — Define the Prompt
Write a precise, detailed text prompt describing the image:
- **Art style**: e.g., `"dark fantasy game UI element, hand-painted, high contrast, no text"`
- **Dimensions intent**: square for icons/portraits, wide for panels/backgrounds, tall for vertical banners
- **Color palette**: reference the project theme explicitly — e.g., `"dark navy blue and steel grey color scheme, metallic sheen"`
- **Isolation / format cues**: `"isolated on transparent background"`, `"tileable texture"`, `"icon design"`
- **Negative cues**: always append `"no text, no watermarks, no signatures"` to avoid clutter

Keep a **style anchor phrase** consistent across all prompts for a project (e.g., `"dark knight RPG game UI, iron and steel aesthetic, dark fantasy"`). This ensures visual cohesion across all generated assets.

### Step 2 — Choose Model and Parameters

| Parameter | Recommended value | Notes |
|-----------|-------------------|-------|
| `model`   | `gpt-image-1`     | Highest quality; use `dall-e-3` as fallback |
| `size`    | `1024x1024`       | Square icons, portraits, and item art |
|           | `1536x1024`       | Wide panels, banners, and backgrounds |
|           | `1024x1536`       | Tall backgrounds and vertical banners |
| `quality` | `high`            | Final assets; use `medium` for drafts |
| `format`  | `png`             | Always PNG for game assets |

### Step 3 — Make the API Call

Use the OpenAI Python SDK. The `OPENAI_API_KEY` environment variable must be set.

```python
import openai, base64, os
from pathlib import Path

client = openai.OpenAI()  # uses OPENAI_API_KEY env var

response = client.images.generate(
    model="gpt-image-1",
    prompt="<your detailed prompt>",
    size="1536x1024",   # wide background; use 1024x1024 for square, 1024x1536 for tall
    quality="medium",   # valid: low | medium | high | auto
)

# gpt-image-1 returns base64-encoded PNG
image_bytes = base64.b64decode(response.data[0].b64_json)
Path("output.png").write_bytes(image_bytes)
```

> **DALL-E 3 difference**: images are returned as URLs instead of base64.
> Use `response.data[0].url` and download the image with `requests.get(url).content`.

### Step 4 — Post-Process to Exact Dimensions
Resize or crop the generated image to the exact pixel dimensions required by the game using Pillow:

```python
from PIL import Image

img = Image.open("output.png").resize((target_w, target_h), Image.LANCZOS)
img.save("public/assets/asset-name.png")
```

For icons that need a transparent background, convert to RGBA before saving:
```python
img = img.convert("RGBA")
img.save("public/assets/icon-name.png")
```

### Step 5 — Validate
After saving the asset, verify:
- The file exists at the expected path (`Path("...").exists()`)
- Dimensions are correct (`Image.open(...).size == (target_w, target_h)`)
- The asset looks visually correct — take a browser screenshot if a dev server is running
- No text, watermarks, or unintended artefacts are present

### Step 6 — Iterate
If the result doesn't match expectations:
1. Inspect what's wrong — wrong style, wrong colors, wrong composition, unwanted elements
2. Revise the prompt — add more specific style keywords, strengthen color constraints, or add negative cues
3. Adjust `size` if the aspect ratio is off
4. Regenerate and re-validate

## Prompt Engineering Guidelines

### Structure of a strong prompt
```
<style anchor>, <subject description>, <color palette>, <format/isolation cue>, <negative cues>
```

**Example — inventory slot icon:**
> `"dark knight RPG game UI, iron and steel aesthetic, dark fantasy, sword inventory icon, isolated on dark background, dark navy blue and silver metallic color scheme, high contrast, stylized, no text, no watermarks"`

**Example — main menu background:**
> `"dark knight RPG game UI, iron and steel aesthetic, dark fantasy, epic castle hall background, dark navy blue and steel grey, moody atmospheric lighting, 16:9 widescreen composition, no text, no watermarks"`

**Example — HUD health bar panel:**
> `"dark knight RPG game UI, iron and steel aesthetic, dark fantasy, horizontal health bar frame, metallic border with rivets, dark background, red accent, isolated element, no text, no watermarks"`

### Key rules
- **Be specific about art style** — vague prompts produce generic results
- **Reference color explicitly** — name colors, don't assume the model will match the palette
- **One subject per prompt** — don't ask for a full UI screen in one call; generate elements separately
- **Consistency** — reuse the same style anchor phrase for every asset in a project

## Error Handling

| Error | Cause | Fix |
|-------|-------|-----|
| `AuthenticationError` | `OPENAI_API_KEY` not set or invalid | Configure `OPENAI_API_KEY` through an approved secret manager or local untracked env file loaded safely with a dotenv library; use placeholders only in examples and never paste real keys into chat, logs, commits, or sample commands |
| `BadRequestError` (content policy) | Prompt rejected for policy violation | Rephrase — avoid specific references to violence, real persons, or explicit content |
| `RateLimitError` | Too many requests in a short window | Add `time.sleep(1)` between calls in batch loops |
| `openai.APIConnectionError` | No internet or proxy issue | Check network connectivity and proxy settings |

**Batch generation with rate-limit guard:**
```python
import time

assets = [("icon-sword", "sword icon prompt ..."), ("icon-shield", "shield icon prompt ...")]
for name, prompt in assets:
    response = client.images.generate(model="gpt-image-1", prompt=prompt, size="1024x1024", quality="medium")
    Path(f"public/assets/{name}.png").write_bytes(base64.b64decode(response.data[0].b64_json))
    time.sleep(1)  # respect rate limits
```

## Integration with Game-Focused Agents

This skill is especially useful for the `pixijs-prototype-specialist` and `technical-artist` agents when:
- Placeholder UI or scene art needs a faster path to polished visuals
- Exported design assets need complementary generated textures, icons, or decorative treatments
- Concept art, marketing-style mockups, or rapid visual directions are needed before a manual art pass
- A prototype needs stronger visual fidelity for stakeholder review, demos, or playtests

The `code-writer` agent may also use this skill when asked to automate image-generation pipelines or asset post-processing scripts.

## When to Use
- Game UI assets that require artistic quality beyond programmatic drawing
- Character or creature concept art and portraits
- Background scenes, environments, and atmospheric illustrations
- Decorative textures, borders, and frames that match a specific visual theme
- Rapid prototyping of visual style before commissioning professional art
