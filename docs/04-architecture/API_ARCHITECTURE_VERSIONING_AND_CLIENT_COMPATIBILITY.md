# API Architecture, Versioning and Client Compatibility

**Document ID:** ARC-API-001  
**Status:** Proposed architecture baseline — protocol and technology choices open  
**Authority:** FRS-001, ERS-001, NFR-001, Actor and Access Model, SaaS Multi-Tenancy and Data Isolation Architecture, Authentication/Authorization Architecture, and ODR-004/005/019/020/029/030/031/032.  
**Purpose:** Define API boundaries and compatibility rules for web, mobile, POS and the future Tally desktop synchronization agent.

## 1. Scope and non-goals

The platform is API-first. Web and mobile clients consume the same authoritative business capabilities. A POS experience may be a module/client profile; it does not receive broader authority merely because it is a POS client. A separately installed Tally desktop agent is a tenant-bound integration client.

Customer self-service accounts and consumer e-commerce APIs are outside the current scope. The API must not assume that the dairy owner uses only web or that all employees use only mobile.

This document does not select REST, GraphQL, RPC, a framework, API gateway, serialization library, token format or deployment topology. These are technology decisions under ODR-030/031.

## 2. API design invariants

1. Business rules and authoritative calculations execute server-side.
2. APIs expose domain use cases, not direct persistence-table CRUD as the only contract.
3. Web, mobile, POS and integration clients receive consistent validation, state-transition and authorization outcomes for equivalent operations.
4. Every protected operation authenticates its actor/service and authorizes action, resource, tenant, branch/location and state.
5. Tenant and branch scope are derived and validated server-side; client-supplied identifiers are selectors, not authority.
6. Feature entitlement, user permission and resource scope are independent checks.
7. External clients cannot set trusted audit fields, posting status, calculated balances or privileged ownership fields.
8. Financial, stock, delivery and schedule commands that can be retried MUST implement idempotency.
9. API contracts MUST distinguish synchronous completion, accepted asynchronous work, validation failure, conflict, authorization failure and unknown external outcome.
10. API responses MUST not leak cross-tenant resource existence or internal secrets.
11. API changes MUST be traceable to requirements and documented before release.
12. API design must preserve Indian business conventions already specified: INR, IST business-time defaults, Indian date/number presentation and multilingual Unicode support. No multi-currency or alternative calendar system is implied.

## 3. Logical API boundaries

Organize contracts around business capabilities and bounded contexts, including:
- Platform administration and SaaS tenant lifecycle.
- Tenant configuration, branch and operating locations.
- Identity, membership, role, permission, device and approval.
- Parties, customers, suppliers and farmers.
- Products, units, price lists and tax configuration.
- Customer subscriptions, recurrence, exceptions and generated occurrences.
- POS and sales.
- Delivery planning, runs, stops, attempts and fulfilment.
- Procurement, milk collection, quality, farmer settlement.
- Inventory, stock movements, lots, processing and traceability.
- Billing, receivables, receipts, refunds and reconciliation.
- Double-entry accounting, journals, periods, reports and audit.
- HR, attendance, leave and payroll.
- Expenses, cash and bank.
- Reporting, dashboards, budgets, forecasts and projections.
- Notifications, files, imports, exports and integration operations.
- Tally desktop-agent pairing, sync batches, mappings and reconciliation.

A single workflow may coordinate multiple domains, but each domain retains a clear owner for its authoritative rules and data. Do not duplicate posting, stock or schedule logic in separate client-specific APIs.

## 4. Contract structure

Each operation specification MUST document:
- Stable operation/requirement ID and business purpose.
- HTTP method/path or chosen protocol equivalent.
- Authentication requirement and accepted principal types.
- Required permission and tenant/branch/resource scope.
- Request schema, field meaning, nullability, precision and validation.
- Server-derived versus client-supplied fields.
- Response schema and resource identifiers.
- State transition and side effects.
- Idempotency behavior and retention/replay policy.
- Concurrency precondition and conflict behavior.
- Transaction boundary and asynchronous side effects.
- Error codes, retryability and safe remediation.
- Audit events and correlation identifiers.
- Pagination, sorting, filtering and export restrictions.
- Rate/usage limits and plan-entitlement behavior, if applicable.
- Compatibility and deprecation notes.
- Acceptance tests and traceability links.

## 4.1 Contract description and extension surfaces

HTTP API contracts SHOULD be maintained contract-first using the OpenAPI Specification, with schema validation, generated/documented consumer views where appropriate and automated compatibility checks. Select a specification/tooling version supported by the final stack; OpenAPI 3.2.1 was current on 2026-11-19. This does not select REST versus GraphQL/RPC or an API gateway.

Extensions and partner integrations MUST use published, versioned APIs, events, webhooks or import/export adapters. They MUST NOT write directly to another module's private persistence. Each extension has a separate Tenant-bound identity, explicit permissions/scope, rate/resource limits and revocation lifecycle. Configuration and extension contracts are versioned independently from API and internal database versions.

## 5. Versioning and compatibility

Use an explicit, documented public contract versioning strategy. Whether versioning is path-, header- or media-type-based remains a technology/ADR decision.

Rules:
- Breaking changes MUST use a new major API contract version or an explicitly approved equivalent migration mechanism.
- Additive changes are not automatically safe: enum expansion, stricter validation, changed defaults, changed ordering, precision changes and altered authorization can break clients.
- A released contract version MUST retain its documented semantics during its support window.
- Deprecation MUST include affected clients/tenants, replacement contract, migration guidance, sunset date and telemetry-based adoption evidence.
- Do not remove a field, enum value or endpoint while supported clients may still depend on it.
- Mobile applications may remain installed on older versions; backend compatibility policy must explicitly address this.
- Internal domain events and Tally adapter schemas have independent versioning; do not conflate them with public API version.
- API documentation and generated client artifacts, if used, must be produced from the reviewed contract source.

Support windows and the number of simultaneously supported major versions remain open for decision; do not invent them.

## 6. Authentication, authorization and tenant context

- Resolve the principal and session/device state before protected business operations.
- Resolve tenant membership or explicit support grant server-side.
- Resolve branch/location scope from trusted assignments and the requested use case.
- Apply authorization to list, detail, search, mutation, bulk, report, export, download and asynchronous job endpoints.
- Do not fetch a global resource and rely on UI filtering.
- A platform operator does not gain tenant data access by virtue of a platform role.
- A service/integration client has a named identity and bounded capabilities.
- Client type, app version, device identifier or possession of a tenant ID is not authorization.

See ARC-TEN-001 and ARC-IAM-001 for full logical rules.

## 7. Command, query and response semantics

Separate intent to change state from read/query behavior at the contract level, even if the implementation uses one application service:
- **Commands** request a business action and return the resulting resource state or an accepted operation reference.
- **Queries** return permission-filtered views and must not cause material business side effects.
- **Long-running operations** return an operation/job reference, status endpoint and completion/error summary.
- **Reports/exports** capture filters, scope, data cut-off, generation time and definition/version metadata.

Avoid exposing internal database IDs as the only stable public identifier. Use stable opaque resource identifiers with tenant-safe lookup semantics. External identifiers (Tally GUIDs, voucher numbers, bank references, provider IDs) remain separately namespaced and mapped.

## 8. Idempotency and concurrency contracts

For retryable commands:
- Accept a client-generated idempotency key or equivalent stable operation identity.
- Scope the key to authenticated principal/service, tenant and operation semantics.
- Persist the request fingerprint and final/accepted outcome.
- Repeating the same key and same semantic payload returns the prior outcome without repeating side effects.
- Reusing a key with a different semantic payload returns a conflict.
- Define behavior for in-progress, timed-out and unknown outcomes.
- Do not rely on a pre-insert lookup alone; enforce uniqueness atomically at persistence level.

For mutable resources, use a documented optimistic concurrency precondition (for example resource version/ETag) or another explicit conflict mechanism. Stale updates must not silently overwrite newer changes. See ARC-CONC-001.

## 9. Error model

Every error response should contain a stable machine-readable code, safe human-readable message, correlation/reference ID and structured field details where appropriate.

The contract must distinguish:
- Invalid request/field validation.
- Unauthenticated and unauthorized.
- Resource not found within authorized scope.
- Conflict/stale version/duplicate semantic action.
- Invalid state transition.
- Entitlement or usage-limit restriction.
- Rate limiting and retry-after behavior.
- Accepted asynchronous operation still processing.
- External provider unavailable.
- External outcome unknown and awaiting reconciliation.
- Internal failure with safe retry classification.

Do not return stack traces, SQL errors, secret values or cross-tenant existence details. Localized messages may vary; machine-readable codes and semantics must remain stable.

## 10. Pagination, filtering and bulk operations

- List APIs MUST have bounded pagination and deterministic ordering.
- Filters/sorts MUST be allow-listed and permission-aware.
- Pagination tokens, if used, MUST be bound to tenant, scope, filter and sort context; they must not grant access after permission revocation.
- Large exports and bulk mutations MUST use explicit asynchronous jobs with limits, progress, cancellation policy, result files and per-record errors.
- Bulk operations MUST define all-or-nothing versus partial-success semantics.
- Financial posting, stock movement and synchronization batches MUST expose their exact atomicity and recovery behavior.
- Never silently truncate a report or export; disclose limits and provide a continuation or asynchronous alternative.

## 11. Localization and time

- API machine values and identifiers MUST be language-neutral and stable.
- Human-readable labels/messages MUST be localizable; clients may request a supported language.
- Unicode names and addresses MUST round-trip without normalization loss that changes identity.
- Use explicit machine date/time representations and document whether a value is an instant, business date, local time or recurrence rule.
- IST is the default business timezone; branch-specific timezone support is not assumed for the India-only initial product unless later approved.
- Display formatting must not be mistaken for stored value semantics.
- Quantity, INR amount and tax precision are governed by domain rules, not floating-point client calculations.

## 12. API security and abuse controls

Define per-principal, per-tenant and per-operation limits where appropriate. Rate limiting must not allow one tenant to exhaust shared capacity or bypass plan limits. Sensitive actions may require step-up authentication or approval according to policy.

Validate payload size, file type/content, import schema, query complexity, export scope and webhook signatures. Protect against replay, injection, enumeration, mass assignment and object-level authorization failures. Secrets must never appear in URLs or ordinary logs.

## 13. API contract test requirements

- Contract tests for all supported client versions.
- Tenant and branch isolation for every resource family.
- Permission matrix tests across read/write/approve/post/export.
- Idempotency replay and key-payload mismatch tests.
- Concurrent stale-version and duplicate-command tests.
- Pagination determinism and scope-bound cursor tests.
- Async job status, cancellation and partial-result tests.
- Provider timeout/unknown-outcome tests.
- Localization, Unicode, precision and IST boundary tests.
- Backward compatibility tests for installed mobile versions.
- Tally agent pairing, revocation, batch and schema compatibility tests.

## 14. Open decisions

- ODR-004: membership cardinality and assignment lifecycle.
- ODR-005: exhaustive atomic permission catalogue across all modules, remaining approval and scope policies; permission-first cross-Branch baseline is approved under ODR-002 section 16.7.
- ODR-019/020: app packaging and offline behavior.
- ODR-029: integration priority and partner contracts.
- ODR-030: API protocol, framework, gateway and tooling.
- ODR-031: production topology and network/security boundaries.
- ODR-032: supported languages and translation workflow.

## 15. Exit criteria

Before API implementation, approve protocol/versioning strategy, authentication/session contract, permission mapping, error catalogue, idempotency policy, concurrency preconditions, pagination/bulk semantics, localization/time rules, mobile compatibility window and API test strategy. Each operation must map to FRS requirement IDs and domain ownership.


## 16. Configuration and extension compatibility

Configuration keys MUST have stable identifiers, declared types/scopes/defaults and compatibility metadata. Renaming or retiring a key requires an explicit migration/alias, diagnostics, effective-date handling and rollback path; a platform update MUST NOT silently reinterpret a Tenant's saved value. Historical transactions retain the configuration/rule version needed to explain their outcome.

API, event, extension and configuration contract versions are independent. A compatibility matrix must state which client/extension/configuration versions are supported by each platform release. Deprecation requires affected-Tenant/consumer discovery, replacement, migration instructions, telemetry, notice and a controlled sunset. A new extension may require a newer platform contract, but installation must be denied safely when compatibility or required permission is missing.