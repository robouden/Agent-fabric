---
name: performance-optimization
description: "Profiling, caching strategies, query optimization, load testing, and performance benchmarking."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Performance Optimization Skill

## Capabilities
- **Application profiling** — CPU, memory, and I/O profiling with language-specific tools (pprof, async-profiler, cProfile, Chrome DevTools)
- **Caching strategies** — Redis, Memcached, CDN caching, browser cache headers, application-level caching
- **Database query optimization** — EXPLAIN analysis, index design, query rewriting, denormalization
- **Load testing** — design and run load tests with k6, JMeter, Artillery, or Locust
- **Benchmarking & baseline metrics** — establish performance baselines and track regressions
- **Bundle size optimization** — tree shaking, code splitting, dynamic imports, dead code elimination
- **Image & asset optimization** — compression, responsive images, lazy loading, format selection (WebP, AVIF)
- **Connection pooling & resource management** — database pools, HTTP keep-alive, gRPC channels

## Best Practices
1. **Measure before optimizing (profile first)** — never optimize based on assumptions; always profile to find the real bottleneck
2. **Set performance budgets** — define maximum acceptable values for page load time, bundle size, API response time
3. **Cache at the right layer** — CDN > application cache > database cache; cache as close to the user as possible
4. **Use lazy loading for non-critical resources** — defer loading of below-the-fold content and non-essential modules
5. **Optimize the critical rendering path** — minimize render-blocking CSS and JS for faster first paint
6. **Use connection pooling for databases** — reuse connections instead of creating new ones per request
7. **Minimize network round trips** — batch API calls, use HTTP/2 multiplexing, reduce redirect chains
8. **Compress responses** — enable gzip or brotli compression for text-based responses
9. **Use pagination for large datasets** — never load unbounded data into memory

## When to Use
- Diagnosing reported performance issues (slow pages, high latency, timeouts)
- Implementing caching for frequently accessed data
- Optimizing slow database queries identified in monitoring
- Setting up load tests before a launch or traffic spike
- Establishing performance baselines for a new service
- Reducing frontend bundle sizes for better Core Web Vitals

## Anti-Patterns to Avoid
- ❌ Premature optimization — optimizing code without evidence it is a bottleneck
- ❌ Caching without invalidation strategy — stale data is often worse than slow data
- ❌ N+1 queries — loading related data one item at a time in a loop
- ❌ Missing indexes — full table scans on frequently queried columns
- ❌ Synchronous operations that could be async — blocking threads on I/O operations
- ❌ Unbounded queries — `SELECT *` without LIMIT on large tables
