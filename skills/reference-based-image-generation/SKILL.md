---
name: reference-based-image-generation
description: "Generate visually consistent image sets (animation frames, icon families, asset variants) by first creating a reference image, then using it as input context for every derivative frame."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Reference-Based Image Generation Skill

## Capabilities
- **Generate consistent animation sprite sets** — walk cycles, attack sequences, idle poses, death animations where every frame matches the same character/object
- **Generate consistent asset families** — icon sets, UI element variants, tileable pattern sets that share visual identity
- **Two-pass workflow** — first create a high-quality reference, then produce derivatives that stay on-model
- **Prompt review and iteration** — detect off-model results and regenerate with refined prompts

## When to Use
Use this skill instead of plain `openai-image-generation` when:
- You need **multiple images of the same subject** that must look visually identical
- Single-pass generation produces inconsistent results across frames (different colors, proportions, details)
- You are generating animation frames, sprite sheets, icon families, or any set where visual cohesion matters

## Workflow

Before uploading any local or reference image to an external API, get explicit user approval for that upload. The user must confirm the asset is non-sensitive, authorized for third-party processing, and is not a private screenshot, credential-bearing image, personal image without consent, or copyrighted third-party asset without the necessary rights or consent.

### Pass 1 — Generate the Reference Image

Create a single, high-quality image of the subject. This image is **not used in the final product** — it exists solely as a visual anchor.

**Reference prompt guidelines:**
- Describe the subject in full detail: colors, proportions, clothing, distinguishing features
- Use a neutral pose (front-facing, idle, well-lit)
- Use `quality: high` for maximum detail
- Include your project's **style anchor phrase** for art direction consistency
- Save to a `_reference.png` file alongside the output directory

```python
import openai, base64, io
from pathlib import Path
from PIL import Image

client = openai.OpenAI()

ref_prompt = (
    "<style anchor phrase>, "
    "<full subject description with colors, proportions, distinguishing features>, "
    "neutral standing pose, front-facing, well-lit, centered in frame, "
    "transparent background, no text, no watermarks"
)

response = client.images.generate(
    model="gpt-image-1",
    prompt=ref_prompt,
    size="1024x1024",
    quality="high",
)

ref_bytes = base64.b64decode(response.data[0].b64_json)
Path("output/_reference.png").write_bytes(ref_bytes)
```

### Pass 2 — Generate Derivatives Using the Reference

For each derivative image (animation frame, variant, etc.), send the reference image **as input** alongside a prompt that describes what this specific frame should show.

**Key principle:** The prompt must explicitly instruct the model to reproduce the same subject from the reference, then apply the specific action/variation.

**Derivative prompt template:**
```
"Generate the image same as <subject description>, using this file as reference,
create exactly the same <subject> <specific action/variation for this frame>.
<style anchor phrase>, <format cues>, no text, no watermarks"
```

```python
import time

def generate_with_reference(ref_path: Path, prompt: str, out_path: Path, size: int = 256) -> bool:
    """Generate a derivative image using a reference image as input context."""
    if out_path.exists():
        return False  # skip existing

    ref_bytes = ref_path.read_bytes()
    ref_file = ("reference.png", ref_bytes, "image/png")

    try:
        response = client.images.edit(
            model="gpt-image-1",
            image=[ref_file],
            prompt=prompt,
            size="1024x1024",
            quality="medium",
        )
        raw = base64.b64decode(response.data[0].b64_json)
        img = Image.open(io.BytesIO(raw)).resize((size, size), Image.LANCZOS)
        img.save(out_path, "PNG")
        return True
    except Exception as e:
        print(f"ERR {out_path.name}: {e}")
        return False
```

**Rate limiting:** Add `time.sleep(1.5)` between API calls to avoid rate limits during batch generation.

### Pass 3 — Review and Fix

After generating all derivatives, review the results:

1. **Visual scan** — look for frames where the character/subject drifts off-model (wrong colors, different proportions, missing features)
2. **Identify prompt weaknesses** — which frames consistently fail? Common issues:
   - Back/rear views losing detail → add more descriptive cues about back-facing features
   - Action poses changing body proportions → emphasize "same proportions and size as reference"
   - Color drift → explicitly name colors in every derivative prompt
3. **Regenerate** — delete the off-model frames and re-run with revised prompts
4. **Iterate** — repeat until all frames are visually consistent

## Prompt Engineering for Consistency

### Style Anchor Phrase
Define once per project, include in every prompt:
```
"top-down 2D game character sprite, casual mobile game art style,
thick black outlines, vibrant saturated colors, chibi cartoon proportions"
```

### Consistency Reinforcement Cues
Add these to derivative prompts to maintain visual fidelity:
- `"exactly the same character as the reference image"`
- `"same colors, same proportions, same outfit, same features"`
- `"maintain identical art style and line weight"`
- `"only change the pose/action, keep everything else the same"`

### Action Description Precision
Be specific about what changes in each frame:
- ❌ `"walking"` — too vague, model may change everything
- ✅ `"mid-stride walking pose, right foot forward, left arm forward, same character same outfit"` — precise, constrained

## API Reference

### images.edit (reference-based generation)

The `images.edit` endpoint accepts one or more input images alongside a text prompt. Use this for Pass 2.

| Parameter | Value | Notes |
|-----------|-------|-------|
| `model` | `gpt-image-1` | Required for image input support |
| `image` | `[("ref.png", bytes, "image/png")]` | Reference image as file tuple |
| `prompt` | string | Must describe the desired output AND reference the input |
| `size` | `1024x1024` | Generate at high res, resize after |
| `quality` | `medium` | Use `high` for hero/key frames, `medium` for bulk |

### images.generate (reference creation)

Use the standard generation endpoint for Pass 1 (no input image needed).

| Parameter | Value | Notes |
|-----------|-------|-------|
| `model` | `gpt-image-1` | Highest quality generation |
| `prompt` | string | Full subject description, neutral pose |
| `size` | `1024x1024` | Square for characters; adjust for other subjects |
| `quality` | `high` | Maximum detail for the reference |

## Error Handling

| Error | Cause | Fix |
|-------|-------|-----|
| `BadRequestError` (content policy) | Prompt rejected | Rephrase — avoid violence descriptors, use "defeated pose" instead of explicit death |
| `RateLimitError` | Too many requests | Increase `time.sleep()` between calls to 2-3 seconds |
| Off-model frames | Weak prompt or complex pose | Strengthen consistency cues, simplify the action description |
| Color drift | Model reinterpreting palette | Explicitly name hex codes or color names in every prompt |

## Example: Character Animation Sprite Set

```python
STYLE = "top-down 2D game sprite, casual mobile art, thick outlines, vibrant colors, chibi proportions"
CHAR = "tough brawler hero, muscular build, grey hoodie and jeans, orange sneakers"

# Pass 1: Reference
ref_prompt = f"{STYLE}, {CHAR}, neutral standing pose, front-facing, transparent background, no text"
# → generates _reference.png

# Pass 2: Walk cycle frame
frame_prompt = (
    f"Generate the image same as {CHAR}, using this file as reference, "
    f"create exactly the same character in a mid-stride walking pose, "
    f"right foot forward, left arm forward, facing right (side view), "
    f"{STYLE}, transparent background, no text, no watermarks"
)
# → generates with reference image as input
```

## Integration with Other Skills
- Builds on **OpenAI Image Generation** skill for API mechanics and post-processing
- Used by **Technical Artist** and **Game Audio Engineer** (for visual asset consistency)
- Can be called by any agent that needs to generate multiple related images
