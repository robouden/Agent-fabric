---
name: database-operations
description: "Schema design, migrations, seeding, query optimization, and backup/recovery strategies."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Database Operations Skill

## Capabilities
- **Schema design & modeling** — design normalized/denormalized schemas, define relationships, choose appropriate data types
- **Migration management** — create and manage schema migrations using Flyway, Alembic, Prisma Migrate, Knex, TypeORM, or framework-native tools
- **Data seeding & fixtures** — generate seed data for development, staging, and testing environments
- **Query optimization & indexing** — analyze slow queries with EXPLAIN, add appropriate indexes, rewrite inefficient queries
- **Backup & recovery** — plan and implement backup strategies, test restore procedures, point-in-time recovery
- **Connection pooling** — configure and optimize connection pools (HikariCP, PgBouncer, etc.)

## Best Practices
1. **Always use migrations** — never apply manual DDL changes; every schema change must be a versioned migration
2. **Version control all schema changes** — migrations live in the repository alongside application code
3. **Use parameterized queries** — prevent SQL injection by never interpolating user input into queries
4. **Index foreign keys and frequently queried columns** — missing indexes on foreign keys are a common performance killer
5. **Use connection pooling** — never open a new connection per request; configure pool size based on workload
6. **Test migrations both up and down** — every migration must have a working rollback
7. **Seed development data separately from production** — never mix test fixtures with production seed scripts
8. **Prefer ORM for CRUD but raw SQL for complex queries** — use the right tool for the job

## When to Use
- Setting up a new database for a project
- Adding or modifying tables, columns, or constraints
- Optimizing slow queries identified in monitoring or profiling
- Creating test fixtures and seed data
- Designing data models for new features
- Planning backup and disaster recovery strategies

## Anti-Patterns to Avoid
- ❌ Manual schema changes without migrations
- ❌ Storing database credentials in source code
- ❌ N+1 query patterns (loading related data one row at a time)
- ❌ Missing indexes on foreign keys
- ❌ No rollback strategy for migrations
- ❌ Using `SELECT *` in production queries
- ❌ Ignoring query execution plans when debugging performance
