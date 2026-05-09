---
name: png-optimization
description: "Lossless and lossy PNG compression for game assets in PixiJS/web projects using pngquant and optipng."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# PNG Optimization Skill

Covers auditing, lossy quantization, lossless re-compression, and batch pipelines for PNG game assets. Optimized for PixiJS/web projects where assets live under `public/images/`.

## Capabilities
- **Audit PNG files** — find all PNGs, report sizes, and identify the largest files before touching anything
- **Lossy compression with `pngquant`** — reduce to ≤256 colors, typical savings 40–70%
- **Lossless compression with `optipng`** — strip metadata and re-compress deflate stream, zero quality loss
- **Batch pipeline** — single shell command that processes every PNG under a directory tree
- **Tool installation** — install `pngquant` and `optipng` on macOS (Homebrew) and Linux (apt)
- **Asset-type routing** — decide which tool fits each asset category

## Tool Installation

### macOS (Homebrew)
```sh
brew install pngquant optipng
```

### Linux (apt)
```sh
sudo apt-get update && sudo apt-get install -y pngquant optipng
```

## Step 1 — Audit: Find and Size All PNGs

```sh
# List all PNGs sorted by size (largest first)
find public/images -name "*.png" -type f \
  | xargs du -k \
  | sort -rn \
  | awk '{printf "%6s KB  %s\n", $1, $2}'
```

Record the total before size:
```sh
du -sh public/images
```

## Step 2 — Lossy Optimization with pngquant

Reduces the palette to ≤256 colors. Quality range `65-80` balances file size and visual fidelity for game art.

```sh
# Overwrite each PNG in-place (--force --ext .png overwrites the original)
find public/images -name "*.png" -type f \
  | xargs -P4 pngquant --quality 65-80 --speed 1 --force --ext .png --
```

- `--quality 65-80` — skip files where the output would fall below 65% quality
- `--speed 1` — slowest/best compression (use `--speed 3` for faster CI runs)
- `-P4` — run 4 parallel jobs; adjust to CPU count

## Step 3 — Lossless Re-compression with optipng

Run after pngquant (or alone for lossless-only assets). Strips metadata and finds the best deflate strategy.

```sh
find public/images -name "*.png" -type f \
  | xargs -P4 optipng -o4 -strip all --
```

- `-o4` — optimization level 4 (good balance of speed vs savings; `-o7` is maximum)
- `-strip all` — remove EXIF, comments, and color-profile metadata

## Batch Pipeline (Lossy → Lossless)

One-liner for a full pass over `public/images/`:

```sh
IMAGES_DIR="public/images"

echo "=== Before ===" && du -sh "$IMAGES_DIR"

find "$IMAGES_DIR" -name "*.png" -type f \
  | xargs -P4 pngquant --quality 65-80 --speed 1 --force --ext .png --

find "$IMAGES_DIR" -name "*.png" -type f \
  | xargs -P4 optipng -o4 -strip all --

echo "=== After ===" && du -sh "$IMAGES_DIR"
```

For shell scripts, save as `scripts/optimize-png.sh` and make it executable:
```sh
chmod +x scripts/optimize-png.sh
./scripts/optimize-png.sh
```

## When to Use Which Tool

| Asset Type | Tool | Quality / Flags | Typical Savings |
|---|---|---|---|
| Boss portraits, card art, backgrounds | `pngquant` then `optipng` | `--quality 65-80` | 40–70% |
| UI icons, pixel-exact logos, crisp sprites | `optipng` only | `-o4 -strip all` | 5–20% |
| Thumbnails, low-priority decorative art | `pngquant` then `optipng` | `--quality 50-70` | 50–75% |
| Source-of-truth / master assets | Neither — keep originals lossless | — | — |

## Safety Rules

1. **Always work in a git branch** — run `git checkout -b chore/compress-assets` before any lossy operation so every original is recoverable via `git checkout`.
2. **Or take a backup first** — `cp -r public/images public/images.bak` before the first run.
3. **Audit before touching anything** — run Step 1 to understand the scope and note the before size.
4. **Never run pngquant on master/source art** — keep layered originals or pre-quantization exports in a separate `source-assets/` directory outside the web root.
5. **Verify a sample after compression** — spot-check 3–5 of the largest files visually before committing the full batch.

## Anti-Patterns to Avoid
- ❌ Running pngquant directly on `main` without a backup or git branch
- ❌ Using `--quality 0-100` (equivalent to no quality control; always set a floor)
- ❌ Re-running pngquant on already-quantized PNGs (quality degrades each pass; it is idempotent only with the same palette)
- ❌ Omitting `-strip all` — metadata can add 5–20 KB per file
- ❌ Skipping optipng — pngquant reduces palette but does not re-compress the deflate stream

## When to Use
- Before shipping a PixiJS game to reduce initial load time and CDN transfer costs
- As a pre-commit or CI step in the asset pipeline to enforce a size budget
- After an artist drops new PNGs into `public/images/` without running through any optimization
- When Lighthouse or WebPageTest flags unoptimized images as a performance issue
