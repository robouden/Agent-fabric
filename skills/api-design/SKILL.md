---
name: api-design
description: "REST and GraphQL API design patterns, OpenAPI specifications, versioning, and backwards compatibility."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# API Design Skill

## Capabilities
- **REST API design** — resource naming, HTTP methods, status codes, content negotiation
- **GraphQL schema design** — types, queries, mutations, subscriptions, and resolver patterns
- **OpenAPI/Swagger specification generation** — document APIs with OpenAPI 3.x for client generation and documentation
- **API versioning strategies** — URL-based (`/v1/`), header-based (`Accept-Version`), content-type-based versioning
- **Backwards compatibility analysis** — detect breaking changes before deployment
- **Rate limiting & throttling patterns** — token bucket, sliding window, and per-client limits
- **Pagination patterns** — cursor-based, offset-based, and keyset pagination
- **Error response standardization** — consistent error format across all endpoints

## Best Practices
1. **Use nouns for resources (plural)** — `/users`, `/orders`, `/products` (not `/getUsers`)
2. **Consistent error format** — follow RFC 7807 Problem Details for HTTP APIs
3. **Version APIs from day one** — even if you only have v1, the versioning structure should be in place
4. **Document with OpenAPI 3.x** — auto-generate docs, client SDKs, and validation from the spec
5. **Use HATEOAS where appropriate** — include links to related resources in responses
6. **Paginate all list endpoints** — never return unbounded collections
7. **Validate all inputs** — reject invalid data at the API boundary, not deep in business logic
8. **Use proper HTTP status codes** — don't return 200 for errors; use 4xx for client errors, 5xx for server errors
9. **Idempotent PUT/DELETE operations** — repeated calls should produce the same result

## When to Use
- Designing new REST or GraphQL APIs
- Reviewing API contracts for consistency and completeness
- Generating API documentation from code or spec
- Planning API versioning strategy for a growing service
- Ensuring backwards compatibility before releasing a new version
- Standardizing error responses across microservices

## Anti-Patterns to Avoid
- ❌ Verbs in URLs (`/getUser`, `/createOrder`) — use HTTP methods instead
- ❌ Inconsistent naming conventions (mixing camelCase and snake_case)
- ❌ Breaking changes without versioning
- ❌ Missing pagination on list endpoints
- ❌ Generic error messages without actionable detail
- ❌ Exposing internal database IDs without mapping to public identifiers
- ❌ Returning 200 OK with an error payload
