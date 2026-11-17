# Technology Evaluation and Provisional Technology Baseline

**Document ID:** ADR-TECH-001  
**Status:** Evidence-based shortlist and provisional proof-of-concept direction — final stack approval is gated by team capability, compatibility tests and production-operability review  
**Product:** Dairy ERP SaaS (India)  
**Decision date:** 2026-11-17  
**Authority:** Project Vision and Scope, FRS-001, NFR-001, ARC-API-001, ARC-IAM-001, ARC-CONC-001, ARC-EVT-001, ARC-SCALE-001, ARC-MOB-001, ODR-030/031.  
**Purpose:** Narrow the technology choices to maintainable candidates and define a reproducible evaluation, without choosing technology from familiarity, development-VPS limitations or unsupported assumptions.

## 1. Executive direction

The architecture baseline remains a modular monolith with a versioned API, relational transactional source of truth, durable background processing, provider-neutral object storage and independently scalable API/worker processes.

The following shortlist is recommended for proof of concept (PoC), not yet approved as the final production stack:

| Layer | Preferred PoC candidate | Alternative to evaluate | Current decision status |
|---|---|---|---|
| Backend/API | ASP.NET Core on .NET 10 LTS | Laravel 13; Node.js 26 LTS with a structured server framework | Preferred PoC candidate; final selection gated by team capability and domain PoC |
| Transactional database | PostgreSQL 18 latest supported minor | MySQL 8.4 LTS | PostgreSQL preferred PoC candidate; prove ORM/driver, locking, migration and operations |
| Mobile | Flutter stable channel | React Native stable channel | Flutter preferred PoC candidate for role-aware, offline-capable field workflows |
| Web client | Separate browser client consuming the same API; framework selection deferred to frontend PoC | React/Vue/Angular or server-rendered approach where justified | No framework selected |
| Background processing | Backend-native durable worker model plus transactional outbox | Dedicated broker/worker only when delivery guarantees or scale require it | Architecture selected; product/library choice requires PoC |
| Cache / coordination | Add Redis-compatible cache only for demonstrated shared-state/cache/queue needs | Database-backed/simple in-process mechanisms for low-risk use cases | Do not make cache a correctness dependency |
| Object storage | S3-compatible provider-neutral adapter; Cloudflare R2 and Backblaze B2 candidates | Other S3-compatible providers | Abstraction selected; provider selection and pricing verification separate |
| Hosting | Linux VPS-compatible deployment, reverse proxy, managed/system services as operationally appropriate | Managed services where cost/availability justify them | No hosting vendor or region selected |

A candidate becomes approved only after the PoC evidence and owner/team review described below. The preferred PoC candidate is not a claim that the user has approved the stack.

## 2. Non-negotiable selection criteria

Every candidate must be evaluated against the same weighted criteria. Scores must be recorded with evidence, not intuition.

| Criterion | Weight | Required evidence |
|---|---:|---|
| Transaction integrity and concurrency | 20% | Atomic uniqueness/idempotency, isolation/locking, deadlock handling, double-entry and stock invariants under deterministic race tests |
| Security and tenant isolation | 15% | Authentication/authorization integration, tenant/branch scoping, audit, secret management, patch lifecycle |
| Maintainability and ecosystem maturity | 15% | Supported releases, migration path, stable libraries, documentation, upgrade burden |
| Domain and API suitability | 10% | Complex workflows, validation, versioned APIs, background jobs, integration adapters |
| Operational simplicity on Linux VPS | 10% | Deployment, upgrades, monitoring, backup/restore, process supervision and rollback |
| Performance and scaling path | 10% | Representative workload PoC; no synthetic benchmark claims |
| Mobile/offline integration | 10% | Secure local persistence, sync queue, device capabilities, API compatibility and testing |
| Total lifecycle cost | 5% | Hosting, licensing, support, development and maintenance effort |
| Localization and accessibility | 5% | Unicode, Marathi/Hindi/English, input/display, accessibility and document generation |

The product's current development VPS resource limits MUST NOT be used to disqualify a production-capable technology or reduce product scope. The PoC environment may be lightweight; production sizing is governed by ARC-SCALE-001.

## 3. Backend/API candidates

### 3.1 ASP.NET Core / .NET 10 LTS — preferred PoC candidate

Evidence:
- Microsoft's support policy lists .NET 10 as an active LTS release with support through 14 November 2028. .NET 11 was released on 10 November 2026 as STS and has a shorter support window. For a long-lived ERP, the LTS track is the safer initial comparison baseline. [Official .NET support policy](https://dotnet.microsoft.com/en-us/platform/support/policy) (checked 2026-11-17).
- The platform supports Linux and is suitable for API and worker processes; the exact hosting/runtime configuration must be validated in the deployment PoC.

Why it is shortlisted:
- Strongly typed domain contracts and compile-time checks are useful for accounting, inventory, subscription and integration invariants.
- API and background worker processes can share domain/application contracts while remaining independently deployable/scalable.
- The LTS support window provides a clear patch and upgrade planning horizon.

Risks/verification:
- Confirm the available development team's practical .NET/C# experience; do not assume it.
- Prove ORM behavior for PostgreSQL constraints, transactions, row locking, concurrency tokens and migration rollback.
- Prove operational deployment, diagnostics, PDF/document generation, localization and Tally adapter integration on the intended Linux VPS.
- Avoid splitting the modular monolith into multiple services merely because the framework supports it.

### 3.2 Laravel 13 — strong alternative

Evidence:
- Laravel's official release policy lists Laravel 13 as released 17 March 2026, supporting PHP 8.3–8.5, with bug fixes through Q3 2027 and security fixes through 17 March 2028. Laravel provides 18 months of bug fixes and 2 years of security fixes per release. [Official Laravel release/support policy](https://laravel.com/framework/docs/releases) (checked 2026-11-17).
- Laravel 13's deployment documentation specifies PHP 8.3 or higher and documents standard Linux web-server deployment options. [Official Laravel deployment documentation](https://laravel.com/framework/docs/13.x/deployment).

Why it is shortlisted:
- Integrated conventions for routing, validation, database access, queues, scheduling, notifications and application scaffolding can reduce initial delivery friction.
- Suitable for a modular monolith when domain boundaries and transaction ownership are enforced independently of framework conventions.

Risks/verification:
- The shorter per-major support window means planned framework upgrades and dependency governance are essential.
- Prove complex transaction/concurrency patterns rather than assuming ORM convenience guarantees accounting or stock correctness.
- Confirm the team's PHP/Laravel expertise and the long-term cost of maintaining a large domain model.

### 3.3 Node.js 26 LTS — alternative candidate

Evidence:
- The official Node.js release schedule lists Node.js 26 as Active LTS beginning 28 October 2026, with end of life on 30 April 2029. Node.js 27 is Alpha at the decision date and is not a production candidate. [Official Node.js release schedule](https://nodejs.org/en/about/previous-releases) (checked 2026-11-17).

Why it is shortlisted:
- A TypeScript-based backend can share language/tooling concepts with a TypeScript web client.
- Mature ecosystem for API integrations and asynchronous I/O.

Risks/verification:
- Select and govern a structured framework, validation, dependency and background-job conventions; do not let the ecosystem's flexibility create inconsistent module boundaries.
- Prove transactional invariants, job durability, schema validation and operational diagnostics.
- Avoid selecting it solely to share a language with the browser client; that does not remove the need for server-side business rules.

### 3.4 Backend decision gate

The backend must not be finalized by popularity or benchmark charts. Run the same vertical-slice PoC in the two strongest candidates (initially .NET 10 and Laravel 13; include Node.js 26 only if team capability or evidence justifies it). Each slice must include:
- Tenant and branch authorization on every read/write.
- Customer subscription occurrence generation with idempotency.
- A POS sale with stock movement and accounting posting boundary.
- A balanced double-entry journal and immutable reversal.
- Concurrent duplicate command and last-stock-unit race tests.
- Transactional outbox and retry after process crash.
- Tally export/import adapter boundary.
- Versioned API contract and automated API tests.
- PDF generation and object-storage adapter.
- Logs, metrics, health checks, backup/restore and deployment rollback.

Record developer effort, defects, test evidence, operational complexity and upgrade burden. No candidate passes if it cannot prove correctness under deterministic concurrency tests.

## 4. Transactional database candidates

### 4.1 PostgreSQL 18 — preferred PoC candidate

Evidence:
- The PostgreSQL versioning policy lists PostgreSQL 18 as supported through 14 November 2030, with 18.7 as the current minor at the date of this decision. PostgreSQL 17 is supported through 8 November 2029. PostgreSQL 14 reached its final support date on 12 November 2026 and must not be selected for a new production baseline. [Official PostgreSQL versioning policy](https://www.postgresql.org/support/versioning/) (checked 2026-11-17).
- PostgreSQL 18.7 was released on 12 November 2026 with security and bug fixes across supported versions. [Official release announcement](https://www.postgresql.org/about/news/postgresql-187-1712-1616-1520-1425-and-19-rc-2-released-3442/).

Why it is preferred for PoC:
- Strong relational constraints and transaction semantics suit the authoritative financial/stock ledger.
- A long support horizon and current supported release make it a sound new-system candidate.
- It supports a future path for reporting and tenant-aware query design without requiring a separate analytics database at launch.

Required proof:
- Test exact database/driver/ORM compatibility, unique constraints, isolation, row locks, deadlocks, JSON use only where appropriate, migration duration and restore.
- Test tenant/branch query patterns and any row-level security proposal; application authorization remains mandatory even if database controls are added.
- Prove double-entry precision and quantity precision using exact decimal types, never floating-point monetary arithmetic.
- Select a supported minor release and patch cadence; never deploy PostgreSQL 19 RC as production.

### 4.2 MySQL 8.4 LTS — alternative

Evidence:
- Oracle documents MySQL LTS releases as stability-oriented, limiting behavior changes within an LTS series, with a support policy of five years Premier Support and three years Extended Support. [Official MySQL release model](https://dev.mysql.com/doc/refman/8.4/en/mysql-releases.html) (checked 2026-11-17).

Why it remains a valid alternative:
- Mature relational database with broad hosting/provider availability.
- LTS lifecycle is attractive for conservative operations.

Required proof:
- Run the same concurrency, transaction, precision, indexing, backup/restore and tenant-scope PoC.
- Verify database engine/version configuration and ORM behavior for all required constraints.
- Compare operational expertise and managed hosting costs in the actual target environment.

### 4.3 Database decision gate

PostgreSQL 18 is the preferred PoC candidate, not yet a final lock. The database choice must be made together with the backend/ORM combination because transaction behavior, migrations, connection pooling and operational tooling are coupled. Production must use a supported stable LTS/current supported major and current security patches.

## 5. Mobile technology candidates

### 5.1 Flutter — preferred PoC candidate

Evidence:
- Flutter's official architecture documentation includes an offline-first design pattern that separates local and remote data sources and explains feature-level offline approaches. It explicitly treats offline behavior as a requirements-led choice rather than requiring all functionality to work offline. [Official Flutter offline-first architecture guide](https://docs.flutter.dev/app-architecture/design-patterns/offline-first) (checked 2026-11-17).

Why it is preferred for PoC:
- One role-aware application can target the mobile platforms selected for release while implementing a controlled local database and synchronization boundary.
- The offline-first guidance aligns with ARC-MOB-001's limited delivery-run cache and queued outcome capture.
- It allows a dedicated mobile client without coupling the web frontend to the mobile UI framework.

Required proof:
- Validate Android and iOS device coverage required by the market, background sync constraints, secure local database/key handling, barcode/camera/location needs if applicable, printing support, Marathi/Hindi fonts/input and accessibility.
- Demonstrate encrypted/minimized route snapshot storage, durable queue after process termination, idempotent sync and remote revocation behavior.
- Validate plugin maintenance, build/release signing, app-store delivery and upgrade process.

### 5.2 React Native — alternative

Evidence:
- React Native's official release overview lists 0.88.x as Active after its 15 October 2026 release and 0.87.x as Active; it commits to maintaining the latest three minor series. [Official React Native release overview](https://reactnative.dev/releases/overview) (checked 2026-11-17).
- Its versioning policy states that breaking changes may arrive in a new minor series, so upgrade discipline is necessary. [Official React Native versioning policy](https://reactnative.dev/releases/versioning-policy).

Why it remains viable:
- Appropriate where the team has strong React/TypeScript skills and needs native mobile capabilities.
- The ecosystem and shared TypeScript conventions may help coordination with a TypeScript web client.

Required proof:
- Verify offline persistence, secure storage, native module quality, supported OS matrix, release cadence and dependency upgrade cost.
- Test background task behavior on both major mobile platforms; do not assume background execution is guaranteed.

### 5.3 Mobile decision gate

Flutter is the preferred first PoC because offline delivery is a core field requirement and the official architecture guidance directly supports a local/remote repository approach. React Native remains a valid alternative if demonstrated team expertise or device/plugin requirements materially improve its total lifecycle score. Do not choose a mobile technology before the delivery offline vertical slice passes ARC-MOB-001 tests.

## 6. Web frontend

No web frontend framework is selected in this document. The web client must:
- Consume the same versioned API and authorization outcomes as mobile.
- Support owner/manager and operational workflows without assuming every user is desktop-only.
- Support responsive layouts, keyboard accessibility, localization, Unicode, large data tables and controlled exports.
- Avoid embedding authoritative business calculations in browser code.
- Provide a sustainable component/testing strategy and stable supported release policy.

Compare React, Vue and Angular (or a justified server-rendered option) using a representative dashboard + POS + subscription management slice. Select based on team capability, accessibility, complex form/table needs, maintainability, testing and bundle/performance evidence—not framework popularity alone.

## 7. Background jobs, messaging and cache

The logical architecture is already selected: durable asynchronous processing, transactional outbox, idempotent handlers, bounded retries, dead-letter/manual resolution and tenant fairness.

Technology baseline:
- Begin with the backend framework's durable queue/worker facilities only if the chosen adapter meets the outbox, retry, lease, visibility and recovery requirements.
- Add a dedicated broker when independent worker scaling, delivery guarantees, fan-out or measured queue throughput justifies it.
- Use Redis-compatible infrastructure for cache, coordination or queueing only with explicit persistence/failure semantics. Redis cache loss must not corrupt financial or stock correctness.
- Keep the transactional database authoritative for job intent/outbox state and business state.
- Separate critical business queues from best-effort notifications and analytics.

PoC must kill workers at every processing boundary, retry deliveries, expire leases and prove no duplicate financial/stock effect.

## 8. File and object storage

Use the provider-neutral object-storage interface already required by ARC-STO-001 and ARC-SCALE-001. Candidate providers include Cloudflare R2 and Backblaze B2, but free-tier allowances are not a production availability or cost guarantee.

Before provider selection, compare:
- S3 API compatibility and any provider-specific differences.
- Storage, operation, retrieval, egress, minimum retention and lifecycle charges.
- Data location, contractual terms, durability/availability commitments and support.
- Signed URL scope/expiry, encryption, versioning, deletion and legal retention.
- Restore/export portability and tested provider migration.
- Actual monthly workload using measured object count, size, read/write/retrieval and egress.

Do not select a provider from advertised free quota alone. Keep metadata and authorization in the application; object keys must not reveal tenant/customer details.

## 9. Linux VPS deployment compatibility

The production design must remain deployable on a Linux VPS without making Docker mandatory. The deployment PoC must verify:
- Reverse proxy and TLS termination.
- API process supervision, worker process supervision and graceful restart.
- Database installation/managed service choice and secure network boundaries.
- Separate environment secrets and key rotation.
- Scheduled jobs, queue monitoring and durable logs.
- Health/readiness endpoints, metrics, alerts and incident diagnostics.
- Backup, restore, point-in-time recovery where supported, and off-site copy.
- Safe deployment: maintenance/compatibility strategy, expand-migrate-contract database changes, rollback and worker compatibility.
- Resource limits and connection budgets based on production workload, not development machine constraints.

Container support may be documented as an optional packaging/deployment method, not a prerequisite.

## 10. Proof-of-concept plan and acceptance gates

Run a time-boxed PoC using synthetic/anonymized data. The PoC is not a production implementation and must not alter the approved domain architecture.

| PoC | Deliverable | Pass criteria |
|---|---|---|
| Backend + database vertical slice | Subscription occurrence, POS sale, stock movement, journal posting, audit and outbox | Deterministic race tests pass; no duplicate posting; tenant/branch isolation passes |
| Mobile offline delivery | Assigned route cache, delivery capture, restart-safe queue, sync and conflict UI | ARC-MOB-001 acceptance tests pass on target devices; revocation/expiry works |
| Web client slice | Dashboard, subscription management and POS interaction | Same API semantics; accessible responsive UI; no duplicated business rules |
| File storage | PDF/evidence upload/download through provider adapter | Authorization, signed URL expiry, lifecycle, portability and restore pass |
| Operations | Linux VPS deployment, workers, monitoring and backup restore | Repeatable deploy/rollback; recovery evidence; no undocumented manual step |
| Tally boundary | Sample export/import mapping and idempotent sync batch | Stable identifiers, reconciliation, retry and unknown-outcome handling pass |

A PoC report must include candidate versions, exact environment, source links, code/build identifiers, test dataset, workload, test commands, raw results, defects, limitations, costs and recommendation. Benchmark results without reproducible workload and environment are not decision evidence.

## 11. Decision register and ownership

| Decision | Classification | Interim direction / closure condition |
|---|---|---|
| Modular monolith, API-first, durable async work | Recommended engineering baseline already defined in ARC-SCALE-001/ARC-API-001 | Preserve; do not reopen without contrary evidence |
| Backend framework | Preferred PoC: ASP.NET Core/.NET 10 LTS; Laravel 13 strong alternative; Node.js 26 conditional alternative | Final selection after identical domain PoC and team capability review |
| Database engine | Preferred PoC: PostgreSQL 18; MySQL 8.4 LTS alternative | Select with backend/ORM after integrity and operations tests |
| Mobile framework | Preferred PoC: Flutter; React Native alternative | Select after delivery offline field/device PoC |
| Web framework | Evidence required | Compare representative web workflow and team capability |
| Queue/broker/cache | Recommended logical architecture; concrete product evidence required | Select minimal durable option; add broker/cache only with proof |
| Object storage provider | Evidence and owner approval required | Compare R2/B2 and other candidates using actual workload, contract and cost |
| VPS/provider/region | Owner approval and evidence required | Choose after data-location, availability, support, cost and operational responsibility review |
| Supported OS/device matrix and app release policy | Owner approval required after field trial | Define before mobile production release |

## 12. References and evidence date

All release/support facts below were checked on 2026-11-17. Re-check immediately before final technology approval and before production deployment because support status changes.

1. Microsoft .NET support policy: https://dotnet.microsoft.com/en-us/platform/support/policy
2. Laravel release/support policy: https://laravel.com/framework/docs/releases
3. Laravel 13 deployment requirements: https://laravel.com/framework/docs/13.x/deployment
4. Node.js release schedule: https://nodejs.org/en/about/previous-releases
5. PostgreSQL versioning policy: https://www.postgresql.org/support/versioning/
6. PostgreSQL 18.7 release announcement: https://www.postgresql.org/about/news/postgresql-187-1712-1616-1520-1425-and-19-rc-2-released-3442/
7. MySQL LTS/Innovation release policy: https://dev.mysql.com/doc/refman/8.4/en/mysql-releases.html
8. Flutter offline-first architecture: https://docs.flutter.dev/app-architecture/design-patterns/offline-first
9. React Native release overview: https://reactnative.dev/releases/overview
10. React Native versioning policy: https://reactnative.dev/releases/versioning-policy

**Exit condition:** ODR-030 can be closed only after the PoC report, team capability review, operational cost model, compatibility/security review and explicit approval of the selected stack are recorded. Until then, the shortlist and preferred PoC directions are recommendations, not approved implementation technology.
