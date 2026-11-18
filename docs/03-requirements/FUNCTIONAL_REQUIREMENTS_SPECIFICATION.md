# Functional Requirements Specification (FRS)

**Document ID:** FRS-001  
**Status:** Comprehensive functional baseline — product direction accepted; domain policy, external evidence and acceptance verification remain governed by the Open Decisions Register  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, approved product-owner statements, and linked domain specifications.  
**Purpose:** Define observable system capabilities and testable functional requirements before UX, API, database, and implementation design.

## 1. Scope and interpretation

This specification covers the comprehensive Dairy ERP SaaS product. It is not a commitment that every capability ships in the first release. Delivery phases may sequence implementation but must not silently remove capabilities from the approved product scope.

The system is an API-first, capability-composable business operations platform. Authorized dairy personnel use web, mobile and relevant operational clients through shared server-side contracts; the SaaS platform operator uses separately authorized platform capabilities. Client type does not grant business authority. Customer self-service login, consumer storefront/e-commerce and customer mobile accounts are outside current scope.

A tenant may be a one-person dairy, a small retailer, a multi-branch operator, a loose-milk procurement/packing operation, a packaged-goods reseller, or a processing business. Capabilities must be optional/configurable according to operating profile. No employee, GST registration, corporate bank account, payment gateway, warehouse, processing unit or dedicated department is universally mandatory.

### 1.1 Normative language
- **MUST**: mandatory platform/product requirement.
- **SHOULD**: expected default; deviation requires documented rationale.
- **MAY**: optional capability.
- **TBD/Open**: not approved; do not implement by assumption.

### 1.2 Requirement identity
Requirement IDs are stable. Each requirement must be traceable to domain source, acceptance test, API operation and (later) data entities. A requirement change must record rationale and impact.

## 2. Product-wide functional requirements

| ID | Requirement | Priority | Acceptance evidence |
|---|---|---|---|
| FRS-GEN-001 | The backend MUST expose business capabilities through versioned APIs usable by web and mobile clients. | Must | Same business operation and authorization outcome across clients |
| FRS-GEN-002 | Business rules MUST execute on the server; clients cannot independently decide authoritative prices, permissions, stock, balances or status transitions. | Must | Tampered client request is rejected or recalculated |
| FRS-GEN-003 | Every tenant-owned record and query MUST be constrained by authenticated tenant context. | Must | Cross-tenant read/write tests fail closed |
| FRS-GEN-004 | Branch/location scope MUST be enforced independently from tenant scope. Tenant-wide records may be branch-neutral; branch-attributed events require valid scope. | Must | Cross-branch access tests and branch-neutral use cases |
| FRS-GEN-005 | Tenant plan entitlements MUST control feature/usage availability but MUST NOT grant data access or override authorization. | Must | Entitlement and permission tests independently pass/fail |
| FRS-GEN-006 | The product MUST distinguish fixed platform controls, configurable business policies and extensible integration points. | Must | Configuration cannot disable security/integrity invariants |
| FRS-GEN-007 | Material actions MUST preserve actor, timestamp, source, reason and before/after or event history as appropriate. | Must | Audit retrieval for create/change/approve/post/reverse/export |
| FRS-GEN-008 | Retryable commands MUST be idempotent where duplicate execution could create financial, stock, delivery or other material harm. | Must | Repeated identical command produces one business effect |
| FRS-GEN-009 | Concurrent updates MUST use server-side consistency controls and explicit conflict outcomes. | Must | Two competing updates cannot silently overwrite each other |
| FRS-GEN-010 | Historical transactions MUST retain the effective product, unit, price, tax, schedule and policy snapshots needed to explain the result. | Must | Later master edits do not change historical result |
| FRS-GEN-011 | The system MUST support INR and one consistent Indian date convention, with IST as default business timezone. | Must | Display, storage and business-date boundary tests |
| FRS-GEN-012 | English is primary; localization infrastructure MUST support Marathi, Hindi and additional Indian languages, including Unicode names and documents. | Must | Locale switch and Unicode round-trip tests |
| FRS-GEN-013 | The system MUST distinguish business date from server timestamp where shifts/cut-offs require it. | Must | Late-night/shift-boundary tests |
| FRS-GEN-014 | Exports and reports MUST enforce the same Tenant, Branch, resource, field and action permissions as interactive views. | Must | Unauthorized export is denied and audited |
| FRS-GEN-015 | Business workflows MUST support owner-operated operation without requiring employee records or artificial staff roles. | Must | A sole operator completes setup, sale, delivery and close using only explicitly assigned permissions |
| FRS-GEN-016 | One product MUST support the smallest owner-operated dairy through large multi-branch businesses using composable capabilities and proportionate setup/usage, without customer-specific code forks or divergent product semantics. | Must | Small/medium/large Tenant journeys use the same product contracts and historical semantics |
| FRS-GEN-017 | Plan entitlement, capability activation, Tenant configuration, principal permission, release feature flag and extension grant MUST be independent controls; none may silently substitute for another. | Must | Negative tests prove entitlement/activation/configuration/extension do not authorize an action |

## 3. Platform administration and SaaS tenancy

| ID | Requirement | Priority |
|---|---|---|
| FRS-PLT-001 | Platform-authorized users MUST create, verify, activate, suspend and offboard tenant workspaces through audited workflows. | Must |
| FRS-PLT-002 | Platform administration MUST manage plans, feature entitlements, usage limits, trials, renewals and subscription status as separately governed commercial records. | Must |
| FRS-PLT-003 | Tenant onboarding MUST allow a business profile and at least one initial branch without requiring GST registration. | Must |
| FRS-PLT-004 | A tenant MUST be able to add branches independently of GST registration status, subject to separately verified legal profile rules. | Must |
| FRS-PLT-005 | Tenant configuration MUST distinguish business-wide settings from branch/location-specific settings and identify inheritance/override behavior. | Must |
| FRS-PLT-006 | Platform support access to tenant data MUST be permission-limited, justified, time-bounded where feasible and audited. | Must |
| FRS-PLT-007 | Tenant export, closure, suspension, retention and deletion workflows MUST be explicit and permissioned. | Must |

## 4. Business structure, identity and access

| ID | Requirement | Priority |
|---|---|---|
| FRS-IAM-001 | A person, login account, employee record, role, permission and device MUST be represented as distinct concepts. | Must |
| FRS-IAM-002 | A global user identity MAY hold independent memberships in multiple Tenants; each user/Tenant pair has at most one current membership. | Must |
| FRS-IAM-003 | Authorization MUST evaluate active Tenant membership, effective explicit permission, resource and Branch/location scope, ownership, state, grant validity, limits and approval policy as applicable. Identity, role title and account type identify/organize the actor but MUST NOT grant authority. | Must |
| FRS-IAM-004 | The system MUST support platform-level, Tenant, Branch, location, resource/task and sensitive-field permissions as applicable. Permissions MUST be independently assignable, changeable, restrictable, expirable and revocable within delegated authority. | Must |
| FRS-IAM-005 | Backend authorization MUST be applied to every API and background operation; hiding a UI control is insufficient. | Must |
| FRS-IAM-006 | The system MUST support configurable approval/separation-of-duties policies for high-risk actions (financial posting, refunds, stock adjustment, payroll finalization, permission changes, Tally bulk sync); whether approval is required for a given action is governed by the approved policy. | Must |
| FRS-IAM-007 | Device/session lifecycle, revocation and security event history MUST be supported. | Must |
| FRS-IAM-008 | Customer self-service identities MUST NOT be provisioned under the current approved product scope. | Must |
| FRS-IAM-009 | No role title, job title, seniority, account type or Home/Primary Branch assignment MUST grant access. Every data disclosure and action requires the applicable effective permission and scope; cross-Branch read, mutation, approval, sensitive-field access and export are independently permissioned. Identical effective permissions and scope MUST yield identical authorization outcomes regardless of labels. | Must |
| FRS-IAM-010 | A Tenant membership MAY have multiple workforce Branch assignments and an optional single current Home/Primary Branch; these assignments provide operating context only and MUST NOT grant data visibility or action authority. | Must |

### 4.1 Customer-configurable permission governance

The Tenant MUST be able to configure eligible permission bundles and permission assignments for its users/groups, subject to fixed platform controls and the assigning actor's own delegated authority. Roles are optional editable bundles only. Direct principal and governed group permission assignments MUST be supported with explicit allow/deny, resource scope, limits, purpose and effective period. Explicit deny, default deny, fail-closed conflict handling, change audit and cross-channel revocation are mandatory. The canonical product-wide atomic key inventory and metadata requirements are governed only by ARC-IAM-CAT-001 section 15 and ODR-005 section 16.39; domain-level approval/risk controls are governed by ARC-IAM-CTRL-001 and ODR-005 section 16.41. See ARC-IAM-001, ARC-AUTH-MATRIX-001 and BR-FIX-024.

### 4.2 Cross-Branch shared-site stock-task grants

| ID | Requirement | Priority |
|---|---|---|
| FRS-IAM-011 | The system MUST support a StockTaskAccessGrant for exactly one named worker and one stock-owning/target Branch, limited to explicitly scoped OperatingLocation/StockLocation, permitted stock actions, stock/item/lot/task scope where applicable, quantity/value limits and an effective period whose end is strictly later than its start. Group-wide, role-wide or anonymous grants MUST NOT be supported. | Must |
| FRS-IAM-012 | A submitted grant version MUST remain non-effective until an authorized user holding the required stock-owning-Branch approval permission, or a currently valid delegate within the approved delegation policy, approves that exact immutable version and payload. | Must |
| FRS-IAM-013 | A grant MUST NOT create or replace the worker's independent action permission, active Tenant membership, resource scope or any workforce assignment required by Tenant policy. All such conditions MUST be revalidated at every stock-affecting transition and commit. | Must |
| FRS-IAM-014 | Material grant changes, including worker, Branch, location, actions, stock/task scope, limits or effective period, MUST create a new immutable GrantVersion and require fresh approval before the changed authority is used. A worker change MUST require a new grant request. | Must |
| FRS-IAM-015 | Grant effective start MUST be inclusive and effective end exclusive, and approval MUST NOT authorize actions retroactively before approval commit. Expiry, revocation and automatic invalidation MUST block new tasks and further stock mutations immediately, with zero grace; notification, cached state and pending extension requests MUST NOT extend authority. | Must |
| FRS-IAM-016 | If a grant expires, is revoked or invalidated while a stock task is in progress, the affected task MUST be held while preserving completed steps, quantities, custody, stock effects and outstanding work. Further work MUST require an active grant for the same worker or a new grant and valid reassignment for a replacement worker, followed by controlled reconciliation. | Must |
| FRS-IAM-017 | The system MUST distinguish grant authorization state from task reconciliation state and retain append-oriented grant/task decisions, approvals, holds, handovers, corrections, compensation and closure evidence. | Must |
| FRS-IAM-018 | Grant approval, version changes, expiry, revocation, invalidation and stock-affecting task commits MUST be idempotent, auditable and concurrency-safe across APIs, mobile/barcode, jobs, retries and integrations. | Must |
| FRS-IAM-019 | Every protected business data/action operation across all enabled and optional modules MUST map to one or more registered atomic permission keys from the canonical catalogue; a missing mapping MUST fail closed. | Must |
| FRS-IAM-020 | Core permission keys MUST use the stable, language-neutral `domain.resource.action` format and registered lifecycle/version metadata. Unknown, malformed, retired or incompatible keys MUST deny by default; wildcard/all-permissions grants MUST NOT be an implicit or default assignment. Extension keys MUST use their registered namespaced format. | Must |
| FRS-IAM-021 | Tenants MUST be able to create/version editable permission bundles, assign eligible atomic keys directly or through governed groups, apply scoped allow/deny assignments, amend and revoke them within delegated authority; Tenant display labels MUST NOT alter key semantics. | Must |
| FRS-IAM-022 | Sensitive-field access, bulk search/list, report drill-down, export and download MUST be independently permissioned and constrained to declared data classes, rows, fields, purpose and resource scope; export MUST NOT bypass field-level permissions. | Must |
| FRS-IAM-023 | Extensions MUST register namespaced, versioned permissions for declared extension resources/actions. Extension installation, activation or credential creation MUST NOT grant extension data access or core-resource access without a separate explicit scoped grant. | Must |
| FRS-IAM-024 | Platform-only permissions and Tenant-assignable permissions MUST be distinct. Platform/support identities MUST NOT obtain implicit Tenant business-data access; Tenant-data support access requires a separate scoped, purpose-bound and auditable SupportAccessGrant. | Must |
| FRS-IAM-025 | Each registered permission key MUST declare which collection/list, search/discovery, detail, aggregation and drill-down surfaces it authorizes. A list/search/detail surface MUST NOT be inferred from a permission unless the catalogue metadata explicitly includes it; row/field filtering and sensitive-field permissions remain enforced. | Must |
| FRS-IAM-026 | Permission assignment creation, restriction, expiry and revocation MUST be effective-dated, versioned and auditable; revocation or an explicit deny MUST invalidate subsequent access across all channels, and a concurrent write MUST revalidate authorization at the authoritative commit boundary. | Must |
| FRS-IAM-027 | The system MUST distinguish a user's permission to perform a domain action from any additional approval requirement. Workflow task assignment or generic approval capability MUST NOT replace the exact domain action permission or resource scope. | Must |
| FRS-IAM-028 | Tenants MUST be able to configure versioned ApprovalPolicyProfiles/Rules by domain action, risk, resource/scope, amount/quantity/UOM, data sensitivity or effective period, including approval-required conditions, thresholds, reviewer count/quorum, independence, stage order and evidence. Fixed safeguards MUST NOT be weakened. | Must |
| FRS-IAM-029 | Every approval decision MUST be bound to the exact source resource, immutable payload/version and the exact ApprovalPolicyEvaluationSnapshot used for that decision; the snapshot MUST identify every matched ApprovalPolicyVersion/Rule and combined requirement. It MUST retain actual initiator/approver, effective permissions/scope, threshold evaluation, decision, reason, evidence and time. Material payload changes MUST invalidate/restart approval; commit-time authorization and policy evaluations MUST be revalidated. | Must |
| FRS-IAM-030 | The system MUST provide editable, proportionate starter approval profiles for single-operator, small-team, multi-Branch and enterprise operations. Profiles MUST initialize policy configuration only; they MUST NOT assign permissions, create fixed user/account classes or alter canonical key semantics. | Must |
| FRS-IAM-031 | ApprovalPolicyProfiles and ApprovalPolicyVersions MUST have a controlled, effective-dated lifecycle. Every published version MUST be immutable; material changes MUST create a new version; future-effective versions MUST NOT govern decisions before activation; prior versions MUST remain available for historical decision replay. | Must |
| FRS-IAM-032 | Policy changes that weaken an approval control, broaden an approval scope, reduce reviewers/evidence, relax maker-checker/independence or make an action delegable MUST require independent approval by a different authorized user before publication. Tightening-only changes MAY be published by a user with the exact configure/publish permissions where no stricter policy applies. Fixed safeguards MUST NOT be weakened. | Must |
| FRS-IAM-033 | Every ApprovalRequest MUST retain an immutable ApprovalPolicyEvaluationSnapshot identifying all matched policy versions/rules and the combined requirements. At approval and commit, current effective policy MUST be re-evaluated: a stricter new policy MUST add requirements and invalidate affected prior stages; a less restrictive policy MUST NOT reduce requirements on an existing pending request; conflicts or unresolvable policy MUST hold/deny commit. | Must |
| FRS-IAM-034 | An ApprovalPolicyVersion MUST be immutable after publication, effective-dated and historically reproducible. Policy edits MUST create a new version; a scheduled version MUST NOT apply before its effective start; retirement MUST NOT create an ungoverned approval gap. | Must |
| FRS-IAM-035 | Any policy change that weakens approval controls or broadens delegated approval/data/export scope MUST require independent approval by a different authorized user before publication. Tightening-only changes MAY use the authorized configure/publish path where no stricter policy requires review. Fixed safeguards MUST NOT be weakened by Tenant configuration. | Must |
| FRS-IAM-036 | When multiple approval policies apply, fixed safeguards MUST be applied and compatible requirements combined. The stricter valid rule MUST prevail; incomparable units, ambiguous scope or conflicting rules MUST hold/deny the decision. Each ApprovalRequest MUST preserve the original policy snapshot and linked stricter re-evaluations without overwriting history. | Must |

## 5. Branches, locations and operating profile

| ID | Requirement | Priority |
|---|---|---|
| FRS-ORG-001 | A tenant MUST support one or many branches. | Must |
| FRS-ORG-002 | Branch, outlet, warehouse, collection centre, processing site, stock location, service area and route MUST remain distinct concepts even when co-located. | Must |
| FRS-ORG-003 | A tenant MAY configure which capabilities are active at each location. | Must |
| FRS-ORG-004 | Transfers between locations MUST record dispatch, receipt, quantities, responsible actors and discrepancies. | Must |
| FRS-ORG-005 | Consolidated and branch-specific operational/financial reporting MUST be permission-aware. | Must |

## 6. Customer, subscription, order and sales

| ID | Requirement | Priority |
|---|---|---|
| FRS-CUS-001 | Staff MUST create and maintain customer profiles, multiple service addresses, contacts, instructions and account status. | Must |
| FRS-CUS-002 | Customer records MUST be usable without customer login credentials. | Must |
| FRS-SUB-001 | A subscription MUST support one or more product lines with quantity, unit, price basis, service address, branch/area and effective dates. | Must |
| FRS-SUB-002 | Recurrence MUST support daily, alternate-day, every-N-day, selected weekdays and other validated recurrence patterns. | Must |
| FRS-SUB-003 | Recurrence MUST support configurable shifts, not only fixed morning/evening values. | Must |
| FRS-SUB-004 | Staff MUST be able to pause, resume, skip, change quantity/product, substitute, cancel and reinstate according to effective-date and cut-off rules. | Must |
| FRS-SUB-005 | Schedule generation MUST be idempotent and preserve schedule version/source for every occurrence. | Must |
| FRS-SUB-006 | Planned, allocated, dispatched, delivered, returned and billable quantities MUST remain distinct. | Must |
| FRS-ORD-001 | Staff MUST capture one-time orders and additional products independently of recurring subscription lines. | Must |
| FRS-SAL-001 | POS MUST support walk-in/counter sales with configurable cash, UPI, bank, account/credit and other enabled tender methods. | Must |
| FRS-SAL-002 | POS MUST support appropriate receipt/return/void workflows with permissions and audit; posted sales cannot be silently erased. | Must |
| FRS-SAL-003 | Sales channels MUST share product, price, tax, stock and accounting rules rather than implementing divergent calculations. | Must |
| FRS-SAL-004 | Discounts, overrides, credit sales and refunds MUST follow configurable limits and approvals. | Must |

## 7. Procurement, farmer collection and quality

| ID | Requirement | Priority |
|---|---|---|
| FRS-PRC-001 | Staff MUST maintain suppliers and farmers with contacts, terms, payment details and status. | Must |
| FRS-PRC-002 | Packaged-goods purchases and raw-milk collection MUST have distinct workflows while sharing supplier/product/accounting foundations. | Must |
| FRS-PRC-003 | Milk collection MUST record producer, collection point, date/shift, measured quantity, unit, sample/test evidence and acceptance outcome. | Must |
| FRS-PRC-004 | Accepted, rejected, quarantined and pending quantities MUST be distinguishable. | Must |
| FRS-PRC-005 | Procurement rates MUST be effective-dated and support validated quality-based calculation, incentives, penalties and deductions. | Must |
| FRS-PRC-006 | Farmer settlement calculation MUST be separate from collection entry and actual payment. | Must |
| FRS-PRC-007 | Purchase orders, receipts, supplier invoices, returns, advances and payments MUST preserve source links and partial quantities/settlements. | Must |
| FRS-PRC-008 | Quality parameters and acceptance policies MUST be configurable/versioned; no universal unverified milk-quality formula may be hardcoded. | Must |

## 8. Product, processing, inventory and traceability

| ID | Requirement | Priority |
|---|---|---|
| FRS-INV-001 | Product catalogue MUST distinguish sellable product, procurement item, stock item, variant, SKU, pack size and unit where applicable. | Must |
| FRS-INV-002 | Unit conversions and quantity precision MUST be explicit, validated and retained in transaction snapshots. | Must |
| FRS-UOM-001 | The platform MUST maintain a versioned canonical UOM catalogue with stable machine identifiers, explicit dimensions, canonical symbols/formal names and localized display labels. Tenant customization MUST NOT redefine canonical unit identity or dimensional semantics. | Must |
| FRS-UOM-002 | Every stock-managed SKU MUST resolve to exactly one effective stock base UOM; a Product MAY supply a default only where variants/SKUs are dimensionally compatible. Non-stockable services MUST NOT require a stock base UOM. | Must |
| FRS-UOM-003 | Every quantity-bearing transaction MUST preserve the original entered/source quantity and UOM, source precision, normalized/base quantity where applicable, conversion factor/rule version, precision profile and relevant evidence/policy versions. | Must |
| FRS-UOM-004 | The system MUST reject incompatible-dimension arithmetic and generic conversion. Contextual cross-dimensional conversion (including milk mass/volume) MUST require an approved, versioned formula and required inputs; source and derived values MUST remain separate. | Must |
| FRS-UOM-005 | Pack count, pack content and total derived content MUST remain distinct. Pack/unpack, case-break, filling and repacking MUST be explicit, versioned stock/production transformations, not generic count-to-volume/mass conversions. | Must |
| FRS-UOM-006 | Quantity precision MUST distinguish source/device resolution, instrument accuracy/calibration, stored precision, calculation precision, posting precision, display precision and business tolerance. A single Tenant-wide scale MUST NOT be assumed. | Must |
| FRS-UOM-007 | Tenants MUST be able to select supported UOMs, SKU base units, pack definitions, validated conversion rules and precision profiles within platform/tested/legal bounds; arbitrary unsupported scales or dimension changes MUST be rejected. | Must |
| FRS-UOM-008 | Authoritative quantity, rate and monetary calculations MUST use exact decimal/fixed-point arithmetic; binary floating-point MUST NOT be used as the source of truth. Rounding mode, precision, order of operations, boundary and policy version MUST be explicit. | Must |
| FRS-UOM-009 | Ordinary INR currency posting MUST default to rupees and paise (two decimal places), with per-unit rates and intermediate calculations allowed separately governed higher precision. Applicable statutory rounding rules MUST override ordinary commercial defaults only within their verified scope. | Must |
| FRS-UOM-010 | SKU base-UOM, conversion, precision, pack-definition and rounding-policy changes MUST be versioned/effective-dated and MUST NOT reinterpret or silently alter historical transactions, stock movements or finalized financial documents. | Must |
| FRS-UOM-011 | Tally and other external adapters MUST detect target UOM/currency precision and conversion capabilities. Unsupported/lossy precision MUST be reported and blocked or handled only through an explicit approved mapping/rounding policy; silent truncation is prohibited. | Must |
| FRS-UOM-012 | Reports and analytics MUST disclose the UOM basis, source/derived status, conversion/profile version and aggregation grain; incompatible dimensions MUST NOT be aggregated. | Must |
| FRS-INV-003 | Stock balances MUST derive from an auditable stock movement ledger, not direct mutable quantity edits. | Must |
| FRS-INV-004 | Stock MUST be scoped to stock location and distinguish available, reserved, quarantined, damaged, expired and in-transit quantities. | Must |
| FRS-INV-005 | Receipts, issues, reservations, transfers, returns, counts, wastage and adjustments MUST have controlled lifecycle and audit. | Must |
| FRS-INV-006 | Batch/lot, expiry and FEFO/FIFO allocation MUST be supported where product/tenant policy requires it. | Must |
| FRS-PRD-001 | Processing capability MUST support versioned process definitions, input lots, output lots, WIP, yield, by-products, losses and quality release. | Must |
| FRS-PRD-002 | Tenants that only resell packaged goods MUST not be forced to configure processing. | Must |
| FRS-PRD-003 | Traceability MUST allow forward and backward lineage between source collection/purchase lots, processing batches and finished goods lots. | Must |

### 8.1 Shared-site custody and resource accountability

These requirements apply where a Tenant enables custody tracking for the relevant stock, container, asset, facility or workflow. The assurance level and subject coverage are configurable; ownership, permission and event-integrity invariants remain fixed.

| ID | Requirement | Priority |
|---|---|---|
| FRS-CST-001 | The system MUST distinguish economic/stock ownership, stock-ledger attribution, physical location, physical custody, actual handler, workforce assignment, permission and approval. | Must |
| FRS-CST-002 | Where custody tracking is enabled, the system MUST record a traceable custody event with subject, actor/handler, custodian, location, action, time, outcome and applicable policy/evidence version. | Must |
| FRS-CST-003 | The system MUST support configurable, effective-dated custody assurance profiles, including proportionate single-party capture, acknowledged handover and high-assurance evidence where required. | Must |
| FRS-CST-004 | A handover MUST NOT be accepted solely because of timeout, silence, failed notification or client-side status; pending/disputed handovers MUST preserve the last verified custodian and known location. | Must |
| FRS-CST-005 | The system MUST support partial receipt, rejection, quantity/condition discrepancy and linked incident/reconciliation records without overwriting dispatch or receipt facts. | Must |
| FRS-CST-006 | Custody events MUST remain distinct from StockMovement and financial postings; a custody-only event MUST NOT create stock/financial effects, and custody discrepancies MUST NOT automatically create write-offs, supplier/employee deductions or liability findings. | Must |
| FRS-CST-007 | Shared facilities/equipment MUST support distinct ownership, allowed-use, booking/availability, custody, actual use, cleaning/calibration/maintenance and optional cost-allocation records where enabled. | Must |
| FRS-CST-008 | Custody access, evidence, handover and incident actions MUST use explicit permissions and resource/Branch scope; grant expiry/revocation MUST preserve actual physical custody while blocking unauthorized further operations. | Must |
| FRS-CST-009 | Perishable/cold-chain workflows MUST be able to capture effective-dated control profiles and actual timestamped quantity, container/lot, condition, temperature, quality and receiving evidence where applicable; exact limits MUST be sourced from applicable verified policy, not a universal hardcoded rule. | Must |
| FRS-CST-010 | Customer-configured custody profiles, templates and evidence requirements MUST preserve historical versions, audit, upgrade compatibility and cross-channel enforcement. | Must |

## 9. Distribution and delivery

| ID | Requirement | Priority |
|---|---|---|
| FRS-DLV-001 | Delivery planning MUST combine subscription occurrences, one-time orders, approved extra supply and redelivery requests without losing source identity. | Must |
| FRS-DLV-002 | Service areas, route templates, delivery runs, stops, attempts and fulfilments MUST be distinct records. | Must |
| FRS-DLV-003 | Assignment MUST restrict a delivery worker to assigned/authorized work and minimum necessary customer data. | Must |
| FRS-DLV-004 | Delivery outcomes MUST support delivered, partial, failed, customer unavailable, refused, rescheduled, returned and other configured reasons. | Must |
| FRS-DLV-005 | Corrections and redelivery MUST preserve original attempt and link compensating activity. | Must |
| FRS-DLV-006 | The system MUST define and enforce an explicit offline/conflict protocol for mobile delivery; the guaranteed offline workflows and duration remain subject to ODR-020. | Must |
| FRS-DLV-007 | Delivery execution MUST not itself imply billing or payment unless an explicit posting event/policy triggers it. | Must |
| FRS-DLV-008 | A DeliveryPlan MUST be able to group one or more DeliveryRuns; each DeliveryRun MUST use a Route or one-off manual Route plan owned by the same Branch as the run, within that Branch's effective ServiceArea assignments. | Must |
| FRS-DLV-009 | DeliveryRun MUST support one accountable run assignee and one or more effective-dated participant assignments without requiring an employee record for an owner-operated Tenant; operational duties MUST NOT grant permissions. | Must |
| FRS-DLV-010 | Vehicle/transport-resource assignment MUST be optional, time-bounded and independent of personnel assignment; where resource capacity or exclusivity is configured, the system MUST prevent over-capacity and conflicting concurrent assignments. | Must |
| FRS-DLV-011 | Planning, assigning, dispatching, executing, exception review and closing MUST be separately permissioned actions; dispatch MUST validate Branch/Route ownership, demand allocation, stock/custody, active assignments and all applicable readiness controls. | Must |
| FRS-DLV-012 | Dispatch MUST snapshot/version the route, stop manifest, demand allocation, participants, transport resources and relevant policy versions; post-dispatch changes MUST use an auditable exception/reassignment workflow. | Must |
| FRS-DLV-013 | The system MUST support configurable product/transport handling requirements and evidence (including applicable hygiene, packaging, temperature, condition and traceability evidence) without applying one universal checklist or temperature to all products. | Must |
| FRS-DLV-014 | Route optimization, maps, telematics/GPS and external carrier integrations MUST be optional extensions; optimization MUST respect Branch/service-area, capacity, workforce, customer and product-handling constraints and MUST NOT silently modify a dispatched plan. | Must |
| FRS-DLV-015 | Where Tenant policy or verified applicable rules require driver/vehicle qualification or compliance evidence, the system MUST record source, verification state, effective dates and expiry and MUST block dispatch where required evidence is invalid or expired. | Must |
| FRS-DLV-016 | Dispatch and delivery custody events MUST be linked to the relevant DeliveryRun and StockMovement where both physical custody and inventory effects occur; neither may imply or duplicate the other. | Must |
| FRS-DLV-017 | Concurrent run planning/dispatch MUST prevent duplicate allocation of source demand, duplicate stop fulfilment, conflicting exclusive resource assignments and duplicate stock effects; retries and stale clients MUST be safely reconciled. | Must |
| FRS-DLV-018 | A vehicle/participant failure or revoked assignment during a run MUST preserve completed work and current physical custody while placing affected work on hold until valid reassignment, handover or controlled exception resolution. | Must |

## 10. Billing, receivables and payments

| ID | Requirement | Priority |
|---|---|---|
| FRS-BIL-001 | Billing MUST support recurring-cycle and ad-hoc charges, adjustments, credits, advances and partial settlement. | Must |
| FRS-BIL-002 | Customer statement MUST remain distinct from invoice and receipt. | Must |
| FRS-BIL-003 | Billable quantity MUST follow approved fulfilment and adjustment rules, not planned schedule quantity alone. | Must |
| FRS-BIL-004 | Month-end billing MUST be supported; other billing cycles MAY be configured after rule approval. | Must |
| FRS-PAY-001 | Payment recording MUST support cash, UPI QR, direct bank transfer and optional gateway methods. | Must |
| FRS-PAY-002 | The system MUST allow receipt into personal or business bank accounts without requiring gateway integration. | Must |
| FRS-PAY-003 | A manually entered UPI reference MUST be marked unverified until external evidence confirms it. | Must |
| FRS-PAY-004 | Payment record, provider confirmation, bank settlement and reconciliation MUST be separate states/evidence. | Must |
| FRS-PAY-005 | Partial allocations, on-account receipts, refunds, reversals, transfers and unmatched items MUST be supported. | Must |

## 11. Accounting, expenses and Tally readiness

| ID | Requirement | Priority |
|---|---|---|
| FRS-ACC-001 | The system MUST maintain a vendor-neutral accounting model independent of Tally. | Must |
| FRS-ACC-002 | Posted journals MUST use double-entry bookkeeping and balance exactly under defined decimal precision. | Must |
| FRS-ACC-003 | Operational modules MUST emit controlled financial posting events through versioned posting rules. | Must |
| FRS-ACC-004 | Posted financial entries MUST be immutable; correction uses linked reversal/adjustment. | Must |
| FRS-ACC-005 | Customer, supplier/farmer, stock, cash and bank subledgers MUST reconcile to control accounts. | Must |
| FRS-ACC-006 | Financial periods MUST support close, controlled reopen and audit history. | Must |
| FRS-ACC-007 | Chart of Accounts, voucher numbering, dimensions and posting mappings MUST be tenant/books-configurable within integrity constraints. | Must |
| FRS-EXP-001 | Expense capture MUST support vendor bills, cash expenses, recurring expenses, petty cash, employee claims, advances and settlements. | Must |
| FRS-EXP-002 | Approval, payment, posting and reconciliation MUST be distinct events. | Must |
| FRS-BNK-001 | Cash/bank account registry MUST support personal/business account classification, UPI references and masked sensitive identifiers. | Must |
| FRS-BNK-002 | Statement imports MUST preserve source files, row-level evidence, validation and duplicate detection. | Must |
| FRS-BNK-003 | Reconciliation MUST support suggestions, manual matching, exceptions and controlled reopen. | Must |
| FRS-TLY-001 | Accounting structures MUST be ready for a separately installed desktop synchronization application. | Must |
| FRS-TLY-002 | Tally mapping MUST use stable SaaS IDs and versioned external mappings; names alone are insufficient identity. | Must |
| FRS-TLY-003 | Export/import MUST preserve voucher identity, dates, ledger lines, debit/credit, party, dimensions, tax references and reversal links where supported. | Must |
| FRS-TLY-004 | Every sync batch MUST support preview, validation, approval policy, idempotency, retry, per-record outcomes and reconciliation. | Must |
| FRS-TLY-005 | Tally release-specific XML/JSON/TDL capabilities MUST be detected/tested; unsupported/lossy fields MUST be disclosed. | Must |
| FRS-TLY-006 | Tally integration MUST not require public inbound access to the customer's desktop/Tally instance. | Must |

## 12. HR, payroll, assets and maintenance

| ID | Requirement | Priority |
|---|---|---|
| FRS-HR-001 | Workforce capability MUST be optional; an owner-only tenant can operate without employee setup. | Must |
| FRS-HR-002 | Personnel, login, engagement type, attendance, shift, leave and compensation MUST be distinct concepts. | Must |
| FRS-HR-003 | Attendance corrections, overtime, leave and payroll inputs MUST be auditable and approval-controlled. | Must |
| FRS-HR-004 | Payroll MUST support configurable effective-dated earnings, deductions, contributions, advances and reimbursements. | Must |
| FRS-HR-005 | Statutory payroll applicability MUST be evaluated by reviewed, versioned rules; a toggle alone cannot claim compliance. | Must |
| FRS-HR-006 | Finalized payroll MUST not be silently recalculated or overwritten. | Must |
| FRS-AST-001 | Asset register, maintenance plan, work order, downtime and cost history SHOULD be supported for relevant tenants. | Should |

## 13. Tax and India-specific capability

| ID | Requirement | Priority |
|---|---|---|
| FRS-TAX-001 | GST registration MUST be optional and independent of branch count. | Must |
| FRS-TAX-002 | Tax classification and rates MUST be effective-dated and evidence-backed; no single assumed rate for all dairy products. | Must |
| FRS-TAX-003 | Invoice/document behavior MUST depend on verified tenant and transaction applicability. | Must |
| FRS-TAX-004 | Tax snapshots MUST be preserved on finalized documents. | Must |
| FRS-TAX-005 | Statutory exports/registers MUST be available without claiming direct portal filing unless an authorized integration is implemented and verified. | Must |
| FRS-TAX-006 | Legal/tax rule changes MUST not retroactively alter finalized transactions. | Must |

## 14. Reporting, analytics, projections and budgeting

| ID | Requirement | Priority |
|---|---|---|
| FRS-ANA-001 | Reporting MUST be a first-class capability across operations, finance, procurement, inventory, delivery, workforce and SaaS administration. | Must |
| FRS-ANA-002 | Each KPI MUST define formula, unit, grain, date basis, filters, source, owner and version. | Must |
| FRS-ANA-003 | Dashboards MUST provide authorized drill-down to source transactions and relevant financial postings. | Must |
| FRS-ANA-004 | Reports MUST distinguish actual, target, budget, forecast and scenario values. | Must |
| FRS-ANA-005 | Forecasts MUST disclose data cut-off, assumptions, method/version and limitations. | Must |
| FRS-ANA-006 | Budget versions and approvals MUST preserve previous approved values and revision history. | Must |
| FRS-ANA-007 | Scenario analysis MUST not create or mutate posted operational/financial transactions. | Must |
| FRS-ANA-008 | Heavy reports/forecasting MUST run asynchronously where needed and not block POS, delivery or posting. | Must |
| FRS-ANA-009 | Tenant and branch permissions MUST apply to aggregate, drill-down and export queries. | Must |

## 15. Notifications, workflow and integration

| ID | Requirement | Priority |
|---|---|---|
| FRS-WFL-001 | Configurable approval workflows MUST support amount/category/branch/risk conditions, approver scope and delegation policy. | Must |
| FRS-WFL-002 | Notification templates MUST support localization, delivery status, retries and provider abstraction. | Must |
| FRS-WFL-003 | Notification failure MUST not silently roll back a committed business transaction. | Must |
| FRS-INT-001 | External integrations MUST use versioned adapters/contracts and scoped credentials. | Must |
| FRS-INT-002 | Integration jobs MUST support retry, idempotency, error classification, dead-letter/manual resolution and audit. | Must |
| FRS-INT-003 | API clients MUST not receive broader permissions because of client type or integration status. | Must |

## 16. Cross-cutting exception and integrity requirements

1. A posted/issued/closed record cannot be hard-deleted to conceal history.
2. A failed workflow step must leave a visible retryable or compensating state; no silent partial completion.
3. Source events and downstream stock/financial effects must be linked by stable references.
4. Every retry path must be safe against duplicate effects.
5. A stale client update must be rejected or explicitly reconciled, not last-write-wins by default.
6. A reversal must reference the original and preserve both records.
7. Configuration changes must be effective-dated where they affect future or historical calculations.
8. Bulk imports must preview validation errors and partial success behavior before execution.
9. Sensitive fields and exports must be masked/restricted and access logged.
10. Cross-tenant or unauthorized cross-branch access must fail closed.

## 17. Non-functional requirements to be specified separately

This FRS establishes functional behavior, not final numerical targets. Separate NFR specification must quantify availability, latency, throughput, concurrency, tenant scale, retention, RPO/RTO, backup frequency, offline duration/conflict policy, accessibility, browser/device support, observability and cost envelopes. Development VPS limits MUST NOT cap production requirements.

## 18. Open decisions and dependencies

The following remain open and must be resolved through the Open Decisions Register rather than assumptions: legal entity/books structure and legal applicability (ODR-003); shared-site custody and accountability, route/dispatch and detailed grant-version/task-reconciliation baselines are specified under ODR-002 sections 16.36–16.38; remaining scenario validation and legal/contractual ownership/liability/insurance/vehicle-qualification applicability remain external-review dependencies; domain-level approval/risk-control semantics are selected in ODR-005 section 16.41; numeric threshold values are Tenant-configurable under ARC-IAM-CTRL-001, while exact resource/state/API mapping, sensitive-field classification and automated coverage remain implementation-readiness gates; subscription cut-off and recurrence precedence (ODR-006/007); ODR-008 UOM/precision semantics are selected in DBR-004 and section 16.40; exact device/process tolerance profiles, Legal Metrology applicability, statutory transaction rounding and Tally target-version loss handling remain evidence-gated; quality and procurement rates (ODR-009/010); processing and stock policy (ODR-011/012); pricing/billing/tax treatment (ODR-013/016); payment verification (ODR-015); detailed CoA and accounting policy (ODR-017); labour applicability (ODR-018); mobile packaging/offline guarantees (ODR-019/020); notification providers (ODR-021); KPI catalogue/forecast horizons/budget workflow (ODR-022/024); SaaS commercial rules (ODR-025); data lifecycle (ODR-026); scale and recovery objectives (ODR-027/028); integration priorities (ODR-029); technology/deployment/localization decisions (ODR-030/032).

## 19. Approval gate

This document is a proposed requirements baseline. Product-owner approval is required before treating any proposed business policy as final. No implementation or schema generation should begin solely from this draft until Gate C and Gate D review are complete.


## 20. Product variability and lifecycle requirements

The product MUST classify domain rules as fixed, configurable, customizable, extensible or externally dependent. Fixed invariants cannot be disabled by Tenant/Branch settings, subscription plans or extensions. Configurable policies use approved keys, allowed values/scopes, validation, versioning, effective dates and audit. Customer customization is limited to supported, upgrade-safe presentation/form/report/form/workflow/metadata surfaces. New capability and integration extensions use versioned contracts, separate Tenant-bound identities, explicit permissions, resource limits and revocation. Modules MUST maintain authoritative data ownership and avoid ungoverned direct writes across module boundaries. The initial modular-monolith direction and evidence-gated service extraction are governed by ARC-PV-001.
