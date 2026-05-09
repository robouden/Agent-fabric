---
name: review-code
description: "Prompt for reviewing code quality, security, architecture, and performance"
version: 0.1.0
metadata:
  hermes:
    category: prompt
---
# Review Code

## Context
Please review the following code for quality, security, and best practices.

## Files to Review
{{FILES_OR_PATHS}}

## Focus Areas
- [ ] Correctness
- [ ] Security vulnerabilities
- [ ] Performance
- [ ] Code style and readability
- [ ] Test coverage
- [ ] Error handling
- [ ] Architecture and design patterns
- [ ] API contracts (backwards compatibility, status codes, validation)
- [ ] Database queries (indexing, N+1, migration safety)
- [ ] Accessibility (semantic HTML, ARIA, keyboard navigation)

## Workflow Note
- Use the relevant skill (loaded via `-s <skill>` or invoked as `/<skill>`) to select the reviewer or tester roles.
- Use `@` for file/path mentions included in `{{FILES_OR_PATHS}}`.
- Use Hermes parallel-skill execution if you want review and test-gap analysis to run in parallel.

## Steps
1. **Code Reviewer** — perform a thorough code review covering correctness, security, readability, and structural improvements. Include the extended review areas (API contract, database, accessibility) where applicable.
2. **Code Reviewer** — perform an architecture review: component boundaries, dependency direction, separation of concerns, and adherence to project patterns.
3. **Code Reviewer** — perform a performance review: identify N+1 queries, missing caching opportunities, unnecessary re-renders, memory leaks, and unbounded data fetches.
4. **Tester** — check whether test coverage is adequate and flag untested critical paths and edge cases.
