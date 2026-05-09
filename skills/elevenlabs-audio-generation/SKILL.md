---
name: elevenlabs-audio-generation
description: "Generate short game-ready sound effects, UI tones, and bark-style voice assets from text prompts using the ElevenLabs Sound Generation API."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# ElevenLabs Audio Generation Skill

## Capabilities
- **Generate short combat SFX** — create sword swings, impact sweeteners, projectile launches, magic bursts, and other punchy one-shot sounds for prototype or production-prep passes.
- **Generate UI sounds** — produce confirm pings, error buzzes, reward stingers, menu open/close ticks, and other lightweight interface feedback assets.
- **Generate short voice and bark assets** — create brief non-verbal vocals, stylized barks, and placeholder voice-style cues for gameplay feedback or prototyping.
- **Iterate fast with controlled variations** — request several prompt variants with small wording changes to build repetition-safe sets instead of a single overused sound.
- **Export and validate deliverables** — save generated files to disk, verify format and duration, and flag assets that still need editing, normalization, layering, or cleanup.

## Workflow

Follow these steps in order for every ElevenLabs audio-generation task.

### Step 1 — Define the Asset Brief
Write a compact but specific brief before generating anything:
- **Gameplay purpose** — combat hit, UI confirm, pickup reward, enemy bark, etc.
- **Target duration** — usually `0.5-3.0s` for game-ready one-shots; keep prompts explicit for short assets.
- **Tone and material cues** — metallic, arcane, airy, crunchy, synthetic, wooden, radio-filtered, cartoon, realistic.
- **Mix role** — foreground hero sound, subtle UI feedback, background sweetener, placeholder bark.
- **Variation count** — default to `3-6` variations for assets that will repeat frequently.

Keep prompts focused on **one sound idea at a time**. If the game needs a layered result, generate the components separately and combine them later in a DAW or engine pipeline.

### Step 2 — Prepare Access and Output Paths
The ElevenLabs API key must be provided through the environment.

- Use `ELEVENLABS_API_KEY` from the current shell or a sourced `.env` file.
- **Never** hardcode the key into scripts, commit it to the repo, or paste the full secret into logs.
- If you need to confirm the key is present, check only that the variable exists — do not echo the full value.
- Choose an output path and naming convention up front, for example: `assets/audio/sfx/ui/confirm-01.mp3`.

Recommended defaults for short prototypes:
- `output_format`: `mp3_44100_128` when available, otherwise `mp3_22050_32`
- `prompt_influence`: start around `0.3-0.5` for a balance of prompt fidelity and variation

Per the ElevenLabs sound-generation API docs, `model_id` is supported but optional and currently defaults to `eleven_text_to_sound_v2`. Omit it in normal examples unless you specifically need to pin or override the documented default.

### Step 3 — Make the API Call
Use the ElevenLabs sound-generation endpoint directly for reproducible, scriptable output.

**Practical cURL example — short combat SFX:**

```bash
mkdir -p assets/audio/sfx/combat

curl -sS -X POST \
  "https://api.elevenlabs.io/v1/sound-generation?output_format=mp3_44100_128" \
  -H "xi-api-key: $ELEVENLABS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Short sharp fantasy sword slash with a metallic whoosh and tight impact tail, clean one-shot, no music",
    "duration_seconds": 2.0,
    "prompt_influence": 0.4
  }' \
  --output assets/audio/sfx/combat/sword-slash-01.mp3
```

**Python example — short UI sound:**

```python
import os
from pathlib import Path
import requests

api_key = os.environ["ELEVENLABS_API_KEY"]
out_path = Path("assets/audio/sfx/ui/confirm-01.mp3")
out_path.parent.mkdir(parents=True, exist_ok=True)

response = requests.post(
    "https://api.elevenlabs.io/v1/sound-generation",
    params={"output_format": "mp3_44100_128"},
    headers={
        "xi-api-key": api_key,
        "Content-Type": "application/json",
    },
    json={
        "text": "Short clean sci-fi UI confirm beep, soft attack, polished, no voice, no music",
        "duration_seconds": 0.8,
        "prompt_influence": 0.35,
    },
    timeout=60,
)
response.raise_for_status()
out_path.write_bytes(response.content)
```

### Step 4 — Generate Variations for Repetition Control
Do not stop at one file for sounds that will fire often.

1. Generate `3-6` variations with small prompt adjustments.
2. Change one axis at a time: brighter/darker tone, shorter/longer tail, softer/harder attack, more/less synthetic.
3. Name files consistently: `sword-slash-01.mp3`, `sword-slash-02.mp3`, `sword-slash-03.mp3`.
4. Keep short notes on which variation best fits gameplay readability.

Treat the direct `sound-generation` endpoint as the reproducible path for prototype batches: once authentication and output paths are correct, generate several controlled variations so repeated playback does not feel robotic.

### Step 5 — Validate the Output
After every generation pass, verify the asset before handing it off.

- **File exists** — confirm the output path was written successfully.
- **Format is correct** — inspect with `file`, `ffprobe`, or an equivalent metadata tool.
- **Duration is in range** — verify the sound length matches gameplay needs; trim if the tail is too long.
- **Manual listen pass** — audition the file on speakers or headphones in addition to waveform inspection.
- **Clipping / distortion check** — listen for crunchy transients, brittle highs, pumping, or broken tails.
- **Loudness consistency** — normalize or batch-master to the project's loudness target; keep peaks below roughly `-1 dBFS` before engine import and align one-shots to a consistent perceived level.
- **Engine-fit follow-up** — convert to the runtime codec the project expects (`wav`, `ogg`, middleware bank import, etc.) if MP3 is only an intermediate artifact.

A lightweight validation example:

```bash
test -f assets/audio/sfx/combat/sword-slash-01.mp3
file assets/audio/sfx/combat/sword-slash-01.mp3
ffprobe -v error -show_entries format=duration -of default=nw=1:nk=1 assets/audio/sfx/combat/sword-slash-01.mp3
```

### Step 6 — Integrate or Post-Process
Generated audio is often the starting point, not the final master.

- Trim silence at the head or tail if needed.
- Layer generated sounds with existing library material for more punch.
- Add EQ, transient shaping, or light saturation if the asset feels thin.
- Route the finished asset into the game's mixer/middleware structure with correct category, voice budget, and ducking behavior.

## Prompt Writing Guidelines

### Structure of a strong sound prompt
```text
<asset type>, <action>, <material/timbre>, <duration cue>, <mix/readability cue>, <negative cues>
```

**Example — combat SFX:**
> `"Short heavy axe impact, dense wood crack and dull metal hit, punchy one-shot, 1 second, foreground gameplay readable, no music, no voice"`

**Example — UI sound:**
> `"Short magical inventory confirm ping, bright glassy sparkle, soft tail under 1 second, clean interface feedback, no ambience, no voice"`

**Example — bark / vocal cue:**
> `"Short monster effort bark, aggressive but brief, gameplay readable, under 1 second, no words, no music, no crowd"`

### Key rules
- **One sound per prompt** — do not ask for a layered cinematic mix in one generation.
- **State the duration explicitly** — short game assets benefit from a hard time target.
- **Use gameplay language** — describe how readable or subtle the sound should be, not just what it is.
- **Include negatives** — add cues like `no music`, `no ambience`, `no narration`, or `no long tail` when appropriate.
- **Iterate deliberately** — if a result is close, adjust only one or two prompt details on the next pass.

## Error Handling

| Error | Likely cause | Recommended fix |
|-------|--------------|-----------------|
| `401` / authentication failure | `ELEVENLABS_API_KEY` missing, expired, or incorrect | Re-load the environment, confirm the variable exists, and retry without exposing the secret |
| `400` validation error | Unsupported request payload, invalid `duration_seconds`, or malformed JSON | Check field names, keep duration between `0.5` and `30`, and validate JSON before retrying |
| Empty or tiny output file | Request failed silently or output path was wrong | Inspect HTTP status, write to a known path, and verify file size after download |
| Output sounds noisy or off-brief | Prompt too vague or overloaded | Reduce the prompt to one clear sound idea and regenerate multiple controlled variants |
| `429` or intermittent server failures | Rate limit or temporary upstream issue | Back off, wait briefly, then retry with the same payload; avoid aggressive loops |
| Network timeout | Connectivity issue or long-running request | Increase timeout slightly and retry once; if it persists, surface the blocker clearly |

When automating batches, fail loudly on non-2xx responses and keep a manifest of generated files so partial runs are easy to resume.

## When to Use
- Prototyping short combat SFX quickly before a full sound-design pass.
- Producing UI feedback sounds that need to be coherent but fast to generate.
- Creating placeholder or stylized short barks for gameplay timing tests.
- Building several quick variations for repetition control in action-heavy loops.
- Supplying raw material for later editing, layering, and middleware integration.

## When Not to Use
- Final long-form dialogue, narrative VO, songs, or adaptive music systems.
- Assets that require exact actor performance direction, legal review, or localization pipeline support.
- Sounds that must exactly match an established copyrighted recording or brand-specific signature sound.
- Complex multi-layer cinematic assets better built through manual sound design and mastering.
- Tasks where the project already has approved bespoke recordings and only needs integration via `audio-middleware`.

## Integration with Game-Focused Agents

This skill is primarily intended for the `game-audio-engineer` agent when it needs a fast path to short game-ready source assets for prototypes, spikes, or temporary content.

It pairs well with the `audio-middleware` skill:
- `elevenlabs-audio-generation` creates or iterates source material
- `audio-middleware` defines how those assets should be named, mixed, budgeted, and triggered at runtime
