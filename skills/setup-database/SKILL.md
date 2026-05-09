---
name: setup-database
description: "Prompt for setting up database infrastructure, schema design, and data access layer"
version: 0.1.0
metadata:
  hermes:
    category: prompt
---
# Setup Database

## Context
Set up database infrastructure for: **{{PROJECT_OR_SERVICE}}**

## Requirements
{{DATABASE_REQUIREMENTS}}

## Workflow Note
- Use the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`) to select the role for each step.
- For end-to-end coordination, you can select the orchestrator first.
- Use Hermes parallel-skill execution when research or review work can safely happen in parallel.
- Use `@` only for files and paths.

## Steps

### 1 — Research Database Options

**Role:** Researcher

Select the researcher agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Evaluate database options (PostgreSQL, MySQL, MongoDB, etc.) for **{{PROJECT_OR_SERVICE}}**.
>
> Consider:
> - Data model complexity (relational vs document vs key-value)
> - Scalability requirements (read/write ratio, expected volume)
> - Query patterns (OLTP vs OLAP, full-text search needs)
> - Operational maturity (hosting options, backup tooling, community)
> - Team familiarity and ecosystem fit
>
> Produce a recommendation with trade-offs and justification.

---

### 2 — Design Schema & Migrations

**Role:** Code Writer

Branch: `feat/{{PROJECT_OR_SERVICE}}-database`

Select the code-writer agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Using the `database-operations` skill and the research from Step 1:
>
> 1. Design the data model — tables/collections, relationships, indexes, constraints
> 2. Create migration files (up and down) using the project's migration tool
> 3. Define seed data for development/testing
> 4. Document any denormalization decisions with justification
> 5. Run migrations locally to verify they apply cleanly

---

### 3 — Implement Data Access Layer

**Role:** Code Writer

Stay on the same branch.

Select the code-writer agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Implement the repository/DAO pattern for the data model from Step 2:
>
> 1. Set up connection pooling and configuration (env-based)
> 2. Implement repository interfaces and concrete implementations
> 3. Add query builders or ORM mappings as appropriate
> 4. Implement transaction support for multi-step operations
> 5. Add connection health checks and graceful shutdown
> 6. Ensure all queries use parameterized statements (no string concatenation)

---

### 4 — Test

**Role:** Tester

Stay on the same branch.

Select the tester agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Write database integration tests:
>
> 1. Migration tests — verify up and down migrations are reversible
> 2. Repository tests — CRUD operations, edge cases, constraint violations
> 3. Use Testcontainers (or equivalent) for real database instances in tests
> 4. Test connection pooling behavior under concurrent access
> 5. Verify seed data loads correctly

---

### 5 — Review

**Role:** Code Reviewer

Select the code-reviewer agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Review the database implementation on branch `feat/{{PROJECT_OR_SERVICE}}-database`:
>
> - Schema design: normalization, indexing strategy, constraint coverage
> - Query patterns: parameterized queries, no SQL injection vectors
> - Migration safety: reversible, no data loss, idempotent where possible
> - Connection management: pooling config, timeout handling, leak prevention
> - Security: credential storage, access control, data encryption at rest

---

### 6 — Document

**Role:** Documenter

Stay on the same branch.

Select the documenter agent with the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`), or have the orchestrator assign this step:

> Create data model documentation:
>
> 1. ER diagram (Mermaid) showing all entities and relationships
> 2. Table/collection reference with column types and constraints
> 3. Migration runbook (how to apply, rollback, and troubleshoot)
> 4. Connection configuration guide (environment variables, pooling settings)
> 5. Query patterns and examples for common operations

Push the branch and open a PR:

```bash
git push -u origin feat/{{PROJECT_OR_SERVICE}}-database
gh pr create \
  --title "feat: database setup for {{PROJECT_OR_SERVICE}}" \
  --body "## Summary\nDatabase infrastructure, schema, data access layer, and tests."
```

Report the PR URL.
