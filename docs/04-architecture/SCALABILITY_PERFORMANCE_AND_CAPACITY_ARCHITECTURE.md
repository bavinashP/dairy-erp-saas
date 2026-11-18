# Scalability, Performance and Capacity Architecture

**Document ID:** ARC-SCALE-001  
**Status:** Technology-neutral engineering baseline — numeric workload envelope to be calibrated from pilot evidence before production sizing  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, NFR-001, ARC-TEN-001, ARC-IAM-001, ARC-EVT-001, ARC-CONC-001, ARC-OBS-001, ARC-DR-001, ODR-027/030/031.  
**Purpose:** Define how the platform scales across tenants, branches, users, recurring dairy operations, transactional workloads, files, reports and analytics without reducing product scope to fit development hardware.

## 1. Decisions and baseline

The following are selected as the default engineering direction:
1. Start with a **modular monolith** organized by business capability, backed by a documented API contract. Do not begin with distributed microservices; extract a module only when measured scaling, isolation, deployment or team-ownership evidence justifies the operational cost.
2. Keep request handling stateless wherever practical. Persist session, workflow, job and business state in shared durable services, not process memory.
3. Use a transactional relational system as the authoritative store for financial and operational records. The specific database engine remains part of the technology ADR, but domain integrity must not depend on a particular ORM.
4. Separate synchronous user interactions from long-running work. Reports, PDF generation, bulk imports/exports, notifications, recurring schedule generation, forecasting and external synchronization use durable background processing.
5. Scale API/request workers and background workers independently. Do not scale only by CPU; monitor queue age, database saturation, memory, storage I/O and provider latency.
6. Treat tenant isolation, branch authorization, concurrency control, audit and idempotency as non-negotiable at every scale.
7. Use database partitioning, read replicas, distributed caches, search clusters and microservices only when measured evidence shows the simpler baseline is insufficient.
8. The current development VPS constraints MUST NOT cap production design, feature scope, load targets or architecture. Development tooling should be resource-efficient; production capacity is determined from workload and service objectives.

This baseline avoids premature distributed-system complexity while preserving explicit extraction and horizontal-scaling paths.

## 2. Workload model

Capacity planning must model the workload rather than a single tenant/user count. Record at least:

### 2.1 Tenant and organizational dimensions
- Active paying/trial tenants by plan and business profile.
- Branches/outlets/collection points/warehouses per tenant.
- Active users, concurrent users, role mix and shift overlap.
- POS terminals/devices, delivery operators and shared operational devices.
- Route templates/manual routes, DeliveryPlans and concurrent DeliveryRuns per Branch/date/shift.
- Delivery stops, attempts, fulfilment events, assigned participants, vehicles/transport resources and exclusive resource-booking windows.
- Customer/subscription count and number of active subscription schedules.
- Suppliers/farmers, products/SKUs, batches/lots and stock locations.

### 2.2 Transaction dimensions
- POS transactions per branch per peak hour.
- Subscription occurrence generation per business day and cut-off.
- Delivery stops, fulfilment updates and exception events per route/shift.
- Milk collection entries, quality tests, procurement settlements and stock movements.
- Invoice/receipt/payment/journal entries and reconciliation volume.
- API requests per second by operation class, payload size and read/write ratio.
- Dispatch events, route/stop updates, offline synchronization commands, geocoding/map requests, route-optimization jobs and telematics/device events by Tenant.
- Idempotency retries, webhook bursts and integration polling frequency.

### 2.3 Data and artifact dimensions
- Database row growth by domain and tenant.
- Average and maximum attachment/PDF size, object count and download frequency.
- Audit/event/outbox growth and retention classes, including run/assignment history and dispatch snapshots.
- Custody event frequency, raw sensor/device reading rate, evidence attachment volume, object-storage retrieval/egress and device connectivity patterns by Tenant/workflow.
- Report/forecast dataset size, execution frequency and concurrency.
- Backup volume, change rate, WAL/binlog generation and restore throughput.
- Analytics refresh and historical query workload.

### 2.4 Time and seasonality
- Morning and evening delivery/POS peaks.
- Month-end billing, statements, collections and payment reconciliation.
- Weekly procurement and settlement patterns.
- Festival, seasonal milk demand and promotion spikes.
- Delivery-window concentration (morning/evening), weather/road disruption, school/office holiday effects and peak route replanning demand.
- Payroll/accounting close and tax/reporting periods.
- Tenant onboarding, bulk imports and data migrations.

Do not use daily averages alone. Capacity must be tested against overlapping peak windows, month-end bursts and recovery/rebuild workloads.

## 3. Workload scenarios

Maintain three workload envelopes, refreshed as pilot/production telemetry becomes available:

| Scenario | Meaning | Planning use |
|---|---|---|
| Conservative | Expected early adoption and normal daily operation | Initial deployment and minimum viable capacity |
| Expected | Approved business growth forecast plus peak-hour concurrency | Standard production sizing and cost planning |
| Stress | Short-duration burst, month-end overlap, noisy tenant, retry storm, provider slowdown and recovery rebuild | Headroom, isolation, overload behavior and disaster-recovery validation |

Each envelope must state tenants, branches, users, active subscriptions, peak POS/delivery transactions, API concurrency, background queue arrival rate, data growth, object growth, report concurrency and retention. Numbers must come from product-owner target market assumptions, pilot observations or explicitly labelled estimates—not invented precision.

The platform must be tested at expected peak and at a documented stress multiplier above it. The multiplier is selected during capacity test planning based on risk and cost; record it in the release evidence.

## 4. Logical scaling architecture

### 4.1 API and web application
- Keep API instances stateless and horizontally replicable.
- Externalize sessions, rate-limit counters, job state and other shared coordination state.
- Apply bounded request body sizes, pagination, query timeouts, per-tenant rate limits and concurrency budgets.
- Separate interactive latency-sensitive endpoints from bulk/report endpoints.
- Use connection pooling with explicit limits; do not let each application worker create unbounded database connections.
- Serve static assets and generated files through suitable CDN/object delivery paths where justified; authorization remains server-side.
- Keep API version compatibility for deployed mobile clients; client upgrade cadence must not force synchronized backend releases.

### 4.2 Transactional data
- Keep a single authoritative write path for each aggregate/business invariant.
- Use indexes based on measured query plans and tenant/branch access patterns.
- Ensure every tenant-scoped query includes validated tenant scope; branch filters are independently enforced.
- Use bounded transactions and avoid holding locks during external network calls.
- Apply optimistic concurrency/version checks or explicit locks according to ARC-CONC-001.
- Maintain archival/partitioning strategy only after row growth, query performance and retention evidence justify it.
- Read replicas may serve suitable stale-tolerant reports; never route a read requiring read-after-write correctness to a lagging replica without a consistency strategy.
- Database sharding is a later-stage option, not the initial default. Any future shard key must preserve tenant isolation, transactional boundaries and cross-tenant platform administration.

### 4.3 Background processing
- Scale worker pools independently by queue class and resource profile.
- Separate critical business processing from best-effort notifications and analytics.
- Run route optimization, geocoding, map enrichment and bulk fleet/telematics reconciliation asynchronously in bounded tenant-aware queues; a failed or saturated optimization provider must not block manual dispatch.
- Use per-tenant fairness/concurrency limits to prevent noisy-neighbour starvation.
- Apply idempotency, bounded retries, leases, dead-letter/manual resolution and queue-age monitoring.
- Long jobs must checkpoint progress and resume safely.
- Apply backpressure when downstream providers, database or object storage are saturated.
- Separate high-volume sensor/telemetry ingestion and evidence processing from critical stock/financial posting queues; preserve event ordering/idempotency and expose lag/failed-reading status.
- Bound device frequency, batch size, attachment size and per-Tenant evidence storage/egress; do not drop safety/traceability evidence silently when a quota is reached.

### 4.4 Object storage and file delivery
- Keep binary objects out of relational database rows except small metadata where justified.
- Use provider-neutral object storage and immutable versioned object keys.
- Upload/download directly through short-lived scoped URLs where safe, avoiding unnecessary VPS bandwidth/CPU.
- Meter storage, request, retrieval and egress usage by tenant, including custody evidence, sensor payloads and media attachments.
- Keep evidence metadata/provenance searchable while large binary artifacts use object storage and governed lifecycle tiers; retrieval must remain permission-checked.
- Use asynchronous PDF/report generation with bounded worker resources.
- Follow ARC-STO-001 and ARC-DR-001 for lifecycle and recovery.

### 4.5 Analytics and reporting
- Keep transactional writes independent from expensive analytical scans.
- Use pre-aggregations/materialized summaries for stable, frequently used dashboards when measured query cost warrants them.
- Run heavy reports and forecasts asynchronously with a durable job/result record.
- Maintain data freshness metadata and source cut-off timestamps.
- Introduce a separate analytical store only when workload isolation, history volume or query complexity justifies replication and operational overhead.
- Enforce tenant/branch scope in the analytical model; aggregation is not permission to expose cross-tenant information.

## 5. Tenant fairness and noisy-neighbour controls

- Apply per-tenant API request budgets by operation class.
- Bound simultaneous expensive reports, exports, imports, forecast runs and integration jobs.
- Apply tenant-aware queue scheduling/fairness so a large tenant cannot monopolize all workers.
- Define plan-based limits for business entitlements separately from platform-wide safety ceilings.
- Meter database-heavy operations and reject or queue pathological filters/exports.
- Bound file size, batch size, pagination size, webhook payload and retry rate.
- Bound route stops per optimization request, route/plan complexity, provider map/geocoding requests and per-Tenant concurrent optimization runs; enforce external provider rate limits and safe fallback to manual route ordering.
- Use per-tenant storage quotas and warnings; critical business/audit artifacts must not be silently discarded at quota exhaustion.
- Detect unusual growth, retry loops and automated abuse; provide safe throttling and operator visibility.
- Platform operators may impose temporary protective limits during incidents, with audit and customer communication.

## 6. Performance budgets and user experience

Numeric targets are set per workflow after measuring pilot usage and agreeing business impact. Define percentile targets (not averages) for:
- Authentication and session establishment.
- POS product lookup, checkout and receipt confirmation.
- Customer/subscription lookup and schedule changes.
- Delivery run/stop retrieval and fulfilment update.
- Procurement/collection entry and quality capture.
- Stock availability and movement posting.
- Payment receipt and account balance views.
- Common dashboard/report opening.
- Search/autocomplete and export initiation.

Performance requirements:
- Interactive actions should return a durable accepted/completed status quickly; heavy work is asynchronous with progress.
- Avoid synchronous dependency on messaging, PDF, analytics or non-critical provider calls for core transaction completion.
- Use pagination and server-side filtering; do not return unbounded collections.
- Provide clear loading, retry and offline/poor-connectivity behavior to clients.
- Do not cache financial/permission-sensitive results without tenant, branch, principal/policy version and freshness semantics.
- Measure p50/p95/p99, error/timeout rate, throughput and resource saturation per endpoint class.

## 7. Database and query growth controls

- Every high-volume table has an ownership/scope model, growth estimate, index plan and archival/retention classification.
- Avoid unbounded polymorphic relationships without referential integrity strategy.
- Use stable business IDs and idempotency keys; do not rely on timestamps as uniqueness.
- Batch imports/exports and backfills with checkpoints and rate controls.
- Use keyset/cursor pagination for large changing datasets where offset pagination becomes expensive.
- Track slow queries and query plans with safe parameter redaction.
- Define migration compatibility: expand → migrate/backfill → switch reads/writes → contract; avoid long blocking migrations.
- Test schema changes against realistic row volumes and rollback/recovery paths.
- Partitioning, sharding and denormalization require measured evidence and documented migration plans.

## 8. Capacity and autoscaling signals

Scale decisions should use combined signals:
- API CPU/memory and request queue/latency.
- Database CPU, I/O, locks, connection pool saturation, replication/log lag and slow-query volume.
- Worker queue depth, oldest job age, processing throughput, retry/dead-letter rates.
- Object storage upload/download latency, request rate and quota/budget thresholds.
- Cache memory/hit rate and eviction pressure where a cache is selected.
- Report/forecast concurrency, execution duration and freshness lag.
- Per-tenant traffic/storage concentration and fairness.
- Backup duration, log archive gap and restore throughput.

Autoscaling must respect database connection budgets and worker downstream limits. Scaling out application instances without controlling DB connections can worsen an outage. Scale-in must not terminate jobs without checkpoint/lease recovery.

## 9. Failure and overload behavior

- Define graceful degradation for non-critical reports, forecasts, exports, notifications and integrations.
- Preserve POS, delivery, procurement and financial transaction integrity under overload.
- Apply bounded queues; reject/slow non-critical work before exhausting memory or database connections.
- Use circuit breakers/timeouts for external providers; unknown outcomes go to reconciliation.
- Prevent retry storms with jitter, retry budgets and provider-aware throttling.
- Shed duplicate/redundant analytics refreshes and coalesce repeated report requests where safe.
- Provide operational status for delayed schedules and queues.
- Never silently lose accepted work; durable job/outbox state must expose backlog and failure.
- Recovery rebuild/re-index operations must be rate-limited so they do not starve live traffic.

## 10. Capacity testing strategy

Required tests:
1. Baseline load for all critical workflows with realistic tenant/branch data distribution.
2. Peak overlap: morning/evening routes, POS, subscription generation and notifications.
3. Month-end: billing, statements, payment allocations, accounting reports and exports.
4. Noisy-neighbour: one tenant creates large reports/imports while others use POS/delivery.
5. Soak: sustained expected load long enough to reveal memory leaks, connection leaks and queue drift.
6. Spike: abrupt traffic and webhook bursts.
7. Stress: find saturation points and confirm graceful degradation.
8. Failover/restart: API/worker/database/provider interruption and recovery.
9. Backup/restore: run ARC-DR-001 exercises while measuring live-service impact.
10. Data-growth: realistic large-table indexes, reports and migration/backfill.
11. Mobile compatibility: old supported clients under new API deployment.
12. Tenant isolation under load: concurrency and cache reuse must never cross tenant boundaries.

Every test report records build/release, environment, dataset shape, tenant mix, workload profile, duration, throughput, p50/p95/p99, errors, resource utilization, queue lag, database saturation, bottleneck, test limitations and pass/fail against approved targets.

## 11. Operational scaling path

Use evidence-led stages rather than premature infrastructure:
- **Stage A — Initial production:** modular monolith, stateless API, one authoritative relational database, durable background worker system, object storage, basic cache only where justified, external monitoring and tested off-site backups.
- **Stage B — Growth:** add API/worker replicas, read-only reporting capacity, query/index tuning, queue-class separation, object/CDN delivery, tenant quotas and pre-aggregated dashboards.
- **Stage C — High scale:** isolate analytical workloads, partition selected high-volume tables, introduce dedicated search/analytics services where justified, and extract only proven bottleneck modules behind stable APIs/events.
- **Stage D — Large platform:** consider tenant placement/sharding, regional architecture, service decomposition and advanced autoscaling only after measured capacity, team and operational maturity justify them.

Each stage requires an ADR with observed bottleneck, alternatives, cost, failure modes, migration path and rollback plan.

## 12. Development and production environment separation

- Development hardware limits affect local build/test parallelism, fixtures, emulators and optional services only.
- They must not be used to remove production features, lower production resilience targets or choose a permanently undersized production architecture.
- Keep development setup lean and avoid requiring resource-heavy container builds when not necessary; Docker is optional, not a project prerequisite.
- Use synthetic or anonymized datasets in development; never copy production personal/financial data casually.
- Production sizing uses expected/stress workload and approved SLO/RPO/RTO, not the developer workstation or development VPS.
- Maintain environment parity for behavior/configuration contracts while allowing resource sizes and service counts to differ.

## 13. Capacity review and ownership

ODR-027 is not closed by inventing a tenant count. Before production sizing, the product owner/business team must supply a target adoption envelope (tenants and branches by year), expected tenant profile distribution and critical operating windows. The engineering team must translate it into transaction, concurrency, storage and growth estimates, validate with pilot telemetry and load tests, and produce a capacity/cost model. Revisit quarterly or after material growth/feature launch.

## 14. Exit criteria

Development readiness requires module workload classification, scaling boundaries, per-tenant fairness, critical workflow performance test design, data-growth/index plan and observability coverage. Production readiness requires an approved workload envelope, measurable percentile SLOs, load/stress/soak evidence, database/worker capacity limits, overload runbooks, independent backup/restore validation and a funded scaling path.
