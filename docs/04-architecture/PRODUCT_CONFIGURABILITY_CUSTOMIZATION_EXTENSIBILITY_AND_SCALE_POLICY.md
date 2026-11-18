# Product Configurability, Customization, Extensibility and Scale Policy

**Document ID:** ARC-PV-001  
**Status:** Product direction approved — 2026-11-19; technology-specific implementation remains subject to evidence and ADR review  
**Authority:** Project Vision and Scope; Domain Landscape and Capability Model; Module Dependency Map; Business Rules Catalogue; SaaS Multi-Tenancy and Data Isolation Architecture; System Architecture Overview; Scalability Architecture; API Architecture; Security Architecture; Open Decisions Register.  
**Purpose:** Define one coherent product policy for stable invariants, customer configuration, supported customization, safe extensibility and growth from a very small dairy business to a large multi-branch enterprise.

## 1. Product direction

The product is one comprehensive Dairy ERP SaaS platform, not separate software products for small, medium and large dairies. It must support owner-operated businesses, local vendors, retail and doorstep distribution, farmer procurement, collection centres, chilling/storage, processing/packaging, wholesale/distribution and combined operating models. One Tenant may enable multiple capabilities and operate multiple Branches and OperatingLocations.

The platform must scale down as well as up:
- A small business can start with only the capabilities it needs, simple guided setup, a small number of users/locations and proportionate operating cost.
- A growing business can activate additional capabilities, introduce branches and controls, configure workflows and migrate historical data without replacing the product.
- A large business can operate multiple business units, Branches, sites, stock locations, teams, high-volume workflows, centralized controls, advanced reporting and selected stronger infrastructure isolation.
- All sizes use the same governed product model, canonical business semantics, security invariants and compatible release line. Customer-specific source-code forks and permanently divergent product versions are not the normal SaaS customization mechanism.

**Product promise:** one product; composable capabilities; customer-configurable policies; governed extensions; consistent security and data integrity; evidence-led scaling.

## 2. Classification rule: fixed, configurable, extensible or customizable

Every material business or technical rule MUST be classified before it is designed or changed.

| Classification | Meaning | Change authority |
|---|---|---|
| Fixed invariant | A condition required for security, correctness, audit, legal integrity or safe operation | Platform/product governance through a versioned change with impact analysis; never tenant-overridable |
| Configurable policy | A supported variable value or workflow choice within a defined safe range | Authorized Tenant configuration, optionally narrowed by Branch/site where that setting explicitly permits it |
| Extensible capability | A new, separately governed capability connected through stable contracts | Product/platform extension governance and Tenant enablement/permission |
| Customizable presentation | A bounded change to labels, layouts, templates, forms, dashboards and reports without changing canonical business meaning | Tenant users with the relevant configuration permission |
| External/legal dependency | A value or decision dependent on current law, contract, provider capability or named human accountability | Verified authoritative evidence and/or the explicitly responsible human; do not invent or encode a guess as fact |

A setting MUST NOT be labelled configurable merely because a developer can technically expose it. Its allowed values, scope, owner, effective date, validation, audit impact, compatibility and non-overridable boundaries must be defined.

## 3. Fixed platform invariants

The following remain fixed across every Tenant, plan, Branch, module, extension and deployment tier:

1. Tenant isolation and the prohibition on accidental cross-tenant access.
2. Server-side authentication, permission-first authorization, resource ownership and scope validation.
3. Explicit permission for each data disclosure/action; default deny; no implicit access from job title, role title or account type.
4. Auditability and preservation of material business, financial, stock and security events.
5. Transaction integrity, concurrency safety, idempotency and controlled retry/reconciliation for material operations.
6. Double-entry accounting integrity, balanced posted journals and controlled reversals; a posted financial record is not silently overwritten.
7. Stock ownership, traceability, lot/batch identity, quantity integrity and controlled stock movements.
8. Separation of duties, maker-checker and non-delegable actions where the governing policy requires them.
9. Immutable historical transaction facts and reproducible snapshots where later master/configuration changes must not rewrite past outcomes.
10. Secure secrets, tenant-bound integrations, protected file access and controlled export/deletion.
11. Resource/fairness limits and abuse protection sufficient to protect other Tenants.
12. Fixed legal or contractual constraints once their applicability is verified for the relevant Tenant/activity.

Plans, Tenant settings, custom workflows, extensions and administrator actions MUST NOT disable or bypass these invariants.

## 4. Capability composition and product tiers

A business capability has separate lifecycle dimensions that must not be conflated:

- **Product existence:** the platform supports the capability.
- **Plan entitlement:** the Tenant's commercial plan permits the capability or a defined usage allowance.
- **Tenant activation:** the Tenant enables/uses the capability within its plan.
- **Operational configuration:** the Tenant defines how the enabled capability operates.
- **Authorization:** a principal has explicit permissions and scope to view or perform an operation.
- **Extension availability:** an approved extension is compatible and available for that Tenant.
- **Usage/consumption:** the Tenant consumes measurable resources subject to fair-use or contractual limits.

A plan entitlement is not a permission. Tenant activation is not permission. A role or account type is not permission. Installing an extension does not authorize its actions or grant data access.

Plans may package capability access, limits, service/support levels and infrastructure options. They MUST NOT create separate product code lines or weaken fixed invariants. The product should support capability dependencies and prerequisites so that enabling a capability does not leave the Tenant with an invalid partial configuration. Optional capabilities must not impose unnecessary setup or navigation burden on a Tenant that does not use them.

Starter templates may be provided for common dairy operating profiles (for example, retail/distribution, farmer collection/procurement, processing/packaging or combined operations). Templates are editable starting configurations, not mutually exclusive Tenant types and not permanent business-mode locks. A Tenant can combine or change operating capabilities while preserving historical records and validating dependencies.

## 5. Configuration model

### 5.1 Configuration layers and precedence

Use a governed inheritance model:

1. **Non-overridable platform invariants:** absolute constraints; no lower layer can weaken them.
2. **Platform defaults:** safe starting values for supported configuration keys.
3. **Tenant configuration:** the Tenant's selected business policies and defaults.
4. **Branch/site/location override:** allowed only for configuration keys explicitly declared to support that narrower scope.
5. **Transaction-specific exception:** allowed only through a defined workflow, permission, approval and audit trail.
6. **User preferences:** presentation/accessibility preferences such as display language, density or personal notification choices; these do not alter business authority or shared Tenant policy.

The most specific valid setting may override an inherited default only when the configuration catalogue explicitly permits that scope. A parent setting may be marked locked to prohibit lower-scope overrides. If there is a conflict with a fixed invariant, explicit deny, ownership rule or required approval, the invariant/policy wins. If precedence is ambiguous, fail safely and block publication or the affected operation.

Do not create arbitrary unlimited configuration scopes. Use only the scopes supported by the relevant domain (for example Tenant, Branch, OperatingLocation, StockLocation or register). A configuration key must declare which scopes are valid.

### 5.2 Configuration catalogue

Every configurable property or policy MUST have a documented catalogue entry containing, as applicable:

- Stable key and human-readable label/description.
- Owning domain/module and accountable platform owner.
- Type, unit, allowed values/range, default and validation rules.
- Applicable business profiles/capabilities and prerequisite settings.
- Allowed scope(s), inheritance behavior and whether lower-level override is permitted.
- Whether the value is operational, financial, security-sensitive, presentation-only or integration-related.
- Effective-date behavior, versioning and historical snapshot requirements.
- Required permission, approval, audit event and change reason.
- Impact of change on existing records, queued jobs, caches, reports, integrations and client compatibility.
- Rollback/compensation method and migration requirements.
- Whether a restart, recalculation, reprocessing or user communication is required.

No undocumented configuration key may be exposed to Tenant administrators.

### 5.3 Configuration lifecycle

Material configuration changes follow a controlled lifecycle:

**Draft → Validate → Preview/Simulate → Approve (where required) → Schedule/Publish → Effective → Superseded/Retired.**

- Validate dependencies, data ranges, effective dates, permissions and compatibility before publication.
- Preview the effect and impacted scope before high-impact changes.
- Use approvals for material financial, inventory, security, tax or workflow changes according to the relevant domain policy.
- Record actor, permission, Tenant/scope, prior/new version, reason, approval, effective timestamp and affected configuration keys.
- Preserve the configuration version/snapshot used by posted or completed transactions.
- Rollback changes future behavior; it must not silently rewrite completed history. Historical correction uses a controlled domain correction/reversal process.
- Failed publication must leave the last valid version active.
- Concurrent edits must detect stale versions and prevent silent last-write-wins loss.

### 5.4 Configuration versus master data

Configuration defines policy or system behavior (for example, price calculation rules, approval limits or delivery cut-offs). Master data defines business entities and reference facts (for example, products, customers, suppliers, rates or locations). Transaction data records what happened. These categories have different ownership, validation, lifecycle and audit rules and must not be collapsed into a generic settings store.

## 6. Supported customer customization

Customer customization should be configuration-first and upgrade-safe. Supported areas may include:

- Business identity, branding, logos, themes and print/document layouts.
- Display labels, terminology aliases and local-language text without changing canonical identifiers or accounting meanings.
- Forms, field visibility, requiredness and safe validation options for supported workflows.
- Additional metadata/custom fields with declared type, data classification, validation, scope, retention, indexing/search rules, export behavior and permissions.
- Customer-defined document templates, receipts, statements and operational print formats.
- Dashboards, saved views, filters, report layouts and scheduled report definitions.
- Supported workflow steps, approval routes, task queues and notification templates.
- Supported pricing, delivery, subscription, stock, procurement and finance policies within each domain's declared safe ranges.
- Import/export mappings and approved external integration settings.

Custom fields MUST NOT replace canonical identifiers, transaction amounts, quantities, tax facts, ledger accounts or other integrity-critical fields. Custom data must remain discoverable, permission-controlled, auditable where material, exportable and covered by retention/deletion policy.

Tenant-specific physical database schema changes, arbitrary SQL, unrestricted scripts and custom code injected into the core runtime are NOT the default customization mechanism. Where a genuine extension is required, use the governed extension interfaces in section 7.

## 7. Extensibility architecture

### 7.1 Approved extension surfaces

The platform should expose explicit, versioned extension surfaces:

- Public/partner APIs and documented integration contracts.
- Domain events and outbound webhooks for approved event subscriptions.
- Inbound adapters/connectors for accounting, banking, payments, notifications, devices and external services.
- Import/export adapters and versioned mapping profiles.
- Custom reports/dashboards through a permission-aware query/report contract.
- Custom workflow actions through a bounded, declared action contract.
- UI integration surfaces for approved navigation, panels and forms.
- Metadata-defined custom fields and extension-owned data namespaces where justified.
- Platform-operated modules/capabilities that follow the same security and lifecycle standards.

### 7.2 Extension safety

- Extensions MUST use published APIs/events/contracts and MUST NOT write directly to another module's private persistence or bypass domain validation.
- An extension receives its own explicit identity, Tenant binding, permissions, resource scope, secrets and rate/usage limits.
- Installing/enabling an extension does not give it unrestricted access to all Tenant data.
- Webhook delivery must be authenticated, signed where applicable, replay-resistant, tenant-bound, retryable and auditable.
- Event consumers must be idempotent and version-aware; event delivery is not assumed to be exactly once.
- External calls must use timeouts, bounded retries, circuit breaking or equivalent failure containment, and explicit unknown-outcome reconciliation where a provider may have accepted a request.
- Extensions must not block critical transaction commits on unnecessary synchronous external dependencies.
- Arbitrary customer-supplied code MUST NOT execute with core application/database privileges. Any later sandboxed runtime requires a separate threat model, resource limits, review, signing, isolation and explicit product decision.
- Extension failure must not corrupt authoritative source records or disable unrelated core capabilities.

### 7.3 Extension catalogue and lifecycle

Each extension must declare stable identifier, publisher/owner, purpose, compatible platform/API/event versions, required permissions, data accessed, scopes, secrets, events consumed/published, dependencies, resource limits, support/maintenance status, migration and deprecation policy.

Lifecycle: **Proposed → Reviewed → Approved/Published → Tenant-enabled → Configured → Active → Suspended/Revoked → Retired.**

Tenant installation and enablement require a current compatible version, explicit Tenant authorization/consent as applicable, declared permissions and audit. Revocation must stop future calls and data access without erasing already completed business history. Extensions must have a tested upgrade, rollback and data-export path.

## 8. Architecture and module boundaries

### 8.1 Initial architecture baseline

Use a **modular monolith** as the initial product architecture, with clear domain/bounded-context boundaries, explicit module ownership, private internal data contracts and shared platform capabilities. This is an architectural choice, not a commitment to one deployable process forever.

The initial module decomposition must favor cohesive business capabilities and clear ownership over maximum module count. Modules may share a deployment initially, but they must not share undocumented persistence ownership or call each other's private internals.

### 8.2 Extraction criteria

Extract a module/service only when measurable evidence shows a material benefit in one or more dimensions:
- Independently different scaling/throughput or availability profile.
- A strong security, compliance or tenant-isolation boundary.
- Independent release cadence or operational ownership.
- Clear bounded context and stable contract with manageable data ownership.
- Fault isolation or workload scheduling benefit that outweighs distributed transaction, latency, observability, deployment and operational complexity.

Every extraction requires an ADR, workload evidence, dependency map, data ownership plan, migration/rollback, versioned API/event contract, authorization propagation, observability, DR and operational ownership. Do not split a tightly coupled workflow merely to follow a microservices trend.

### 8.3 Dependency discipline

- A module owns its business rules and authoritative writes.
- Other modules use published application contracts, commands/queries or domain events rather than direct table mutation.
- Shared foundational capabilities (identity, authorization, audit, configuration, files, notifications, integration, reporting and tenant context) have explicit ownership and stable interfaces.
- Avoid circular dependencies. A dependency change must update the module map and traceability.
- A cross-module business transaction must explicitly state its atomic boundary, outbox/compensation/reconciliation behavior and failure modes.

## 9. Scale from small to enterprise

### 9.1 Scale dimensions

Scale must be modelled across Tenant count, Tenant workload distribution, Branches, OperatingLocations, users/devices, transaction throughput, peak concurrency, data growth, files, integrations, scheduled work, reporting, retention and geographic/site distribution. "Enterprise" is not a single numeric profile; workload and isolation requirements must be measured.

### 9.2 Scaling path

- Keep web/API request processes stateless where practical so application capacity can scale horizontally.
- Scale API, background workers, report/analytics workloads and object storage independently where their demand profiles differ.
- Use bounded asynchronous processing for long-running jobs; isolate queues/work pools by workload class and apply tenant-aware fairness/quotas.
- Protect transaction databases from analytics, bulk imports, exports and noisy-neighbour workloads.
- Measure per-Tenant consumption, latency, queue age, storage growth, errors, cost and resource contention.
- Use capacity tests and production telemetry to trigger scale-up, scale-out, partitioning, read scaling or service extraction.
- Keep a migration path for data partitioning and tenant placement; do not make a later move from shared resources to selected isolated resources require a customer-specific code fork.
- Preserve workload headroom and safe overload behavior for financial, procurement, stock and delivery critical paths.

### 9.3 Tenant isolation tiers

Adopt one shared SaaS control plane, onboarding flow, product governance and release process. The data/compute footprint may use a **pool, bridge or silo** strategy according to justified isolation, workload, contractual, operational and commercial needs.

Recommended direction:
- Pooled shared infrastructure is the cost-efficient standard candidate for ordinary workloads, with mandatory application/database-enforced Tenant isolation and noisy-neighbour controls.
- Bridge/hybrid isolation may place selected tenants or high-load components on separately partitioned resources.
- Silo isolation may be offered where verified contractual, regulatory, security, restore, performance or scale requirements justify the added cost and operations.
- Isolation tier is a deployment/data-placement decision; it does not create a different product version or weaker/stronger permission semantics.
- Tenant placement/migration between tiers must preserve identifiers, audit, encryption/secrets, backup/restore, integration, API compatibility and data residency/retention constraints.

Exact database topology, provider, partition/shard key, resource sizes and isolation-tier eligibility remain subject to ARC-TEN-001, ARC-SCALE-001, ARC-DEP-001, technology PoCs and ODR-027/030/031; this policy does not prematurely select physical infrastructure.

### 9.4 Cost and plan fairness

Plans may define capability entitlements, usage quotas, performance/support tiers and dedicated-resource options. The platform must measure actual consumption and protect shared capacity. A Tenant's plan must not silently change transaction semantics, data ownership, authorization or audit. Quota exhaustion must return a clear, recoverable response; it must not silently drop accepted financial/stock events.

## 10. API, event and configuration compatibility

- APIs and event contracts are stable public boundaries and are versioned independently from internal implementation and database schema.
- Use contract-first descriptions, automated validation and compatibility tests. OpenAPI is the recommended HTTP API description standard; select a specification/tooling version supported by the chosen toolchain (OpenAPI 3.2.1 was current on 2026-11-19).
- Breaking public API changes require a new major contract version or a governed equivalent migration. Additive changes still require compatibility analysis for strict/mobile clients.
- Domain event schemas require stable event identity, source, Tenant context, schema/type version, occurrence time and correlation/causation metadata as applicable.
- Configuration keys and extension contracts are versioned. Renamed/removed keys require a migration/alias strategy, diagnostics and safe fallback; no silent reinterpretation of existing customer settings.
- Client, API, event, extension and configuration versions must have an explicit compatibility matrix and support/deprecation process.
- Deprecation requires affected-Tenant/client discovery, replacement, migration guide, telemetry, notice and a sunset decision. Do not remove a contract solely because a newer application version exists.

## 11. Feature flags, entitlement and authorization are different

- **Release/operational feature flag:** controls staged rollout, experimentation or emergency disablement; must not be the permanent source of business permission.
- **Plan entitlement:** controls commercial feature availability and usage allowance.
- **Tenant capability activation:** records whether the Tenant has enabled a supported capability.
- **Permission:** controls what an authenticated principal may view or do, within scope.
- **Configuration:** defines how an available and authorized capability behaves.
- **Extension grant:** controls which approved integration/extension may access its declared contract.

These controls must be evaluated separately and their decisions must be auditable where material. A feature flag, entitlement, configuration value or installed extension MUST NOT authorize a business action.

## 12. Non-goals and prohibited shortcuts

The following are not acceptable substitutes for governed product variability:
- A separate source-code fork or independently maintained application for each customer.
- Arbitrary tenant SQL/DDL or direct writes into another module's private data.
- Role-title, account-type or UI-only authorization.
- Treating a plan feature, toggle or installation as a permission.
- Generic untyped custom fields for financial, quantity, tax or stock-critical facts.
- Unbounded user scripts, plug-ins or formulas running with core privileges.
- Silent historical recalculation after a configuration or algorithm change.
- Splitting modules into microservices without a measurable business/operational benefit.
- Hard-coded limits or a development VPS constraint that prevents future scale without an explicit product decision.

## 13. Required verification

The design and future implementation test strategy must include:
1. Small and large Tenant profiles use the same product semantics and compatible release line.
2. A small Tenant can activate only required capabilities without invalid dependencies or exposure to unrelated complexity.
3. A Tenant can grow branches, locations, users and enabled capabilities without data migration that changes historical meaning.
4. A Branch override changes only configuration keys explicitly declared branch-overridable.
5. Locked Tenant policy cannot be bypassed by a Branch/site override.
6. Invalid configuration cannot be published; failed publication leaves the prior valid version active.
7. Past posted/completed transactions retain the policy/configuration snapshots used when they occurred.
8. Permission, plan entitlement, activation, feature flags and extension installation are independently tested.
9. An extension cannot access data or actions outside its declared permissions, scope and Tenant.
10. Extension failure, timeout, replay, duplicate delivery and incompatible version do not corrupt core transactions.
11. Module boundaries prevent unauthorized direct writes to another module's private data.
12. Load tests demonstrate tenant fairness and critical-path protection under noisy-neighbour, queue saturation and peak workload.
13. Tenant migration between supported isolation tiers preserves data, identifiers, permissions, audit, integrations and recovery evidence.
14. API/event/configuration compatibility tests detect breaking changes and unsafe migrations.
15. All customer customizations, configuration changes and extension lifecycle actions are auditable.

## 14. Governance, ownership and traceability

- This document is authoritative for cross-product classification of fixed, configurable, customizable and extensible behavior.
- Domain documents remain authoritative for the exact settings, permissions, limits, state transitions and invariants in their own domain.
- The Open Decisions Register remains authoritative for unresolved commercial, legal, external-evidence and named-accountability dependencies.
- The Configuration Catalogue, Extension Catalogue, Module Dependency Map, API/event schemas and Requirements Traceability Matrix must cross-reference each other as they are completed.
- Any new module, configuration key, customization surface or extension point must identify domain owner, permission requirements, data classification, compatibility, audit, operational impact, support model and exit/deprecation path.
- The product owner may later change a product/business commitment; such change must use formal change control and must not be silently inferred from an engineering convenience.

## 15. Research basis and reference sources

The following public references informed this policy. They are architecture/security guidance, not vendor endorsements or automatic product requirements:

1. NIST SP 800-162, *Guide to Attribute Based Access Control (ABAC) Definition and Considerations*: authorization can evaluate subject, object/resource, operation and environment attributes against defined policy. https://csrc.nist.gov/pubs/sp/800/162/upd2/final
2. OWASP Authorization Cheat Sheet: least privilege, deny-by-default and permission validation on every request. https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html
3. AWS Well-Architected SaaS Lens, *Core Isolation Concepts*, *Silo, Pool, and Bridge Models*, and *General Design Principles*: tenant isolation and infrastructure shape can vary by workload, compliance and operating needs while preserving unified SaaS onboarding/operations. https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/core-isolation-concepts.html
4. Microsoft Learn, *Architectural Principles* and *Microservices Architecture Style*: bounded contexts help manage domain complexity; service boundaries should be business-cohesive and not excessively granular. https://learn.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles and https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/microservices
5. OpenAPI Specification 3.2.1, published 10 September 2026: a language-neutral contract description for HTTP APIs. https://spec.openapis.org/oas/v3.2
6. OpenFeature specification: vendor-neutral and extensible feature-flag evaluation interface; feature flags remain a rollout/configuration mechanism, not an authorization substitute. https://github.com/open-feature/spec

**Evidence review date:** 2026-11-19.
