# Production VPS Deployment and Infrastructure Architecture

**Document ID:** ARC-DEP-001  
**Status:** Technology-neutral deployment baseline — provider, region, server sizes and production service commitments require evidence and owner approval  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, NFR-001, ARC-TEN-001, ARC-IAM-001, ARC-API-001, ARC-EVT-001, ARC-CONC-001, ARC-STO-001, ARC-SEC-001, ARC-OBS-001, ARC-DR-001, ARC-SCALE-001, ADR-TECH-001, ODR-027/028/030/031/033/034.  
**Purpose:** Define a secure, recoverable Linux VPS deployment model for the API-first Dairy ERP SaaS, without making Docker, a specific VPS vendor, a specific database engine or development-machine capacity a prerequisite.

## 1. Architecture decisions and boundaries

### 1.1 Selected logical deployment model

Adopt a **Linux VPS-compatible, modular-monolith deployment** with distinct process roles and trust boundaries:

1. Public edge: DNS, TLS termination, reverse proxy and optional CDN/WAF/DDoS protection.
2. Application tier: stateless web/API application instances.
3. Background processing tier: independently supervised workers separated by job criticality and resource profile.
4. Data tier: authoritative transactional relational database on a private network/interface.
5. Object tier: provider-neutral external object storage for documents and backups.
6. Operations tier: monitoring, metrics, structured logs, alerting, deployment control and backup/restore tooling.

These are logical boundaries. They may initially share a VPS where cost and workload justify it, but must remain separately configurable and separable without changing business logic. API and worker processes must not depend on local process memory for durable state.

### 1.2 Docker is optional

The platform MUST be deployable without Docker using supported Linux packages, a reverse proxy, process supervision and documented release procedures. Containers MAY be used as an optional packaging and deployment method if they simplify reproducibility and operations. No feature, development workflow or production capability may require Docker merely because it is convenient.

### 1.3 What is not selected here

This document does not select:
- VPS/cloud provider, region, availability zone or contractual SLA.
- Exact CPU, RAM, disk, bandwidth or server count.
- Database engine or version (ADR-TECH-001 preferred PoC candidate: PostgreSQL 18; not yet approved).
- Backend framework/runtime or mobile/web framework.
- Object storage provider (Cloudflare R2 and Backblaze B2 remain candidates).
- CDN/WAF, DNS, monitoring, secrets manager or managed database vendor.
- Statutory retention periods or legal data-residency conclusions.

These choices require the evidence and approvals identified in ODR-026/027/028/030/031/033/034.

## 2. Logical topology

### 2.1 Public edge

- Public DNS points only to the intended edge endpoint.
- Enforce HTTPS; redirect HTTP to HTTPS and use modern TLS configuration.
- Terminate TLS at a maintained reverse proxy or approved edge service.
- Expose only required public routes: web frontend/static assets, API, and explicitly required webhook/callback endpoints.
- Do not expose database, cache, worker control panels, metrics endpoints, internal admin ports or SSH to the public internet without a separately approved restricted access path.
- Apply request-size, connection, timeout and rate limits at the edge and again in application policy.
- Use CDN/WAF/DDoS services where threat and cost evaluation supports them; do not treat them as a substitute for application authorization.
- Keep secrets and private keys outside source control and public web roots.
- Use separate hostnames or routing boundaries for public application and restricted operations where appropriate.

### 2.2 Application and API tier

- Run the API/web application as a non-root operating-system identity with only required file and socket permissions.
- Keep application instances stateless; persist business state in the transactional database and durable job/outbox state.
- Do not store uploaded business files, durable sessions, queue state or authoritative reports only on local VPS disk.
- Configure explicit connection pools, request timeouts, body limits, memory limits and graceful shutdown.
- Use environment-specific configuration and secret injection; never bake production secrets into build artifacts.
- Expose health/readiness endpoints with no sensitive detail. Readiness must reflect dependencies needed to serve the relevant operation class.
- Do not let diagnostic or health endpoints bypass tenant/security boundaries or reveal infrastructure secrets.

### 2.3 Worker tier

Separate workers by operational risk and resource profile, at minimum:
- Critical business jobs: schedule generation, durable outbox processing and domain workflows that have explicit transaction/recovery semantics.
- Financial/integration jobs: accounting exports, Tally synchronization and payment/bank integration reconciliation.
- Document and bulk jobs: PDF generation, imports/exports and file processing.
- Best-effort jobs: notifications, analytics refreshes and non-critical external calls.

Queue names and physical process counts may vary by framework, but isolation, priority, retry and monitoring semantics must be preserved. A notification burst must not starve critical delivery/accounting work. Workers run under a process supervisor and use leases/idempotency; a restart must not silently lose accepted work.

### 2.4 Data tier

- Database access is private to approved application/worker identities and administrative recovery paths.
- Do not expose the database listener to the public internet.
- Use least-privilege database roles: application runtime, migration/deployment, read-only reporting where justified, backup and recovery.
- Runtime credentials must not have superuser, database-owner or backup-deletion authority.
- Enforce TLS for database connections when traffic crosses hosts or untrusted network boundaries.
- Apply connection budgets across API and worker pools; application scale-out must not exhaust database connections.
- Use transactional constraints, unique idempotency keys, foreign keys and exact decimal types for money/quantity invariants.
- Read replicas are optional and only for explicitly stale-tolerant queries; read-after-write and financial decisions must use a consistency-safe path.
- Database engine/version selection follows ADR-TECH-001 and must pass concurrency, backup, restore and migration PoC.

### 2.5 Object and backup tier

- Use the provider-neutral object-storage adapter from ARC-STO-001.
- Store document metadata, tenant ownership, object version, checksum, classification and lifecycle state in the authoritative database.
- Keep operational documents, temporary exports, archive data and backup generations in separate logical storage classes and access policies.
- Application credentials may create/read permitted operational objects but must not delete protected backup history.
- Backup identity and keys are separate from application runtime credentials.
- Encrypt backup payloads before remote upload as defined in ARC-DR-001.
- Validate checksum and object/database manifest consistency.
- Never treat local VPS snapshots as the only backup copy.

## 3. Deployment topology profiles

The system must support multiple physical deployment profiles while preserving the same logical boundaries.

| Profile | Placement | Appropriate use | Risks and required controls |
|---|---|---|---|
| Profile A — compact pilot | Reverse proxy, API, workers and database on one VPS; object storage and backup off-host | Controlled pilot or low-volume early service where a single-host failure is accepted | Single point of failure, shared resource contention, strict firewall, process isolation, connection limits, independent backup and tested restore |
| Profile B — separated data | Edge/API/workers on application VPS; database on a private separate VPS or managed database; object storage external | Preferred production direction when service continuity and data protection justify additional cost | Private network/TLS, database access policy, network outage handling, independent database backup and failover/restore runbooks |
| Profile C — horizontally scalable | Multiple stateless API instances, dedicated worker pools, separate database capacity, external object storage and centralized telemetry | Growth/stress profile when measured demand requires it | Load balancing, shared durable state, connection pooling, worker fairness, deployment coordination and tested recovery |

Profile A is not a claim of high availability. It may be used only when the business explicitly accepts the single-host risk and the recovery plan has been tested. Profile B is the recommended production direction to evaluate first; actual provider and topology require cost, availability, security and operations evidence. Profile C is triggered by measured workload and ARC-SCALE-001 capacity signals, not speculation.

## 4. Network and firewall model

Define explicit network zones and allow-lists:
- Public ingress: only HTTP/HTTPS to the reverse proxy or approved edge.
- Administrative ingress: SSH or remote administration only through restricted source IP/VPN/bastion or equivalent controlled access; disable password-based root SSH and use managed keys/strong MFA where supported.
- Application-to-database: only approved application/worker hosts and required database port.
- Application-to-object storage: outbound TLS to provider endpoints; no public inbound storage administration.
- Monitoring/backup: dedicated identities and narrowly scoped network paths.
- Internal service ports: bind to loopback/private interfaces; never wildcard-bind internal dashboards to public interfaces.

Default-deny inbound firewall policy is required. Outbound egress should be restricted where practical to required operating-system repositories, package registries, object storage, messaging/payment/Tally providers and monitoring endpoints. Maintain an inventory of permitted external destinations and review it when integrations change.

## 5. Operating-system and host hardening

- Select a maintained Linux server distribution/release with a documented security-support window at procurement time.
- Apply security updates on a defined schedule, with emergency patch handling for actively exploited vulnerabilities.
- Minimize installed packages, disable unused services and remove default credentials.
- Use separate non-root service accounts and least-privilege filesystem permissions.
- Restrict SSH, require strong key-based access and log privileged access.
- Use host firewall, intrusion/abuse detection and malware/file scanning controls appropriate to the risk.
- Configure time synchronization; store event timestamps consistently and preserve IST business-date semantics in application data.
- Encrypt disks/volumes where supported and manage recovery keys separately; encryption at rest does not replace access control or backup encryption.
- Monitor disk capacity, inode exhaustion, memory, CPU, I/O, network and certificate expiry.
- Keep production and development credentials, databases, storage buckets and environments separate.
- Do not copy production personal/financial data into development or test systems without approved minimization/anonymization.

## 6. Secrets, identity and privileged operations

- Keep secrets out of Git, application images, shell history, tickets, logs and client bundles.
- Use a secrets manager or a protected host-level secret-injection mechanism with documented access and recovery controls.
- Separate identities for deployment, application runtime, database migration, backup, monitoring, object storage and integrations.
- Rotate credentials and keys with a documented overlap/revocation procedure; record owner, purpose, scope, creation and expiry/review date.
- Require MFA for privileged control-plane, hosting, source-control and backup-provider accounts where supported.
- Production access must be attributable, least-privileged, time-bounded where feasible and audited.
- Break-glass access must require a reason, alert, post-incident review and credential rotation.
- Platform support access to tenant data remains governed by ARC-IAM-001 and ARC-SEC-001; infrastructure access is not tenant business authorization.

## 7. Release and deployment pipeline

### 7.1 Build and artifact handling

- Build from reviewed, version-controlled source and pinned dependency lockfiles.
- Run automated tests, static analysis, dependency/security scanning and secret scanning before release.
- Produce versioned, immutable release artifacts with commit/build identifiers and provenance.
- Do not build directly on production from unreviewed working-tree state.
- Separate build credentials from production deployment credentials.
- Restrict production deployment to authorized, auditable identities.

### 7.2 Deployment sequence

Use a controlled release process:
1. Verify backup health and current recovery position.
2. Confirm migration compatibility and release notes.
3. Deploy backward-compatible/expand schema changes first.
4. Deploy API/web artifact and compatible workers.
5. Run smoke, health, tenant-isolation and critical workflow checks.
6. Enable new behavior through controlled configuration/feature flags where appropriate.
7. Monitor errors, latency, queue age, database saturation and business reconciliation.
8. Complete contract/migration cleanup only after old clients/workers no longer depend on prior schema.
9. Record release evidence and rollback/forward-fix decision.

Use expand → migrate/backfill → switch → contract. Avoid destructive schema changes in the same release that first introduces replacement fields. Database rollback must not be assumed safe after new writes; use forward-compatible recovery or a documented restore/repair plan.

### 7.3 Rollback and worker compatibility

- Retain the last known-good artifact and deployment metadata.
- API and worker versions must be compatible with the current schema and queued job/event contracts.
- Pause or drain incompatible workers before deploying schema/event changes.
- Do not delete or reinterpret queued jobs during rollback.
- If a release creates committed financial/stock effects, code rollback alone does not reverse business transactions; use linked business reversals and reconciliation.
- Document the trigger for rollback versus forward-fix and the person authorized to decide.

## 8. Monitoring, logging and alerting

Implement ARC-OBS-001 record distinctions:
- Business history and security audit are durable authoritative evidence, not ordinary diagnostic logs.
- Operational logs are structured, correlated and redacted.
- Metrics cover request latency/error rate, CPU/memory/disk/I/O, database connections/locks/slow queries, queue depth/oldest age, worker retries, object-store latency and backup/restore status.
- Distributed traces connect API request → transaction/outbox → worker → external provider using correlation/causation IDs.
- Tenant-aware metrics must not expose tenant-sensitive payloads or permit cross-tenant data access.

Minimum actionable alerts:
- API availability/error/latency threshold breach.
- Database unavailable, connection pool saturation, disk/WAL pressure, replication lag where applicable.
- Critical queue oldest-job age, dead-letter growth and stuck leases.
- Backup failure, missing transaction-log archive, checksum mismatch or restore-test failure.
- Object storage access failures, quota/cost anomaly and manifest mismatch.
- TLS certificate expiry, privileged login anomaly, repeated authentication abuse and suspicious data export.
- Host CPU/memory/disk saturation and service process restart loops.

Every alert must have severity, owner/on-call path, runbook, escalation and acknowledgement expectation. Final SLO thresholds and named responders require ODR-028/034 approval; do not invent an on-call roster.

## 9. Backup, disaster recovery and business continuity

Follow ARC-DR-001 and ARC-STO-001:
- Daily consistent database backup plus continuous/bounded transaction-log archival where supported.
- Independent encrypted off-host backup copies and least-privilege backup identity.
- Separate backup account/credentials from production runtime and application administration.
- Versioned object manifests and checksum verification.
- Monthly isolated restore exercise and quarterly disaster-recovery exercise as the current engineering baseline.
- Restore sequence: infrastructure/identity/secrets → database → object references/files → workers/integrations → tenant traffic.
- Pause external side-effect workers after restore until payment/Tally/provider outcomes are reconciled.
- Validate tenant and branch isolation after restore; do not restore a tenant dataset into an unscoped shared environment.
- Tenant-specific recovery must preserve cross-tenant isolation and shared ledger correctness; recovery method requires explicit tested runbook.

The initial RPO ≤ 15 minutes and RTO ≤ 4 hours are planning targets only. They are not achieved service commitments until provider capability, staffing, capacity and restore exercises prove them and the owner approves the production service tier.

## 10. Capacity, scaling and cost controls

Use ARC-SCALE-001:
- Size production from conservative/expected/stress workload envelopes, not developer hardware.
- Monitor p50/p95/p99 workflow latency, throughput, DB saturation, queue age, worker utilization, storage and backup throughput.
- Keep API and workers independently scalable.
- Set database connection budgets before increasing application replicas.
- Apply tenant fairness, rate limits, export/report quotas and bounded queues.
- Avoid adding a cache, broker, search cluster, read replica or microservice without measured need and an ADR.
- Track VPS, database, backup, object storage, egress, monitoring, email/SMS/WhatsApp, domain and certificate costs.
- Alert on cost anomalies and tenant usage concentration.
- Model at least 12 months of cost under conservative/expected/stress assumptions before committing to a provider or plan price.

The exact server sizing worksheet requires owner adoption assumptions and pilot telemetry under ODR-027. Do not invent CPU/RAM/storage numbers.

## 11. Environment separation

Maintain distinct development, test/staging and production environments:
- Separate databases, object buckets, credentials, signing keys, API origins and integrations.
- Test/staging uses synthetic or anonymized data.
- Production secrets and backup keys are inaccessible to ordinary development accounts.
- Production deployments require review and evidence.
- Configuration differences are explicit and version-controlled without embedding secrets.
- Test restore and deployment procedures in a production-like isolated environment.
- Do not allow a staging worker to process production queues or a development client to access production tenant data.

## 12. Operational runbooks required before production

Create and test runbooks for:
1. Initial provisioning and secure host hardening.
2. DNS, TLS, reverse proxy and certificate renewal.
3. Application and worker deployment, restart, drain and rollback.
4. Database migration, backup, point-in-time restore and credential rotation.
5. Object storage upload/download outage, manifest reconciliation and provider migration.
6. Queue backlog, dead-letter and stuck-job recovery.
7. Security incident, account compromise, secret/key leak and break-glass access.
8. Tenant isolation incident and tenant-specific recovery.
9. VPS/provider outage, replacement host provisioning and DNS cutover.
10. Restore/DR exercise and post-recovery reconciliation.
11. Cost spike, storage quota exhaustion and provider rate limiting.
12. Mobile/API compatibility incident and client version rollback/disable strategy.

Every runbook must identify prerequisites, authorized role, exact steps, expected outputs, stop conditions, rollback/recovery, evidence to retain and escalation owner. Named operational owners and support hours require explicit owner approval.

## 13. Production readiness gates

A production deployment is not ready until:
- ODR-031 provider/region/topology decision is recorded with cost, security, data-location and support evidence.
- ODR-027 workload envelope and capacity/cost model are approved.
- ODR-028 RPO/RTO and service tier are accepted; restore and DR exercises pass.
- ODR-033/034 security governance, alert ownership and incident response are approved.
- Firewall, privileged access, secrets, TLS, backup separation and audit controls are verified.
- Tenant/branch isolation tests pass across API, DB, cache, jobs, files, reports and support tooling.
- Load, stress, soak and failure tests meet approved targets.
- Deployment/rollback and all critical runbooks have been exercised.
- Monitoring and alert delivery are proven; a dashboard alone is not evidence of response capability.
- Object storage and database recovery are tested together.
- No unresolved critical/high security risk lacks an explicitly approved, time-bounded treatment.

## 14. Decision classification

| Decision | Classification | Closure requirement |
|---|---|---|
| Linux VPS-compatible architecture; Docker optional | Recommended engineering baseline | Preserve in implementation and deployment documentation |
| Logical edge/application/worker/data/object/operations separation | Recommended engineering baseline | Validate with selected backend/database PoC |
| Compact one-VPS profile | Conditional pilot option | Use only with explicit single-host risk acceptance and independent recovery |
| Separate database tier for production | Recommended direction to evaluate first | Validate provider/network cost, operational ownership and recovery |
| VPS provider, region and contract | Owner approval + evidence required | Compare data location, availability, support, security, cost and contractual terms |
| Server sizing and tenant capacity | Evidence required / ODR-027 | Pilot telemetry and workload envelopes |
| RPO/RTO and named responders | Owner approval required / ODR-028 | Restore/DR evidence and operational staffing |
| Security risk acceptance and incident governance | Owner approval required / ODR-033/034 | Threat review, named authority and runbook exercises |
| Docker/container use | Optional implementation choice | Select only if it improves reproducibility/operations without becoming a prerequisite |

**Exit condition:** ODR-031 is closed only after a provider/topology recommendation is supported by current evidence and explicit owner approval. This document defines the architecture and required controls; it does not claim that any VPS has been provisioned, hardened, monitored or tested.
