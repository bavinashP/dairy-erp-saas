# SaaS Multi-Tenancy and Data Isolation Architecture

**Document ID:** ARC-TEN-001  
**Status:** Proposed architecture baseline — technology and topology decisions open  
**Authority:** Project Vision and Scope, FRS-001, ERS-001, Actor and Access Model, Business Rules Catalogue, and ODR-001..005, ODR-025..028, ODR-031.  
**Purpose:** Define tenant and branch isolation invariants, request/job context rules, and architecture evaluation criteria without selecting a database, framework or deployment topology.

## 1. Scope

This document defines the logical SaaS isolation boundary for tenant-owned business data and the distinction between tenant-wide, branch-attributed and platform-owned records.

It does not approve a physical schema, shared-database strategy, database-per-tenant strategy, schema-per-tenant strategy, sharding model, hosting provider, service topology or exact legal-entity model.

## 2. Core terminology

- **Platform:** SaaS operator environment and platform-owned configuration.
- **Tenant:** One dairy business workspace and primary business-data isolation boundary.
- **Branch:** A tenant-owned operating unit. Branch count is independent of GST registration.
- **Operating location:** A typed place such as outlet, warehouse, collection point, processing site or stock location. It is not automatically a branch or legal entity.
- **Tenant-wide record:** A record belonging to a tenant but not inherently attributable to one branch.
- **Branch-attributed record:** A business event or resource associated with a specific branch/location.
- **Platform principal:** An identity authorized for platform operations; platform authority does not automatically grant tenant business-data access.
- **Tenant membership:** The relationship that associates an identity with a tenant and its status.

## 3. Non-negotiable isolation invariants

1. Every tenant-owned record MUST have an unambiguous, provable tenant ownership path.
2. Every tenant-bound request MUST derive tenant context from a validated authenticated membership/session or an explicitly authorized platform workflow; never trust a client-supplied tenant ID as proof of access.
3. Tenant context MUST be established before tenant-owned data access and MUST fail closed when missing, invalid, suspended or ambiguous.
4. Tenant isolation MUST apply to reads, writes, aggregates, search, reports, exports, attachments, caches, background jobs, events, webhooks and integrations.
5. Branch scope MUST be checked independently from tenant scope.
6. A branch ID supplied by a client MUST be validated as belonging to the active tenant and within the actor's authorized branch scope.
7. Tenant-wide records may be branch-neutral. Do not force branch attribution where the domain does not require it.
8. Cross-tenant business relationships are prohibited unless a specifically designed, audited platform capability explicitly permits them.
9. Platform administration and support access MUST be separate from tenant membership and business roles.
10. Platform support MUST NOT use an implicit scope bypass. Any exceptional access requires a distinct grant, justification, permitted scope, expiry/revocation and audit.
11. Plan entitlement controls feature availability and usage limits; it MUST NOT grant data access or override authorization.
12. Tenant configuration MUST NOT disable isolation, authorization, audit, integrity or concurrency controls.
13. A job/event MUST carry or securely resolve the tenant identity and any required branch scope; it MUST NOT depend on ambient context left by a previous request.
14. Tenant context MUST be cleared at request/job completion and isolated between concurrent execution units.
15. Errors and lookup behavior MUST not reveal whether another tenant owns a supplied identifier.

## 4. Logical ownership model

| Data class | Ownership | Scope rule |
|---|---|---|
| Platform principal, platform role, plan catalogue, platform configuration | Platform | Platform permissions only |
| Tenant, business profile, subscription plan assignment, tenant configuration | Tenant governance | Tenant context; selected settings may be branch-overridden |
| User account | Global identity or identity authority | Does not itself grant access to tenant data |
| Tenant membership and role assignment | Tenant/platform governance as defined by the entity | Explicit tenant and optional branch/location scope |
| Customer, supplier, product catalogue, price list | Tenant | Tenant-wide unless the business rule assigns branch scope |
| Customer service address, delivery run, collection session, POS register | Tenant | Explicit branch/location/service scope where applicable |
| Stock location, lot, movement, transfer | Tenant | Tenant ownership plus validated source/destination location |
| Invoice, receipt, journal, farmer settlement | Tenant | Tenant ownership; branch attribution where domain/accounting policy requires |
| Audit event | Platform or tenant | Must identify owning security/business boundary and be query-filtered accordingly |
| Integration connection and Tally company mapping | Tenant | Tenant-specific credentials/mappings; never shared implicitly |
| Analytics/report result | Tenant | Source scope and actor permissions must be preserved in generated artifact |

The ERS remains the logical entity authority. This table does not decide physical foreign keys or storage layout.

## 5. Request processing boundary

A tenant-bound request should pass through the following logical stages:

1. Authenticate the principal and validate session/device state.
2. Resolve the requested tenant context from a trusted membership or explicitly authorized platform grant.
3. Verify tenant status and membership validity.
4. Resolve branch/location scope from server-side assignments and the requested operation.
5. Authorize action, resource, state, ownership, delegation and approval limits.
6. Execute domain operation with tenant and branch context propagated explicitly.
7. Persist business changes and audit/correlation data atomically where required.
8. Return only data permitted by the resolved scope.

The order is normative at the logical level; the exact middleware, framework and transaction implementation remain open.

## 6. Background jobs, events and integration boundaries

- Every tenant-owned job/event MUST include a stable tenant reference and source identity.
- Branch context MUST be included when the operation is branch-attributed; otherwise the job must explicitly declare tenant-wide scope.
- Consumers MUST revalidate tenant status, scope, source ownership and authorization policy appropriate to the service identity.
- Job payloads MUST NOT be treated as trusted merely because they originated internally.
- Retries MUST preserve idempotency identity and tenant scope.
- A message for one tenant MUST not be processed using another tenant's ambient context.
- Tally desktop agents, bank imports, webhooks, notification callbacks and file imports MUST be bound to a tenant-specific connection/credential and validated mapping.
- Cross-tenant batching is allowed only where the processing boundary preserves per-tenant authorization, isolation, error reporting and audit; it must never expose one tenant's payload to another.

## 7. Cache, search, files and analytics isolation

- Tenant identity MUST be part of every tenant-specific cache namespace/key and invalidation scope.
- Branch-specific cached results MUST additionally encode branch scope and relevant permission/policy version.
- Cache misses and stale entries MUST not fall back to unscoped data.
- Search indexes/documents MUST retain tenant ownership and enforce scope at query and result retrieval boundaries.
- Attachment storage references MUST be tenant-bound; unguessable identifiers are not authorization.
- Generated exports/reports MUST capture the requesting actor, tenant, branch scope, filters, data cut-off and permission decision.
- Download authorization MUST be rechecked when the artifact is accessed; a long-running job must not grant indefinite access.
- Analytics aggregates and materialized results MUST preserve tenant boundaries. Any platform-wide aggregate requires an explicitly approved privacy and access policy.

## 8. Tenant lifecycle and data boundary

Tenant onboarding, verification, activation, suspension, plan change, export, closure and deletion must be explicit stateful workflows. Suspension must define which reads, writes, background jobs, integrations and owner recovery functions remain permitted. Offboarding must account for legal retention, customer export, financial records, audit, attachments, backups and deletion/cryptographic-erasure feasibility.

These policies remain open under ODR-025 and ODR-026; no deletion or retention period is approved here.

## 9. Physical isolation alternatives to evaluate

| Option | Potential strengths | Risks/costs to evaluate |
|---|---|---|
| Shared database and shared schema with tenant ownership columns | Operational simplicity and efficient shared infrastructure | Strong query/constraint discipline; blast radius; noisy-neighbour controls |
| Shared database with schema per tenant | Additional namespace separation | Migration orchestration, connection/search-path risks, operational complexity |
| Database per tenant | Stronger data/backup/restore separation options | Provisioning, migrations, connection management, monitoring and cost at scale |
| Hybrid/tiered isolation | Can reserve stronger isolation for selected tenants or scale tiers | Multiple operating modes, migration and support complexity |

No option is selected. Evaluation MUST include isolation assurance, tenant count, workload distribution, backup/restore granularity, migration complexity, query performance, cost, operations, regulatory/contract needs, portability and future sharding.

## 10. Tenant and branch isolation verification

The architecture and test strategy MUST include:
- Identifier substitution across every resource family.
- Cross-tenant list, search, aggregate, export and attachment access.
- Cross-branch reads and writes for single-branch and multi-branch users.
- Tenant-wide records accessed by branch-scoped users under explicit policy.
- Tenant suspension during active sessions and queued jobs.
- Revoked membership/session and stale role assignments.
- Job retry, event replay and worker concurrency across tenants.
- Cache-key collision and stale-permission scenarios.
- Tally agent, webhook, bank import and notification callback tenant binding.
- Platform support access grant expiry, revocation and audit.
- Backup/restore and tenant export boundary checks.

Tests must prove both allowed and denied cases and must fail closed on missing context.

## 11. Open decisions

- **ODR-001/003:** legal/business identity and whether a tenant may contain multiple legal entities or GST registrations.
- **ODR-002:** branch and operating-location taxonomy.
- **ODR-004:** multi-tenant membership and multi-branch assignment rules.
- **ODR-005:** role, scope, delegation and approval model.
- **ODR-025/026:** tenant plan lifecycle and data lifecycle.
- **ODR-027/028:** scale, availability and recovery objectives.
- **ODR-030/031:** technology and production topology.

## 12. Exit criteria

This baseline is not approval of a physical tenancy strategy. Architecture readiness requires:
1. ODR dependencies resolved or explicitly accepted as bounded assumptions.
2. Physical isolation option evaluated against documented workload and security criteria.
3. Tenant/branch ownership rules mapped to ERS and persistence constraints.
4. Request, job, cache, search, export and integration context propagation specified.
5. Threat model and automated isolation test plan reviewed.
6. Backup, restore, support-access and offboarding boundaries validated.
