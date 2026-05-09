---
name: setup-infra
description: "Prompt for setting up CI/CD and deployment infrastructure"
version: 0.1.0
metadata:
  hermes:
    category: prompt
---
# Setup Infrastructure

## Context
Set up the infrastructure for: **{{PROJECT_OR_SERVICE}}**

## Requirements
{{INFRASTRUCTURE_REQUIREMENTS}}

## Workflow Note
- Use the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`) to select the role for each step.
- For end-to-end coordination, you can select the orchestrator first.
- Use Hermes parallel-skill execution when research, validation, or review work can safely run in parallel.
- Use `@` only for files and paths.

## Steps

### 1 — Research Infrastructure Options

**Role:** Researcher

Select the researcher agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Evaluate infrastructure options for **{{PROJECT_OR_SERVICE}}**:
>
> - Cloud provider comparison (AWS, GCP, Azure) for the use case
> - Container orchestration (Kubernetes, ECS, Cloud Run, plain Docker)
> - CI/CD platform (GitHub Actions, GitLab CI, CircleCI)
> - Infrastructure-as-code tool (Terraform, Pulumi, CloudFormation)
>
> Produce a recommendation with cost, complexity, and scalability trade-offs.

---

### 2 — Implement Infrastructure

**Role:** DevOps

Branch: `infra/{{PROJECT_OR_SERVICE}}-setup`

Select the devops agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Based on the research from Step 1, implement:
>
> 1. CI/CD pipeline (build, test, lint, deploy stages)
> 2. Dockerfile and docker-compose for local development
> 3. Infrastructure-as-code for cloud resources
> 4. Environment configuration (dev, staging, production)
> 5. Secrets management setup (GitHub Secrets, vault, or equivalent)
> 6. Health check endpoints and readiness probes

---

### 3 — Test Infrastructure

**Role:** Tester

Stay on the same branch.

Select the tester agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Validate the infrastructure setup:
>
> 1. CI pipeline runs successfully on a test commit
> 2. Docker build completes and container starts cleanly
> 3. Health check endpoints respond correctly
> 4. Environment variables are properly injected
> 5. Secrets are not exposed in logs or build output

---

### 4 — Document

**Role:** Documenter

Stay on the same branch.

Select the documenter agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Create infrastructure documentation:
>
> 1. Architecture diagram (Mermaid) showing services, networks, and data flow
> 2. Setup guide for local development environment
> 3. Deployment procedure for each environment
> 4. Troubleshooting guide for common infrastructure issues
> 5. Runbook for on-call engineers

---

### 5 — Review

**Role:** Code Reviewer

Select the code-reviewer agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Review infrastructure code on branch `infra/{{PROJECT_OR_SERVICE}}-setup`:
>
> - Security: no hardcoded secrets, least-privilege IAM, network isolation
> - Best practices: reproducible builds, minimal images, health checks
> - Cost: right-sized resources, auto-scaling configuration
> - Reliability: redundancy, backup strategy, disaster recovery

Push the branch and open a PR:

```bash
git push -u origin infra/{{PROJECT_OR_SERVICE}}-setup
gh pr create \
  --title "infra: setup for {{PROJECT_OR_SERVICE}}" \
  --body "## Summary\nCI/CD pipeline, containerization, and infrastructure-as-code."
```

Report the PR URL.
