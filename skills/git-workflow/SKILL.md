---
name: git-workflow
description: "Branch, commit, push, and create pull requests using gh CLI. Ensures all agent changes go through PR review."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Git Workflow Skill

## Overview
All code changes made by agents **must** go through a branch + pull request workflow. Never commit directly to `main` or the default branch.

## Workflow

### 1. Create a Feature Branch

Before making any changes, create and switch to a new branch:

```bash
# Ensure you're on the latest default branch
git fetch origin
git checkout main && git pull origin main

# Create a descriptive branch
git checkout -b <type>/<short-slug>
```

**Branch naming convention:**
| Prefix | When to use |
|--------|-------------|
| `feat/<slug>` | New features |
| `fix/<slug>` | Bug fixes |
| `test/<slug>` | Adding/updating tests |
| `refactor/<slug>` | Refactoring |
| `docs/<slug>` | Documentation changes |
| `infra/<slug>` | Infrastructure/DevOps |

Use lowercase kebab-case for the slug. Keep it short but descriptive (e.g., `feat/add-user-auth`, `fix/cors-header-missing`).

### 2. Make Changes and Commit

Commit using **conventional commits** with the Co-authored-by trailer:

```bash
git add -A
git commit -m "<type>: <brief description>

<optional detailed explanation>

```

**Commit types:** `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `ci`

Multiple commits on the same branch are fine — they help tell the story of the change.

### 3. Push and Create a Pull Request

After all changes are committed:

```bash
# Push the branch
git push origin HEAD

# Create a pull request
gh pr create \
  --title "<type>: <brief description>" \
  --body "## Summary
<what was changed and why>

## Changes
- <change 1>
- <change 2>

## Testing
<how it was tested or verified>

```

**Important:**
- The PR is left **open** for manual review and merge by the user.
- Always report the PR URL back to the orchestrator/user.
- Use `--base main` if the default branch detection fails.

### 4. Return to Default Branch

After creating the PR, switch back so future work starts clean:

```bash
git checkout main
```

## Rules

1. **Never commit directly to `main`** — always use a feature branch.
2. **Never merge your own PRs** — leave them for the user to review and merge.
3. **Never force-push** — use regular `git push`. If there are conflicts, rebase and push.
4. **One logical change per PR** — don't bundle unrelated changes.
5. **Always include Co-authored-by** trailer in both commits and PR body.
6. **Report the PR URL** — always surface the PR link to the user at the end.

## When to Use
- Any time an agent creates, modifies, or deletes code files.
- Any time an agent modifies configuration, infrastructure, or documentation files.
- Essentially: if it touches files in the repo, it goes through a PR.
