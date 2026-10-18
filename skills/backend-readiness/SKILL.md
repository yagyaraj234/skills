---
name: backend-readiness
description: Check backend readiness from a working diff, PR URL, release target, or full repository audit. Use for API, worker, webhook, database, and service reviews.
---

# Backend readiness

Invoke as `$backend-readiness diff`, `$backend-readiness pr <URL>`, `$backend-readiness release`, or `$backend-readiness full`.

Use the mode, evidence, verdict, and edit rules in [check-readiness](../check-readiness/SKILL.md). When invoked directly, select a mode there. When invoked by `check-readiness`, keep its selected target. Assess backend surfaces only.

Assess checks that apply to the chosen target. Mark an in-scope exception `N/A` with a reason. A section whose subject is absent (no payments, no OpenAPI, no database) is one `N/A` line, not one per check.

Blocking areas: input validation, authentication and authorization, payment and webhook verification and idempotency, data integrity and migrations, secrets and unsafe production defaults, failing build or tests. Other failures are advisory.

### API contracts and validation

- [ ] Path, query, header, cookie, body, file, webhook, and environment input validates type, format, range, size, content type, and business meaning at its trust boundary.
- [ ] Unknown fields, malformed payloads, oversized requests, and invalid state transitions fail with stable status codes and safe error bodies.
- [ ] Responses expose only intended fields and use consistent pagination, timestamps, identifiers, and error envelopes.
- [ ] If the service publishes an OpenAPI document: it matches runtime routes, schemas, auth, errors, pagination, rate limits, webhooks, and idempotency behavior; CI validates the document.
- [ ] If an OpenAPI document exists, Scalar or the installed renderer serves usable reference docs, and protected or internal operations have intentional visibility.

### Authentication and authorization

- [ ] Every non-public operation authenticates the caller and authorizes function, object, tenant, and property access server-side.
- [ ] Protected routes default to denial; public, admin, health, metrics, and documentation routes are explicitly classified.
- [ ] Role, ownership, cross-tenant, expired-token, revoked-session, and privilege-escalation cases have negative tests.
- [ ] Credentials, sessions, tokens, cookies, and service identities use secure transport, expiry, rotation, revocation, and least privilege appropriate to their risk.
- [ ] Rate limits cover caller, IP, tenant, route, and costly business actions where relevant; distributed deployments share limiter state; responses use `429` with retry information; tests prove burst, sustained, concurrent, and bypass behavior.

### Payments and webhooks

Applies only if the target handles payments or inbound webhooks.

- [ ] Payment state changes from verified provider webhooks or provider API state, never from client redirects alone.
- [ ] Webhook signature verification uses the untouched raw body, endpoint secret, provider library, and timestamp tolerance before parsing or side effects.
- [ ] Handler accepts only required event types, records provider event and object identifiers, and processes duplicates and out-of-order events safely.
- [ ] Payment mutation and event receipt commit atomically behind a unique database constraint or equivalent idempotency boundary.
- [ ] Webhook endpoint queues slow work, returns `2xx` promptly after durable acceptance, and routes transient processing failures through bounded retry or dead-letter handling; reconciliation recovers missed events.

### Data and persistence

- [ ] Schema, constraints, indexes, grants, and triggers live in reviewed, versioned migrations where the data store supports them.
- [ ] When using PostgreSQL RLS, policies for protected tables cover applicable roles and commands with `USING` and `WITH CHECK`; owner and bypass behavior is deliberate.
- [ ] When using PostgreSQL RLS, tests cover applicable roles and `SELECT`, `INSERT`, `UPDATE`, and `DELETE` policies, including cross-tenant access.
- [ ] Transactions protect multi-write invariants; database constraints remain the final guard against races and duplicates.
- [ ] Connection pools have bounded sizes derived from database capacity and instance count, acquisition/idle/statement timeouts, guaranteed release, and pool metrics.
- [ ] Migrations have rollout and rollback or forward-fix plans, and backup/restore behavior is verified for destructive changes.

### Reliability and lifecycle

- [ ] Mutating operations with retry or duplicate risk use caller-scoped idempotency keys, normalized request matching, atomic result storage, retention, and concurrency tests.
- [ ] Retries target transient failures only and use timeouts, bounded attempts, exponential backoff, jitter, retry budgets, and provider retry guidance.
- [ ] Permanent failures reach a visible terminal state or dead-letter path.
- [ ] Health endpoints separate startup, liveness, and readiness; checks are cheap, timeout-bounded, and reveal no sensitive internals; readiness reflects ability to serve traffic, and liveness does not turn dependency incidents into restart loops.
- [ ] Graceful shutdown stops new traffic and work, fails readiness, drains in-flight requests and consumers, closes pools and servers, flushes telemetry, then exits within deployment grace time.
- [ ] Shutdown behavior is tested with active requests, jobs, transactions, WebSocket connections where present, and forced timeout.

### Configuration

- [ ] Startup validates required environment variables, types, ranges, URLs, mutually dependent values, and environment-specific invariants before accepting traffic.
- [ ] Production fails closed on missing secrets or unsafe defaults; logs and errors expose variable names without secret values.
- [ ] Example environment files contain names and safe placeholders only; secret storage, rotation, and access are owned outside source control.

### Observability

- [ ] Logs are structured and include timestamp, severity, service, environment, version, operation, outcome, duration, and safe domain identifiers.
- [ ] Secrets, credentials, payment data, and unnecessary personal data are redacted before logging.
- [ ] A validated or generated request ID propagates through responses, downstream calls, queues, jobs, and webhooks; trace and span IDs correlate logs.
- [ ] Metrics cover request rate, errors, duration, saturation, pool use, queue depth, retries, rate limits, webhook failures, and idempotency conflicts with bounded labels.
- [ ] Traces cover inbound requests, database calls, external services, queues, and background work with context propagation.
- [ ] Dashboards and actionable alerts link metrics, traces, and logs to service objectives; alert tests prove routing and recovery.
- [ ] Security and business audit events are durable, access-controlled, and distinct from diagnostic logs.

### Testing and release

- [ ] Formatter, lint, typecheck, unit tests, integration tests, migration checks, OpenAPI validation, and production build each pass or have an explicit baseline failure.

For each applicable behavior—auth boundaries, request validation, payments, webhook duplicates, idempotency, retries, RLS, health, and shutdown—record a separate end-to-end test result against production-like dependencies.

- [ ] Contract tests cover downstream providers and consumers; failure tests cover timeout, partial outage, malformed data, and dependency recovery.
- [ ] Deployment smoke tests verify routes, migrations, health, metrics, logs, traces, alerts, and rollback or forward-fix path in the target environment.

### SOLID without ceremony

- [ ] **Single Responsibility:** validation, authorization, business rules, persistence, transport, and orchestration have cohesive ownership and clear change reasons.
- [ ] **Open/Closed:** real variants extend an existing seam without rewriting stable policy; speculative variants create no framework.
- [ ] **Liskov Substitution:** implementations preserve input, output, error, transaction, and side-effect contracts.
- [ ] **Interface Segregation:** consumers depend on the smallest capability they use, with contracts split only when consumers differ.
- [ ] **Dependency Inversion:** domain policy depends on injected capabilities; framework, database, queue, and provider adapters depend inward.

