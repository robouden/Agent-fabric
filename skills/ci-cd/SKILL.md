---
name: ci-cd
description: "Structured workflow for creating and managing CI/CD pipelines, GitHub Actions workflows, and deployment automation."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# CI/CD Skill

## Capabilities
- **GitHub Actions** — create and manage workflow files
- **Pipeline design** — build → test → lint → deploy stages
- **Environment management** — staging, production, preview
- **Secret management** — use GitHub Secrets for sensitive values
- **Matrix builds** — test across multiple versions/platforms

## Workflow

### Step 1 — Assess Requirements
Before writing any pipeline configuration:
- Identify the project's language, framework, and build tool
- Determine what needs to run: lint, test, build, deploy, release?
- Identify target environments (staging, production, preview)
- Check for existing CI configuration to extend rather than replace
- List secrets and credentials the pipeline will need

### Step 2 — Design the Pipeline
Structure stages to fail fast and minimize wasted compute:

```
┌─────────┐   ┌──────┐   ┌──────┐   ┌────────┐   ┌────────┐
│ Install  │→  │ Lint │→  │ Test │→  │ Build  │→  │ Deploy │
└─────────┘   └──────┘   └──────┘   └────────┘   └────────┘
```

- Run cheap checks first (lint, type-check) before expensive ones (test, build)
- Use parallelism where stages are independent
- Add matrix builds for multi-version/multi-platform support
- Define clear triggers: push, pull_request, release, schedule

### Step 3 — Implement Workflow Files
Create workflows in `.github/workflows/` with descriptive names:

| File | Purpose | Trigger |
|------|---------|---------|
| `ci.yml` | Main CI pipeline (lint + test + build) | push, pull_request |
| `deploy.yml` | Deployment to staging/production | push to main, workflow_dispatch |
| `release.yml` | Version tagging and release | release published |
| `pr-checks.yml` | PR-specific validation | pull_request |

Follow these rules when writing workflow files:
1. Pin action versions exactly (`actions/checkout@v4`, not `@latest`)
2. Cache dependencies (`actions/cache` for node_modules, pip, gradle, etc.)
3. Use GitHub Secrets for all credentials — never hardcode
4. Set timeouts on jobs to prevent runaway costs
5. Use `concurrency` groups to cancel superseded runs
6. Add `permissions` block to follow least-privilege principle

### Step 4 — Configure Environments
For deployment pipelines:
- Create GitHub Environments (staging, production) with protection rules
- Require manual approval for production deployments
- Set environment-specific secrets and variables
- Add deployment status notifications (Slack, email, etc.)

### Step 5 — Validate
After creating pipeline configuration:
- Push to a branch and verify the workflow triggers correctly
- Check that all stages pass on a clean run
- Verify caching is working (second run should be faster)
- Test failure scenarios (does a failing test block deployment?)
- Add status badges to the README

## Anti-Patterns to Avoid
- ❌ Using `@latest` or `@main` for action versions (supply chain risk)
- ❌ Storing secrets in workflow files or repository code
- ❌ Running expensive steps (full test suite, Docker build) before cheap checks (lint)
- ❌ Missing `concurrency` groups (wasted compute on superseded commits)
- ❌ No timeout on jobs (risk of infinite-running workflows)
- ❌ Duplicating logic across workflows instead of using reusable workflows
- ❌ Letting build and deploy define different container repository or tag formats — keep one canonical image name shared across all jobs
- ❌ Omitting `packages: read` on deploy jobs that must reference or validate private GHCR images
- ❌ Failing a deployment on rollout timeout without printing actionable diagnostics (pods, describe output, logs, recent events)

## Kubernetes Deployment Notes
- For Kubernetes deploys, define one canonical image repository/tag convention and reuse it in build, deploy, and manifests.
- If the deployment references private GHCR images, ensure the deploy job requests `permissions: packages: read`.
- Add debug-on-failure diagnostics to rollout steps so failed deploys surface pod state, describe output, logs, and events.
- Keep detailed Kubernetes manifest and cluster guidance in `skills/kubernetes/SKILL.md`; use this skill for pipeline structure and workflow design.

## When to Use
- Setting up automated testing for a repository
- Creating deployment pipelines
- Automating release processes
- Adding quality gates to pull requests
- Configuring matrix builds for cross-platform testing
