---
name: observability
description: "Logging, metrics, distributed tracing, alerting, and dashboard design for production systems."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Observability Skill

## Capabilities
- **Structured logging** — JSON logs with correlation IDs, log levels, and contextual metadata
- **Metrics collection** — instrument applications with Prometheus, StatsD, CloudWatch, or Datadog metrics
- **Distributed tracing** — implement tracing with OpenTelemetry, Jaeger, or Zipkin across service boundaries
- **Alerting rules & escalation** — define meaningful alerts with clear thresholds, routing, and escalation paths
- **Dashboard design** — create actionable dashboards in Grafana, Datadog, or CloudWatch
- **Health check endpoints** — implement liveness, readiness, and startup probes
- **SLI/SLO/SLA definition** — define service level indicators, objectives, and agreements
- **Error tracking** — integrate with Sentry, Rollbar, or Bugsnag for error aggregation and alerting

## Best Practices
1. **Use structured logging (JSON) with correlation IDs** — every log entry should be machine-parseable and traceable
2. **Follow the three pillars** — logs, metrics, and traces together give full observability
3. **Define SLIs/SLOs before building** — know what "healthy" looks like before you deploy
4. **Alert on symptoms, not causes** — alert on user-facing impact (error rate, latency), not internal signals
5. **Use log levels consistently** — DEBUG for development, INFO for normal operations, WARN for recoverable issues, ERROR for failures
6. **Include context in logs** — user ID, request ID, operation name, and relevant parameters
7. **Instrument critical paths first** — prioritize observability for revenue-impacting and user-facing flows
8. **Keep dashboards actionable** — every dashboard panel should answer a specific question, not just look nice

## When to Use
- Setting up production monitoring for a new service
- Debugging production issues across distributed systems
- Defining SLAs for external-facing services
- Designing alerting strategies that minimize noise
- Implementing distributed tracing across microservices
- Creating dashboards for operations teams

## Anti-Patterns to Avoid
- ❌ Logging sensitive data (PII, credentials, tokens) — redact or mask all sensitive fields
- ❌ Alert fatigue (too many alerts) — every alert must be actionable; if you ignore it, delete it
- ❌ Unstructured log messages — free-text logs are nearly impossible to query at scale
- ❌ Missing correlation IDs in distributed systems — without them, tracing requests across services is impossible
- ❌ Monitoring everything equally — prioritize by business impact, not infrastructure count
