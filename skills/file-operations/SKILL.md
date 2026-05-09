---
name: file-operations
description: "Read, write, create, search, and navigate files in the workspace."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# File Operations Skill

## Capabilities
- **Read files** — read file contents by path and line range
- **Write files** — create new files or edit existing files
- **Search files** — find files by name/glob pattern
- **Search content** — grep for text or regex patterns across the workspace
- **List directories** — explore directory structure

## Workflow

### Step 1 — Locate
Before touching any file, find it first:
- Use glob patterns to find files by name (`**/*.ts`, `src/**/index.*`)
- Use grep to find files by content (`function authenticate`, `TODO:`)
- List directories to understand project layout before guessing paths

### Step 2 — Read Before Writing
Always read a file before editing:
- Understand the existing content, structure, and style
- Identify the exact lines to change
- Check for related code that may need coordinated updates

### Step 3 — Edit Precisely
Make targeted, surgical changes:
- Edit specific lines — do not rewrite entire files
- Preserve existing formatting, indentation, and whitespace conventions
- When creating new files, match the style of neighboring files

### Step 4 — Verify
After editing, confirm the change is correct:
- Re-read the modified file to verify the edit landed correctly
- Check that the file still parses (run the build/linter if applicable)
- Verify no unintended side effects in related files

## Anti-Patterns to Avoid
- ❌ Editing a file without reading it first
- ❌ Guessing file paths instead of searching
- ❌ Rewriting an entire file when only a few lines need to change
- ❌ Using absolute paths in source code (use relative paths)
- ❌ Creating duplicate files instead of finding the existing one

## When to Use
- Any task that involves reading or modifying source code, config, or documentation files
- Exploring the project structure to understand the codebase
- Finding references to functions, classes, or variables
