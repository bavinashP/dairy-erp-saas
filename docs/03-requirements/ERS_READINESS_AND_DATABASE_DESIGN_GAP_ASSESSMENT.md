# ERS Readiness and Database Design Gap Assessment

**Document ID:** DBR-001  
**Assessment date:** 2026-11-17  
**Status:** Evidence-based readiness assessment — physical schema generation remains gated  
**Authority:** ERS-001; FRS-001; RTM-001; NFR-001; Business Rules Catalogue; Open Decisions Register; Core Domain Entity Catalogue; Canonical Business Glossary; Module Dependency Map; relevant accounting, inventory, procurement, subscription, delivery and Tally specifications; ARC-TEN-001; ARC-CONC-001; ARC-EVT-001; ARC-SEC-001; ARC-OBS-001; ARC-SCALE-001.  
**Purpose:** Determine whether the existing logical Entity Relationship Specification is sufficiently resolved to proceed to physical database schema/ERD design, identify precise gaps, and define safe next actions without inventing business, legal or accounting policy.

## 1. Executive assessment

**Conclusion: ERS-001 is a substantial logical model, but the physical database design gate is not yet satisfied.**

The repository already contains a broad conceptual entity catalogue and an ERS covering platform/tenant, identity, parties, products, subscriptions, delivery, procurement, quality, processing, inventory, finance, HR, compliance, audit, files, analytics and Tally mappings. It also defines important ownership, immutability, tenant-isolation, financial-balance, stock-ledger, temporal and idempotency invariants.

However:

- ERS-001 explicitly states that it is not a physical schema.
- Its own ERD-readiness checklist remains unchecked.
- Several dependencies are genuine business, accounting, legal or owner-approval decisions.
- RTM-001 reports requirement-to-entity mapping only at capability level; detailed entity coverage remains to be checked.
- Acceptance test identifiers are proposed, but executable scenarios and expected results remain to be authored.
- NFR workload dimensions exist, but numeric workload inputs and pilot evidence are not available for index/partition/capacity decisions.
- Tally mapping is a proposed contract and requires a version-specific proof of concept.

Therefore, do not generate production SQL/DDL, ORM models, migrations or a final physical ERD from ERS-001 at this stage. Continue with an evidence-backed logical-to-physical design assessment and close or explicitly bound the dependencies first.

## 2. Existing evidence reviewed

The following authoritative documents were inspected for this assessment:

### Core model and requirements
- `docs/03-requirements/ENTITY_RELATIONSHIP_SPECIFICATION.md` (ERS-001)
- `docs/03-requirements/FUNCTIONAL_REQUIREMENTS_SPECIFICATION.md` (FRS-001)
- `docs/03-requirements/REQUIREMENTS_TRACEABILITY_MATRIX.md` (RTM-001)
- `docs/03-requirements/NON_FUNCTIONAL_REQUIREMENTS_SPECIFICATION.md` (NFR-001)
- `docs/03-requirements/BUSINESS_RULES_CATALOGUE.md` (BRC-001)
- `docs/02-domain/CORE_DOMAIN_ENTITY_CATALOGUE.md`
- `docs/02-domain/CANONICAL_BUSINESS_GLOSSARY.md`
- `docs/02-domain/MODULE_DEPENDENCY_MAP.md`

### Critical transaction and integration domains
- Accounting Double-Entry and Financial Controls
- Inventory, Stock Ledger and Reconciliation
- Subscription Lifecycle and Recurrence Rules
- Delivery Lifecycle and Exception Rules
- Tally Desktop Synchronization and Data Exchange Architecture
- Tally Field and Voucher Mapping Specification
- ARC-TEN-001, ARC-CONC-001, ARC-EVT-001, ARC-SEC-001, ARC-OBS-001 and ARC-SCALE-001

This is a focused readiness assessment, not a line-by-line audit of every domain document or a legal/accounting opinion.

## 3. ERD-readiness checklist assessment

| ERS-001 readiness item | Assessment | Evidence and remaining work |
|---|---|---|
| Legal entity/books and branch/location alternatives | **Core operating taxonomy resolved; legal/book cardinality deferred** | ODR-001 operating-model and core ODR-002 Branch/OperatingLocation/ServiceArea/Route/shared-site stock boundaries are resolved. ODR-003 legal entity, tax registration and AccountingBook cardinality remains explicitly deferred for post-project legal/accounting review. Do not treat the legal aspect as a current development blocker or infer legal cardinality. |
| Canonical Party/Customer/Supplier/Farmer identity | **Partial — scenario validation required** | Party plus PartyRole, CustomerAccount, SupplierAccount and FarmerProfile is a sound proposed shared identity model. Validate one party acting as customer and supplier, joint/family farmer accounts, multiple contacts/addresses, duplicate detection and merge/audit scenarios. |
| Cardinality and optionality for high-volume workflows | **Partial — workflow test cases required** | ERS contains a principal relationship spine. Validate subscription generation, multiple fulfilments, partial delivery/returns, collection-to-settlement allocation, batch consumption/output, billing and receipt allocation with worked cases. |
| UOM, quantity precision, conversion and monetary rounding | **Blocked for final physical precision — policy/evidence required** | ERS requires unit and precision context and exact decimal money; accounting baseline fixes INR but does not settle all quantity precision, conversion and rounding policies. ODR-008 remains unresolved. Define per-measure precision and rounding before final column definitions. |
| Stock movement and accounting posting source-of-truth contracts | **Partial — costing/posting policies required** | Stock movement and posted double-entry ledger are authoritative; operational source events create posting instructions. Inventory valuation, negative-stock policy, costing method and complete versioned posting rules require closure or bounded profiles. |
| Lifecycle/state machine and immutable snapshot boundaries | **Partial — cross-document reconciliation required** | Subscription schedule versions, immutable posted journals/stock movements and source snapshots are specified. Reconcile all FRS state transitions, reversals, corrections, close/reopen, approval invalidation and effective-date rules into one entity lifecycle matrix. |
| Tenant/branch foreign-reference and row-scope enforcement | **Logical rules defined; physical enforcement not designed or tested** | ARC-TEN-001 and ERS define fail-closed scope and same-tenant ownership. Design composite ownership constraints/tenant-safe references and prove list, lookup, join, background job, export and restore isolation in the selected database PoC. |
| Uniqueness, concurrency and idempotency keys | **Partial — per-workflow key catalogue required** | ERS has candidate keys; ARC-CONC-001 has hazards and PoC tests. Confirm deterministic keys and constraint behavior for all critical workflows, including billing, stock, settlement, posting, receipts, imports, sync batches and outbox/inbox. |
| Privacy classification, retention and audit | **Partial — retention/legal dependencies remain** | Field-level access, audit and attachment inheritance are required. ODR-026 and ODR-033/034 remain unresolved. Complete data classification, purpose, retention, legal hold, deletion/anonymization and audit access policy with current legal evidence. |
| Tally mapping proof of concept | **Contract proposed; evidence not yet demonstrated** | Mapping specification includes payload, direction, idempotency and test fixtures. Validate target TallyPrime releases, XML/JSON/TDL capabilities, voucher/stock detail and read-back reconciliation with disposable company data. |
| FRS-to-entity-to-test traceability | **Capability-level mapping only** | RTM-001 explicitly says entity mapping is capability-level and detailed coverage must be checked. Acceptance IDs exist but executable tests and expected results remain to be authored. Create entity/constraint/FRS/test traceability at individual requirement level. |
| NFR workload before indexes/partitioning | **Partial — no production sizing evidence** | ARC-SCALE-001 defines workload dimensions and stages. ODR-027 requires owner workload inputs and pilot telemetry. Do not invent indexes, partitioning, sharding or capacity limits without representative workload and chosen-engine evidence. |

## 4. Safe design baselines that can be carried forward

These are recommended technical design guardrails supported by the current documents. They do not silently approve unresolved business/legal policies.

### 4.1 Tenant, branch, location and accounting books

- Tenant remains the primary SaaS business-data isolation boundary.
- Branch remains an operational unit and is not automatically a legal entity, GST registration, warehouse or outlet.
- OperatingLocation remains a typed location concept; its permitted types and hierarchy require ODR-002 closure.
- AccountingEntity/books scope remains separate from Tenant and Branch.
- Recommended modelling direction: support an explicit AccountingEntity reference for accounting-owned records, with a simple single-books tenant profile as the initial default. Multiple books/legal entities must remain disabled until ODR-003 is approved and tax/accounting applicability is validated.
- Never infer accounting ownership from branch ID.

### 4.2 Identity and party records

- Keep UserAccount, Person, TenantMembership, MembershipBranchAssignment, PersonnelRecord, PermissionAssignment, optional PermissionBundle/Role and ServiceIdentity distinct. Branch assignment is workforce context, not access authority.
- Keep Party as the tenant-owned business identity root with role-specific customer/supplier/farmer extensions.
- Customer records do not receive login credentials or self-service access under the current product boundary.
- Any cross-tenant person identity matching must be designed as a privacy-controlled identity concern, not an implicit cross-tenant Party relationship.

### 4.3 Ledger-first operational history

- Stock balance is derived from stock movements, reservations and status rules; posted movements are corrected by reversal/adjustment, not edited in place.
- Receivable/payable balances are derived from posted source documents, allocations and payment events.
- Posted journals are immutable and balanced; corrections are linked reversals/adjustments.
- Operational source event, financial posting, external settlement evidence and reconciliation are separate facts.
- Analytics, cache, mobile snapshots and Tally exports are not operational sources of truth.

### 4.4 Historical and effective-dated facts

- Version price/rate, tax classification, UOM conversion, recurrence, posting rule, compensation and statutory rule data where changes affect future calculations.
- Preserve the version or immutable snapshot used by a finalized transaction.
- Define the business timestamp/date used to select each effective-dated version; do not rely on server-local time.

### 4.5 Concurrency and duplicate prevention

- Every critical command needs a stable domain uniqueness key in addition to request idempotency.
- Use database constraints/atomic conditional updates/locking or equivalent proven concurrency mechanisms; a pre-check alone is insufficient.
- Define transaction boundaries and durable outbox/inbox behavior for business effects that trigger asynchronous work.
- Model external provider unknown outcomes as reconciliation-required, not automatic success or blind retry.

## 5. Decision dependency register for database design

| Dependency | Current classification | Required action before final physical design |
|---|---|---|
| ODR-001 — business/legal identity | Owner/business decision + evidence | Define target legal structures and profile requirements; do not encode tax assumptions in Tenant/Branch. |
| ODR-002 — Branch/location taxonomy and shared-site controls | Core baseline resolved; custody/accountability detail remains | Preserve Branch/OperatingLocation/StockLocation/ServiceArea/Route separation, grant lifecycle, permission matrix and workforce boundary. Complete remaining physical custody/accountability and exception workflows. |
| ODR-003 — tenant/legal entity/books | Owner approval + legal/accounting evidence | Confirm whether one tenant may hold multiple legal entities/GST registrations/books; retain AccountingEntity separation meanwhile. |
| ODR-004/005 — membership and authorization | ODR-004 baseline resolved; ODR-005 partial | ODR-004 multi-Tenant membership and workforce Branch assignment baseline is resolved. Permission-first customer configurability, fail-closed precedence and cross-Branch matrix are resolved; complete exact per-module permission keys and domain approval thresholds under ARC-IAM-CAT-001. |
| ODR-008 — UOM/precision | Engineering/domain evidence and business approval | Produce measurement catalogue, base-unit rules, conversion governance and worked milk/product examples. |
| ODR-011/012 — processing/inventory | Business policy + engineering recommendation | Define optional processing profiles, stock custody, batch/lot requirements, costing and negative-stock treatment. |
| ODR-014/016/017 — billing/tax/accounting | Owner/accounting approval + current legal evidence | Define billing document semantics, tax applicability, books, posting rules and period treatment. |
| ODR-018 — HR/payroll | Business scope + legal applicability | Define employee/contractor profiles and conditional statutory payroll requirements. |
| ODR-020 — offline | Recommended narrow baseline exists; policy evidence remains | Validate offline data expiry, device matrix, conflict and revocation behavior before storing local records. |
| ODR-026/033/034 — retention/security/audit governance | Owner approval + legal/security evidence | Complete data classification, retention and operational governance before final lifecycle columns/policies. |
| ODR-027/028/031 — capacity/recovery/deployment | Evidence and owner approval | Supply workload assumptions, test, approve recovery objectives and deployment risk/provider choices. |
| ODR-029/030 — integration/technology | Owner priority and PoC evidence | Confirm priority integrations and selected database/queue/stack only after repeatable tests. |
| Tally mapping depth | Evidence required + owner accounting preference | Complete version-specific tests for detailed vs summarized vouchers, inventory, dimensions, imports and conflict resolution. |

## 6. Required next work before physical schema generation

1. Build a decision-neutral domain model for Tenant, Branch, OperatingLocation and AccountingEntity, including explicit cardinality alternatives and the consequences of each.
2. Validate Party/Customer/Supplier/Farmer identity with worked scenarios and deduplication/merge controls.
3. Create a quantity/UOM and money precision policy proposal with examples for loose milk, packaged milk, weight-based products, fractional units, discounts, GST components and rounding. Keep statutory tax rates separate from technical numeric precision.
4. Map every high-volume workflow from source command to state transition, stock movement, receivable/payable event, journal, outbox and reversal.
5. Create a candidate business-key/idempotency/unique-constraint matrix and concurrency acceptance cases.
6. Reconcile ERS lifecycle/snapshot definitions with the State Transition Matrix and Exception/Reversal/Recovery Matrix.
7. Expand RTM-001 to entity/relationship/constraint/test coverage for every FRS ID.
8. Complete field classification and lifecycle/retention dependencies with ARC-SEC-001 and ARC-OBS-001.
9. Complete the Tally proof-of-concept fixtures and document target release compatibility.
10. Revisit physical database engine selection only after ADR-TECH-001 PoC and team/operational review. PostgreSQL 18 remains a preferred PoC candidate, not an approved production selection.
11. Only after the above, prepare the physical ERD and engine-specific schema proposal, including constraints, indexes, transactions, partitioning candidates, migration/versioning and restore strategy.
12. Run Gate D review and obtain explicit approval for any business, legal, financial or operational policy that cannot be safely represented as a configurable alternative.

## 7. Explicit non-goals at this stage

- No application code, ORM entities, migrations, SQL DDL or production database provisioning.
- No final PostgreSQL/MySQL selection.
- No invented fixed precision for all quantity types.
- No assumption that every tenant has GST registration, multiple legal entities, processing, warehouses, employees or a payment gateway.
- No assumption that branch equals legal entity, GST registration, outlet or warehouse.
- No hard deletion of posted stock, financial, audit or finalized compliance history.
- No claim that the physical schema or database performance has been validated.

## 8. Exit criteria

The ERS readiness gate is satisfied only when:

- Each ERS checklist item is either verified complete or has a specifically bounded alternative with owner/evidence dependency and safe interim rule.
- Every critical FRS workflow maps to entities, relationships, constraints, state transitions and executable acceptance tests.
- Tenant/branch/accounting ownership and cross-reference integrity are explicit.
- Money and quantity precision/rounding policies are approved or precisely parameterized.
- Stock and accounting source-of-truth/posting contracts are complete.
- Uniqueness, concurrency, idempotency, reversal and outbox behavior are testable.
- Privacy, retention, audit and external integration lifecycles are defined.
- The selected database engine passes the relevant technology PoC and security/recovery review.
- Gate D reviewers can trace every material design decision to an authoritative source or explicit approval.

Until these criteria are met, ERS-001 remains a logical baseline and physical database schema generation remains intentionally gated.


## 9. Revalidation addendum — 2026-11-19

This addendum supersedes the older decision-status summaries above only where later decisions are explicitly recorded. The original assessment date and historical evidence are preserved.

### 9.1 Decisions now resolved or materially narrowed

- **ODR-001:** one Tenant supports multiple operating models and multiple Branches. Legal-entity cardinality remains separate under ODR-003.
- **ODR-002:** Branch/OperatingLocation, ServiceArea/Branch, Route/Branch, shared-site stock segregation, limited stock-task grants, approval, grant lifecycle, delegated approval, emergency override, expiry and Home-Branch/workforce boundary are now recorded in the ODR. The complete cross-Branch permission matrix and permission-first authorization are resolved. Detailed shared-site custody/accountability and exception workflow specifications remain open.
- **ODR-004:** one global user identity may hold independent memberships in multiple Tenants, at most one current membership per user/Tenant pair. A membership may have multiple workforce Branch assignments and one optional current Home/Primary Branch; neither assignment grants data access.
- **ODR-005:** permission-first, tenant-configurable, default-deny access and cross-Branch stock-task matrix are resolved. The exhaustive permission catalogue for all modules and domain-specific approvals remain to be completed.
- **ODR-035 / ARC-PV-001:** one product supports small-to-enterprise profiles; fixed/configurable/customizable/extensible classifications, modular-monolith initial baseline, governed extension points, tenant variability and scale evolution are established.

### 9.2 Remaining physical-design blockers and non-blockers

**Genuine physical-design dependencies:**
- ODR-003 legal/accounting entity, tax registration and accounting-book cardinalities; legal conclusions remain deferred to post-project review under the product owner's explicit scope instruction.
- ODR-008 detailed per-UOM/workflow precision profiles, device resolution, commercial rate precision and domain-specific rounding boundaries.
- ODR-011/012 detailed processing, costing, negative-stock and stock adjustment policy where these change authoritative entities/constraints.
- ODR-017 accounting book/Chart of Accounts/posting-period and source-event recognition contracts.
- ODR-026 data classification, retention/legal hold/deletion/anonymization.
- Verified NFR workload envelope and database/technology PoC evidence (ODR-027/030/031).

**No longer blockers at the conceptual level:**
- The basic Tenant/Branch/OperatingLocation/StockLocation separation.
- Permission-first authorization and cross-Branch shared-site stock-task access.
- Multi-Tenant membership cardinality baseline and non-authorizing workforce Branch assignments.
- Initial product variability, customization, extension and module-evolution policy.

### 9.3 Selected persistence design guardrails (no schema design)

The logical model must preserve separate records/lifecycles for UserAccount, TenantMembership, MembershipBranchAssignment, PermissionAssignment, optional Role/PermissionBundle, Tenant/Branch/OperatingLocation, ConfigurationKey/ConfigurationVersion, FeatureEntitlement, CapabilityActivation, ExtensionInstallation/Grant, Party/PartyRole and immutable business transactions. This is a conceptual separation only; it does not authorize table/column design or DDL.

Do not use one generic settings blob or one generic role assignment to conflate all these concepts. Configuration and permissions must be queryable, versioned, auditable and enforceable. Keep configuration snapshots and policy versions on finalized transactions where their meaning affects historical results.

**Physical database design remains gated** by the dependencies listed in 9.2 and repeatable technology PoCs. No SQL, DDL, ORM entities, migrations or code have been created by this addendum.


The current permission model is documented in ARC-IAM-001, ARC-IAM-CAT-001 and ARC-AUTH-MATRIX-001. These replace any older assessment wording that described general roles/membership or the cross-Branch permission matrix as unresolved.