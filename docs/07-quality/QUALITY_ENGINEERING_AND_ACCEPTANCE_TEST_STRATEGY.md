# Quality Engineering and Acceptance Test Strategy

**Document ID:** QES-001  
**Status:** Recommended engineering baseline selected under the product owner's delegated decision mandate — 2026-11-19  
**Authority:** Project Vision and Scope; FRS-001; NFR-001; ERS-001; RTM-001; Business Rules Catalogue; ARC-PV-001; ARC-IAM-001; ARC-IAM-CAT-001; ARC-AUTH-MATRIX-001; ARC-CONC-001; ARC-API-001; ARC-SEC-001; ARC-OBS-001; ARC-SCALE-001; ODR-001..036.  
**Purpose:** Define quality, testability and release acceptance for one comprehensive Dairy ERP SaaS product serving small owner-operated to large enterprise workloads.

## 1. Quality objectives

Quality is a product property, not a final phase. Every feature, configurable policy, module, integration, report, extension and release must be assessed for business correctness; small/medium/large operating-profile fit; Tenant isolation and permission-first authorization; data ownership, historical integrity, accounting and stock correctness; concurrency, idempotency and safe recovery; API/client/extension/configuration compatibility; performance, availability, scalability, tenant fairness and resource cost; security, privacy, auditability and operational support; accessibility, localization and upgrade safety.

Tests must verify both permitted behavior and prohibited behavior. A workflow that succeeds for the intended user is not accepted until unauthorized, stale, duplicated, conflicting and failure-path cases are also tested.

## 2. Requirements and test traceability

Every requirement and material decision has a stable ID and must link to:
1. Domain workflow/business rule.
2. Owning module and data ownership boundary.
3. Permission keys and scopes.
4. API/command/query/event contract where applicable.
5. Configuration/entitlement/extension dependencies.
6. Positive and negative acceptance scenarios.
7. Test level and automation/evidence owner.
8. Release gate and operational monitoring where applicable.

RTM-001 is the authoritative traceability register. A requirement cannot be marked verified based only on prose or screenshot; evidence must identify test version, environment, data profile, execution date, expected outcome and actual result.

## 3. Test layers

Use a balanced test portfolio. Do not maximize unit-test count while leaving integration, security, concurrency or end-to-end risks untested.

| Test layer | Primary responsibility | Examples |
|---|---|---|
| Domain/unit | Pure calculations, state transitions, invariants | Milk quantity conversion, rate chart, pricing precedence, tax calculation fixture, billing eligibility, stock availability |
| Application/service | Use-case orchestration, permission checks, transaction boundaries | Receive stock, approve settlement, issue invoice, close period, create grant |
| Persistence/integration | Constraints, locking, transactions, outbox/inbox, storage adapters | Tenant-safe references, unique keys, duplicate imports, atomic stock posting |
| API/contract | Request/response/event compatibility and error semantics | OpenAPI validation, API client compatibility, webhook event schemas |
| Cross-module workflow | Complete business transaction chains | Collection-to-settlement, subscription-to-cash, procurement-to-payable, production-to-finished stock |
| End-to-end/UI | User journeys across web/mobile/POS | Owner-only retail, branch delivery, multi-site processing, finance close |
| Security/authorization | Threat paths and denied operations | Tenant isolation, IDOR, Branch scope, sensitive fields, role/account-label neutrality |
| Concurrency/resilience | Race conditions, duplicates, failure, retry, recovery | Grant revoke vs stock commit, double settlement, stale update, provider timeout |
| Performance/scale | Capacity, fairness, overload and resource isolation | Peak POS, billing jobs, tenant noisy-neighbour, report pressure |
| Migration/recovery | Data/configuration evolution | Upgrade, rollback, restore, tenant isolation-tier move, Tally import reconciliation |
| Accessibility/localization | Inclusive operation and language/format integrity | Keyboard workflow, screen-reader labels, Marathi/Hindi Unicode round trip |

## 4. Required Tenant and operating profiles

Maintain representative synthetic datasets for:

- **Solo/small:** one user, one Branch, one OperatingLocation, no employees, no GST assumptions, basic retail or milk delivery, manual payments, simple stock.
- **Small combined operator:** collection/procurement plus retail/delivery, few products, basic quality capture and simple accounting.
- **Growing multi-Branch:** multiple Branches/ServiceAreas/Routes, shared physical site, independent StockLocations, employees, approvals, branch reporting and cross-Branch grants.
- **Processing dairy:** collection lots, quality holds, process/recipe versions, production batches, yield/loss, packaged finished goods and traceability.
- **Enterprise/multi-site:** many users/devices/locations, high transaction volume, central and Branch processes, delegated approvals, integration-heavy workflows, advanced analytics and isolation-tier needs.
- **Adverse/hostile:** duplicate/replayed requests, revoked users, stale devices, malformed imports, conflicting permissions, provider outage, quota saturation, cross-Tenant ID substitution and compromised extension credentials.

Use synthetic or properly de-identified data. Production personal/financial records must not be copied into non-production without an approved privacy-protective process.

## 5. Mandatory security and authorization test suites

### 5.1 Tenant and membership isolation
- Every Tenant-owned resource, relation, search index, file, cache, report, export, job, event, analytics row and integration callback is Tenant-bound.
- Multiple memberships of one global user are independent. Revoking Tenant A does not revoke Tenant B membership; Tenant B does not enable Tenant A access.
- Tenant ID supplied by a client is never accepted as authority.
- Platform identities and service identities have no default Tenant data access.

### 5.2 Permission-first behavior
- Role/job title, seniority, account type, client type and Home/Primary Branch changes alone must not alter authorization.
- Same effective permissions/context produce same outcome; identical role labels with different permissions produce different outcomes as configured.
- Tenant custom bundles can grant/revoke eligible permissions only within the assigning principal's delegation ceiling.
- Explicit deny overrides allow; ambiguity/missing policy fails closed.
- Read/list/search/export/sensitive-field access and each mutation/approval/post/reverse/configure action are tested independently.
- Feature entitlement, activation, configuration, feature flags and extension installation never grant permissions.
- Test every alternate entry point: UI, API, bulk endpoints, mobile/offline sync, POS, barcode, search, reports, exports/downloads, files, jobs, webhooks and third-party adapters.

### 5.2.1 Foundational IAM acceptance tests

Use stable identifiers AT-IAM-001..008 for the foundational identity, Tenant membership and permission-first requirements referenced by RTM-001:

- **AT-IAM-001:** Authentication establishes the principal only. A valid login, active account or selected client does not authorize any business record/action without active Tenant membership, exact registered permission, resource scope and applicable policy.
- **AT-IAM-002:** A user with memberships in two Tenants has isolated authorization contexts. Revocation/suspension in one Tenant does not grant, revoke or widen permissions in another; client-supplied Tenant identifiers cannot switch the server-side context.
- **AT-IAM-003:** Tenant isolation prevents IDOR through resource IDs, nested references, search, list, aggregate, reports, files, exports, jobs, event callbacks and integrations; foreign Tenant resources are indistinguishable from unavailable resources under the disclosure policy.
- **AT-IAM-004:** Branch, OperatingLocation, StockLocation, service area and assigned-resource scope are independent. Home/Primary Branch, workforce assignment, shared physical premises, geographic overlap or co-location alone grants no read/action permission.
- **AT-IAM-005:** Role/job title, seniority, account category, Owner/Administrator label, user type and client type are authorization-neutral. Two principals with identical effective permissions, scopes, validity, limits and context receive the same outcome regardless of labels.
- **AT-IAM-006:** Direct principal, governed group and optional PermissionBundleVersion assignments resolve to explicit atomic permission keys and scope. A bundle/group/member assignment does not create broader authority, and an assigner cannot exceed its own delegation ceiling.
- **AT-IAM-007:** Explicit deny takes precedence; missing/expired/revoked/ambiguous permission or scope fails closed. View/list/capture/submit/approve/post/reverse/export/configure/manage_access are independent and are verified through alternate API, bulk, report, mobile, job and integration paths.
- **AT-IAM-008:** Feature entitlement, capability activation, plan, feature flag, device registration, service identity, extension installation or platform identity alone grants no Tenant business access. Platform/support access requires a separate purpose-bound SupportAccessGrant and exact permissions.

### 5.3 Canonical permission catalogue and Tenant configuration

Use stable test identifiers AT-IAM-009..020 for ARC-IAM-CAT-001 section 15 / ODR-005 section 16.39:

- **AT-IAM-009:** Every protected command, field disclosure, report/export, job, event handler, device action and integration operation in each enabled module resolves to one or more registered canonical permission keys; an unmapped operation fails closed.
- **AT-IAM-010:** Core keys validate against the canonical three-part `domain.resource.action` format; extension keys validate against their registered namespace. Unknown, malformed, retired or incompatible keys are denied. A key's machine identifier and semantic meaning do not change when its display label, language, Tenant bundle name or UI text changes.
- **AT-IAM-011:** A Tenant can create/version editable bundles and starter copies, assign/restrict/revoke eligible keys directly or through governed groups, and scope assignments; the assigning principal cannot exceed its own delegation authority.
- **AT-IAM-012:** Explicit deny wins over applicable allow; missing scope, invalid membership, expired/revoked assignment, unsupported scope or ambiguous policy defaults to deny across every execution channel.
- **AT-IAM-013:** View/list/search access, sensitive-field access, report drill-down, export, download and bulk operations are independently tested. Export/download cannot expose rows/fields the principal cannot otherwise access.
- **AT-IAM-014:** Platform-only permissions cannot be assigned by a Tenant. Platform/support access to Tenant data requires a separate purpose-bound, scoped, expiring and auditable SupportAccessGrant.
- **AT-IAM-015:** An extension registers a namespaced, versioned permission for declared extension resources/actions; installation, activation, API credential creation or device pairing alone grants no extension or core-resource data access.
- **AT-IAM-016:** Permission deprecation/alias/version migration preserves the declared compatibility window and retirement date; unknown/retired aliases fail closed and no migration silently broadens effective authority.
- **AT-IAM-017:** The canonical v1 inventory contains only unique, registered three-part core keys in the exact `domain.resource.action` form; the inventory has no duplicate definitions, two-part shorthand keys, malformed identifiers or ambiguous aliases.
- **AT-IAM-018:** Each collection/list, search/discovery, detail, aggregation and drill-down endpoint maps to a registered key whose metadata explicitly permits that query surface; field/row filters, purpose and pagination/resource limits are enforced. No view permission implies export or sensitive-field visibility.
- **AT-IAM-019:** Permission assignment changes, explicit deny, expiry and revocation take effect at the authoritative effective time across API, web/mobile, POS, barcode, report/export/download, jobs, event handlers and integrations; stale session/cache/offline state cannot preserve access.
- **AT-IAM-020:** A permission revocation/deny racing with a protected write is coordinated at commit: the write either commits while the permission is valid or is denied; duplicate retries cannot create duplicate business effects. Security break-glass permissions cannot activate an inventory emergency override, and inventory emergency-override permissions cannot grant general security/platform access.

These tests are additive to AT-IAM-001..008 and AT-GNT-001..018. Together AT-IAM-009..020 verify the catalogue metadata and authorization behavior without implying that each permission is assigned or that each capability is enabled. The v1 catalogue lists eligible capabilities; it does not assign them to a user or enable the corresponding Tenant module.

Use stable identifiers AT-IAM-021..025 for [Domain Permission and Resource Mapping Register](../04-architecture/DOMAIN_PERMISSION_AND_RESOURCE_MAPPING_REGISTER.md):

- **AT-IAM-021:** Every registered core permission key in ARC-IAM-CAT-001 section 15.2 appears exactly once in ARC-IAM-MAP-001 under one canonical resource namespace; no unregistered key, duplicate mapping or unmapped key is present.
- **AT-IAM-022:** Every mapped resource resolves to the authoritative Tenant/Branch/location/StockLocation/person/task/financial-period/service scope and owning domain specification; missing or ambiguous ownership/scope denies access.
- **AT-IAM-023:** Each protected business lifecycle transition in the owning domain's State Transition Matrix maps to its exact action permission(s); view, capture, submit, approve, post, reverse, release, export and configure remain independent.
- **AT-IAM-024:** Each sensitive field/data category, attachment, search/list result, report, drill-down and export is mapped to the appropriate sensitive/query/export permission; parent-record access or broad report permission cannot expose unapproved fields or rows.
- **AT-IAM-025:** Before a module enters implementation, every concrete API route/command/event handler/job/device action/integration operation is registered against the resource mapping and canonical key(s); an absent or stale mapping fails closed. No speculative endpoint name is treated as a real contract.
- **AT-IAM-026:** Each sensitive data category in ARC-IAM-MAP-001 section 4 is mapped to one or more exact registered sensitive-view/download keys plus its parent-resource permission and resource scope. Ordinary record-view permission alone returns only permitted non-sensitive fields.
- **AT-IAM-027:** Sensitive data access records the actual actor, permission/scope, purpose, target data category/resource, source/channel, outcome and timestamp. Masked fields remain masked in list/search/autocomplete, summaries, analytics, exports, attachments and bulk jobs unless all exact permissions are satisfied.
- **AT-IAM-028:** Customer full service addresses/contact directions, Party contact/address details, bank/payee information, identity documents, delivery proof/GPS, payroll/salary/payslip, tax/compliance records, detailed audit events and session/device metadata are denied outside their authorized resource/purpose scope; export/download rechecks the same field/row permissions at access time.



### 5.3.1 Domain permission and approval control matrix

Use stable acceptance identifiers AT-CTRL-001..026 from [Domain Permission and Approval Control Matrix](../04-architecture/DOMAIN_PERMISSION_AND_APPROVAL_CONTROL_MATRIX.md) section 11. These checks cover authorization evaluation order, list/search/field/export scope, direct/bundle/group assignments, risk-tiered approval, single-operator usability, independent review, threshold configuration, payload/policy binding, generic approval separation, emergency override separation, policy-version lifecycle, weakening-change review and in-flight stricter/less-restrictive re-evaluation. The matrix owns the canonical test descriptions; test execution evidence is recorded here.

### 5.4 Sensitive-data boundaries
Test masking/field permissions for bank/payee details, identity documents, payroll, compensation, tax identifiers, customer personal data, payment evidence, secrets and audit investigation records. Export permission must not bypass field permissions.

### 5.5 Delegation and break-glass
Test the ODR-002 Delegated Approval constraints, 30-day maximum, scope/limit inheritance, invalidation, non-delegable actions, self-approval and revoke races. Test Emergency Override qualification, exact actor/resource/action scope, 60-minute initial/120-minute total maximum, independent extension, immutable audit and retrospective review.

### 5.6 Cross-Branch stock-task grant lifecycle and reconciliation

Use stable test identifiers AT-GNT-001..018 for the ODR-002 StockTaskAccessGrant lifecycle:

- **AT-GNT-001:** Draft, submitted, pending-approval, rejected and future-scheduled versions authorize no stock action.
- **AT-GNT-002:** A grant is limited to exactly one named worker and the approved stock-owning Branch, locations, actions, item/lot/task scope, limits and period; group/role-wide grants are rejected.
- **AT-GNT-003:** A grant cannot activate without approval by an authorized user with the required stock-owning-Branch approval permission or a currently valid delegate under ODR-002 section 16.4.
- **AT-GNT-004:** The approval is bound to the immutable GrantVersion/payload hash; stale or substituted scope/version approval cannot activate a grant.
- **AT-GNT-005:** A grant does not create the worker's independent stock action permission, active Tenant membership or workforce assignment. Each prerequisite is evaluated independently.
- **AT-GNT-006:** Effective start is inclusive and effective end exclusive; exact-boundary expiry blocks a new task and any further stock mutation with zero grace.
- **AT-GNT-007:** Revocation blocks new tasks and further stock-affecting transitions immediately across web/API/mobile/barcode/jobs/exports/integrations; notification delivery and stale cache do not extend authority.
- **AT-GNT-008:** Automatic invalidation on worker account/Tenant membership loss, independently required action permission loss, required workforce assignment expiry/revocation, target Branch deactivation or StockLocation ownership relationship change denies subsequent actions.
- **AT-GNT-009:** A material scope/limit/period/worker change cannot be edited into an approved version; it requires a new immutable version and fresh approval. A worker replacement uses a new grant request.
- **AT-GNT-010:** A successor extension must be approved before predecessor expiry and have a non-overlapping effective transition; a pending approval does not extend the current grant and a late approval cannot retroactively authorize a gap.
- **AT-GNT-011:** If a grant ends while a task is in progress, the task is held and preserves committed steps, posted quantities, custody, evidence and outstanding work; no auto-completion or rollback occurs.
- **AT-GNT-012:** Reauthorization for the same worker requires a valid current grant version for the exact remaining scope and all independent prerequisites.
- **AT-GNT-013:** Reassignment to another worker requires a new grant/approval, applicable workforce assignment and actual custody handover when goods change hands; the former worker's authority is not transferred.
- **AT-GNT-014:** Controlled cancellation/closure/reconciliation records outstanding physical custody and stock effects using linked correction/compensation evidence; original events are immutable and unresolved discrepancies remain escalated.
- **AT-GNT-015:** Revocation/expiry/invalidation racing with a stock-affecting commit results either in a commit while authorization is valid or in denial; no stock mutation commits after the ended authorization wins.
- **AT-GNT-016:** Duplicate approve/reject/revoke/extension/stock-task commands and offline replay return the prior outcome or conflict without duplicate grant versions, approval actions, stock movements or custody events.
- **AT-GNT-017:** Loss of an approver's permission after a valid committed approval does not erase the historical approval; any review finding of invalid approval requires an explicit auditable revocation.
- **AT-GNT-018:** Tenant-configured grant duration, action subset, quantity/task/evidence and review thresholds can be tightened without disabling stock-owning-Branch approval, independent permissions, zero-grace enforcement, hold/reconciliation or concurrency integrity.

## 6. Financial, quantity and stock correctness

Mandatory invariants:
- Every posted journal balances in the accounting currency/scale.
- Posted journal, invoice, payment, stock movement and finalized payroll facts are not silently overwritten.
- Reversals/corrections link to the original and preserve history.
- Retry cannot duplicate one business effect.
- Partial payment, allocation, refund and reconciliation cannot exceed available/unsettled source values.
- Quantity dimensions cannot be mixed; unit conversions preserve source value, conversion evidence/version and precision.
- Measurement tolerance is distinct from rounding.
- Stock ownership/Branch/StockLocation/lot/status remains attributable.
- Reserved, in-transit, quarantined, expired, damaged and available quantities remain distinct.
- Count capture cannot approve variance; quarantine cannot release stock; receipt cannot imply adjustment.
- Negative stock default-deny and approved exceptions are tested against product tracking profile and integrity constraints.
- Historical price, UOM, tax, rate, schedule, recipe, permission/policy and configuration snapshots reproduce the original transaction outcome.
- Tally import/export detects lossy precision, duplicate voucher, unsupported mapping and unknown external outcomes.

Use property-based or generated boundary tests for money, quantities, conversions, rounding, balance equations, reservation allocation and idempotency where useful. Avoid binary floating-point as an authoritative source.

### 6.0 Inventory ledger, internal transfer and Branch stock-attribution transfer

Use stable acceptance identifiers AT-INV-001..023 for inventory/stock correctness and ODR-012 section 16.44:

- **AT-INV-001:** Posted stock balances are reproducible from immutable StockMovement records, controlled openings, reservations and status rules; direct balance overwrite is denied.
- **AT-INV-002:** An internal transfer between two StockLocations with the same stock-owning Branch uses `inventory.stock.transfer` and leaves Branch attribution unchanged.
- **AT-INV-003:** An internal StockLocation transfer whose source and destination resolve to different stock-owning Branches is rejected, even if both locations share one physical OperatingLocation.
- **AT-INV-004:** A cross-Branch worker physically handles source-Branch stock at a shared site using the independently assigned action permission and valid StockTaskAccessGrant; stock-ledger attribution remains with the source Branch.
- **AT-INV-005:** A StockTaskAccessGrant or ordinary `inventory.stock.transfer` permission cannot create, approve, dispatch, receive, post, reconcile or reverse a Branch stock-attribution transfer.
- **AT-INV-006:** A Branch stock-attribution transfer requires a distinct immutable transfer request identifying source/destination Branches and StockLocations, product/lot/serial/container, quantity/UOM, limits, reason and applicable policy/evidence version.
- **AT-INV-007:** Source release requires `inventory.stock_attribution_transfer.approve_source` scoped to the source Branch; destination acceptance requires `inventory.stock_attribution_transfer.approve_destination` scoped to the destination Branch.
- **AT-INV-008:** Both Branch-side approvals are bound to the exact same immutable transfer version/payload. A material change to either Branch, StockLocation, lot, quantity, UOM, limit, route, effective period or custody terms requires a new version and fresh approvals.
- **AT-INV-009:** One authorized principal may satisfy both source and destination approval stages only when independently assigned both exact Branch-scoped permissions and no fixed/active maker-checker or separation-of-duties rule requires different actors.
- **AT-INV-010:** Dispatch validates current source stock, lot/status/expiry/quality holds, reservations, quantity/UOM and destination eligibility; insufficient/ineligible stock or a stale version is denied.
- **AT-INV-011:** Dispatch removes the quantity from source available stock and records the approved in-transit state while preserving source Branch attribution; dispatch alone does not create destination Branch stock attribution.
- **AT-INV-012:** Physical custody changes only when the actual receiving party accepts under the effective custody assurance profile; selecting a carrier, assigning a vehicle, dispatch timestamp or notification does not imply acceptance.
- **AT-INV-013:** Receipt captures actual receiver, time, quantity, condition, lot/container and required evidence; received quantity is distinct from accepted quantity.
- **AT-INV-014:** Only the quantity actually accepted under the approved transfer version can be posted to destination Branch attribution. Unaccepted, disputed, rejected, damaged or outstanding quantities remain separately traceable to the source/in-transit attribution and last verified custodian/location.
- **AT-INV-015:** Partial receipt/acceptance creates separate accepted and outstanding transfer-line portions; accepted quantity posts once and outstanding quantity cannot be silently credited, returned or written off.
- **AT-INV-016:** A transfer with unknown, inconsistent or legally/accounting-distinct source/destination entity relationships is held from the dependent final posting path until required ODR-003/016/017 applicability evidence is verified.
- **AT-INV-017:** A Branch stock-attribution transfer does not automatically create a sale, purchase, GST supply, invoice, payable/receivable, financial journal, legal-title change or employee/supplier liability.
- **AT-INV-018:** A custody-only handover creates no synthetic StockMovement; a stock attribution movement does not itself prove custody acceptance. Where both occur, linked movements/events reconcile exactly once.
- **AT-INV-019:** Pre-dispatch cancellation uses the exact cancel permission and preserves request/approval history. Post-dispatch work cannot be simply cancelled; it requires real return/exception/custody reconciliation.
- **AT-INV-020:** A posted stock-attribution transfer is immutable. Any correction/reversal uses a linked compensating StockMovement and the exact reverse permission; original approvals, movements and custody evidence remain available.
- **AT-INV-021:** Concurrent transfers cannot over-allocate source stock, double-claim accepted quantities or post the same source quantity to two destination Branches; source, transfer version and accepted quantity are protected at commit.
- **AT-INV-022:** Duplicate/replayed/stale/offline commands across API, mobile, barcode, job and integration channels cannot create duplicate transfer approvals, dispatches, custody handovers or stock effects; current permissions and transfer state are revalidated at commit.
- **AT-INV-023:** Valuation, cost-basis change, financial posting, write-off, adjustment, export or tax treatment remains independently permissioned and governed; stock-attribution transfer approval does not imply any of these actions.

### 6.1 Physical custody and shared-resource assurance

Test the selected custody assurance profile at each applicable operating scale:
- **AT-CST-001:** Small owner-operated workflow completes a basic single-party capture without requiring a second employee account or enterprise-only setup.
- **AT-CST-002:** Acknowledged cross-Branch/carrier handover requires the actual receiving party's acknowledgement under the selected profile; notification delivery and timeout never imply acceptance.
- **AT-CST-003:** Partial receipt records accepted and outstanding quantities separately; a rejected shipment preserves the actual custodian and known location.
- **AT-CST-004:** A custody-only event does not create a stock movement or financial posting; an actual stock location/quantity change records a separate linked movement.
- **AT-CST-005:** A missing handover response, offline device or duplicate event never creates a custody gap or phantom acceptance.
- **AT-CST-006:** Raw milk/perishable profile captures configured timestamps, container/lot, required temperature/condition and quality evidence; invalid/missing/out-of-range evidence follows the configured hold/review policy.
- **AT-CST-007:** Sensor/device readings retain device identity, source timestamp, calibration/reference and raw value; unverified/manual readings are not misrepresented as sensor-confirmed.
- **AT-CST-008:** Shared equipment use across Branches preserves asset ownership, permitted-use scope, custody, booking, cleaning/calibration/maintenance and any separately approved cost allocation.
- **AT-CST-009:** A stock-task grant expiring while stock is physically held blocks further stock mutation but preserves the last verified custodian/location until a real handover or incident resolution.
- **AT-CST-010:** Custody permissions, evidence attachments and Branch-scoped data are tested across web, API, mobile/barcode, reports, exports, jobs and integrations.
- **AT-CST-011:** A custody incident or discrepancy never automatically creates a write-off, supplier deduction, payroll deduction, liability finding or financial posting.
- **AT-CST-012:** Historical custody output reproduces the profile/configuration version and evidence available at the time.

### 6.2 Route planning, personnel/vehicle assignment and dispatch

Use stable test identifiers AT-DLV-001..030 for delivery acceptance coverage:

- **AT-DLV-001:** Owner-operated delivery can create a one-off manual Route and one DeliveryPlan/DeliveryRun without employee, reusable route-template, vehicle-registry or route-optimization setup.
- **AT-DLV-002:** A saved RouteTemplate is reused on different dates/shifts while each DeliveryRun retains its own route/manifest version.
- **AT-DLV-003:** Branch/Route/DeliveryPlan ownership mismatch is rejected.
- **AT-DLV-004:** A Route that includes a ServiceArea without an effective BranchServiceAreaAssignment is rejected.
- **AT-DLV-005:** One DeliveryPlan splits eligible source demand across multiple runs without over-allocation or duplicate stop quantities.
- **AT-DLV-006:** One accountable run assignee plus multiple assigned participants can perform distinct duties; duty labels do not grant permissions.
- **AT-DLV-007:** A participant without active permission/scope or required workforce assignment cannot be assigned or perform protected run actions.
- **AT-DLV-008:** Cross-Branch run work requires explicit delivery permission and valid workforce assignment where Tenant policy requires it; Home/Primary Branch and StockTaskAccessGrant alone grant no route access.
- **AT-DLV-009:** A walk/cycle/owner-operated run completes without a vehicle/fleet record.
- **AT-DLV-010:** A vehicle/transport resource assignment respects its effective interval, availability and configured capacity.
- **AT-DLV-011:** The same exclusive vehicle/resource cannot be concurrently booked to overlapping DeliveryRuns; conflicting runs fail closed.
- **AT-DLV-012:** Simultaneous multi-vehicle delivery is represented as separate runs under one plan with unique stop/quantity allocation.
- **AT-DLV-013:** Dispatch is denied when required participant, vehicle, driver qualification, stock, custody, temperature/hygiene or other configured readiness evidence is missing/invalid.
- **AT-DLV-014:** Planning, assigning, dispatching, executing, exception review and closing require separate permissions; one action does not imply another.
- **AT-DLV-015:** Dispatch snapshots route, stop manifest, source demand allocation, participants, vehicle/resource and policy versions; a stale version cannot dispatch.
- **AT-DLV-016:** Route optimization is optional and advisory; provider outage does not prevent manual planning or dispatch.
- **AT-DLV-017:** An optimizer cannot propose a cross-Branch Route, unauthorized ServiceArea, over-capacity resource or safety-incompatible product assignment as a valid plan.
- **AT-DLV-018:** Post-dispatch route, stop, participant, vehicle or quantity change creates a linked exception/reassignment event and preserves the original manifest.
- **AT-DLV-019:** Vehicle breakdown or driver absence holds the affected run segment; replacement is validated and records actual custody handover before resuming.
- **AT-DLV-020:** Required driver/vehicle evidence expiry or revocation blocks dispatch; upload alone does not mark evidence as verified.
- **AT-DLV-021:** A temperature/cleanliness/packaging exception follows the effective product-specific hold/review policy and never automatically writes off stock or deducts payables/payroll.
- **AT-DLV-022:** A custody-only handover does not duplicate a stock movement; when both physical custody and stock location/quantity change, the events are linked and reconcile exactly once.
- **AT-DLV-023:** An offline or stale device cannot continue a revoked/expired run assignment or commit a stale route/manifest update.
- **AT-DLV-024:** Duplicate dispatch and stop-outcome retries do not duplicate stock issue, fulfilment, cash receipt or billing source effects.
- **AT-DLV-025:** A stop/source demand allocation cannot be fulfilled by two competing runs beyond its authorized quantity.
- **AT-DLV-026:** Route deviation, delay, stop reordering and address/access exceptions preserve planned versus actual execution evidence.
- **AT-DLV-027:** A disabled participant loses access to further assigned stops while goods already held remain in the recorded custody chain until an actual handover/resolution.
- **AT-DLV-028:** A run closes only after delivered, returned, damaged/quarantined, undelivered and remaining custody quantities reconcile, or through an explicitly permissioned close-with-exceptions path.
- **AT-DLV-029:** Perishable consignment evidence retains product/lot, parties, origin/destination, timestamps, packaging, applicable vehicle/temperature and disposition data under the effective profile.
- **AT-DLV-030:** Small, growing multi-Branch and enterprise profiles execute the same route/run semantics with proportionate controls, tenant quotas and no separate code forks.

### 6.3 UOM, quantity precision, conversion and rounding acceptance

Use stable test identifiers AT-UOM-001..020 for DBR-004 / ODR-008 section 16.40:

- **AT-UOM-001:** Canonical UOM entries resolve to exactly one registered dimension, stable machine identity, symbol/formal name and catalogue version; changing labels/translations does not change dimensional semantics.
- **AT-UOM-002:** Adding, comparing, averaging or converting quantities with incompatible dimensions is rejected unless a separately approved contextual conversion rule is supplied.
- **AT-UOM-003:** Each stock-managed SKU has exactly one effective base UOM; duplicate overlapping assignments are rejected.
- **AT-UOM-004:** A Product default base UOM may be inherited only where the SKU/variant is dimensionally and operationally compatible; conflicting SKU assignment is resolved explicitly. Non-stockable services do not require a stock base UOM.
- **AT-UOM-005:** A transaction accepts only an enabled UOM and an active, applicable conversion/pack rule; original entered quantity/UOM and normalized quantity remain separately available.
- **AT-UOM-006:** Exact standard conversions (e.g. g/kg, mL/L) use the correct catalogue version and do not introduce avoidable intermediate rounding.
- **AT-UOM-007:** Milk mass-volume conversion is unavailable without approved contextual density/temperature evidence; when enabled, source and derived values plus rule/version/conditions are both retained.
- **AT-UOM-008:** Temperature conversion applies the correct offset formula, preserves source scale and does not treat temperature readings as additive stock quantities.
- **AT-UOM-009:** Pack count, declared content and derived total content remain distinct; a count of bottles/packs cannot be directly added to litres/kilograms.
- **AT-UOM-010:** Pack/unpack/case-break/repack is an explicit versioned stock/production transformation, preserves source/result identities and is idempotent.
- **AT-UOM-011:** A source device/human measurement preserves its actual input resolution, method/device, timestamp and calibration/provenance; extra stored digits do not imply extra measurement accuracy.
- **AT-UOM-012:** Source, stored, calculation, posting, display precision and business tolerance are tested independently; unsupported Tenant precision profiles fail closed.
- **AT-UOM-013:** Changing a UOM, SKU base assignment, conversion, pack definition or precision profile affects only eligible future transactions; historical results remain reproducible.
- **AT-UOM-014:** Quantity tolerance and rounding remain separate: an in-tolerance measurement is not silently overwritten or rounded to the target.
- **AT-UOM-015:** Ordinary commercial HALF_UP rounding is deterministic at declared positive/negative tie boundaries and does not depend on runtime defaults.
- **AT-UOM-016:** CGST Act section 170 nearest-rupee rounding is applied only to verified in-scope tax/other amounts under that Act; ordinary selling prices, farmer rates and stock values are not universally rounded to whole rupees.
- **AT-UOM-017:** INR monetary posting balances at paise precision; rate precision may be higher; rounding residuals are explicit, auditable and never unexplained plugs. Reversals reproduce original posted values.
- **AT-UOM-018:** Tally target-company/release UOM and currency precision is detected; unsupported precision is blocked or handled by explicitly approved mapping/rounding and reconciliation, never silently truncated.
- **AT-UOM-019:** Reports disclose unit basis, source/derived status, conversion/profile version and aggregation grain; incompatible dimensions cannot be combined.
- **AT-UOM-020:** Concurrent/retried conversion, stock posting, settlement and invoice calculations produce one deterministic effect and retain the same policy/version snapshot; configuration changes cannot alter an in-flight/finalized result.

These tests complement the existing financial, stock, measurement, processing and Tally acceptance suites; implementation must also include representative property-based and boundary vectors for supported unit families and workflow profiles.

## 7. Concurrency, idempotency and failure testing

For each critical write path, test:
- Same request repeated concurrently and sequentially.
- Same idempotency key with different semantic payload.
- Two users updating the same record/version.
- Concurrent stock issue/reservation/count/posting.
- Grant/permission expiry or revocation racing with an uncommitted operation.
- Permission scope change while a background job is queued.
- Subscription edit racing with occurrence generation.
- Duplicate bank/Tally import and webhook replay.
- Provider timeout after unknown acceptance, followed by reconciliation.
- Worker restart/process crash and retry around outbox/inbox acknowledgement.
- Partial batch success and per-item recovery.
- Closed period/posted transaction correction.
- Clock skew and effective-date boundaries.

Tests must prove safe outcomes, not assume distributed exactly-once delivery or automatic rollback.

## 8. API, event and extension compatibility

- Validate API contracts against reviewed OpenAPI sources.
- Test supported client versions and additive/breaking changes, including enum expansion, stricter validation, default changes and precision changes.
- Validate event schema versions, event identity, Tenant binding, correlation/causation and replay handling.
- Test old supported mobile clients and Tally desktop agents against backend releases.
- Test extension installation with missing permissions, incompatible version, expired credentials, excessive scope, replay, timeout and provider outage.
- Ensure an extension cannot write directly into another module's private persistence.
- Deprecation tests verify migration, alias/adapter, warning telemetry, sunset policy and no silent broadening of permission.

## 9. Configuration and customization testing

For every configuration key or supported customization:
- Type, range, unit, default and scope are valid.
- Only declared Tenant/Branch/site scopes can override.
- Locked parent policies cannot be weakened.
- Fixed invariants cannot be disabled.
- Invalid dependency combinations block publication.
- Draft, preview/simulation, approval, publish and rollback behave safely.
- Concurrent edits detect stale versions.
- Historical transactions retain the configuration snapshot used.
- Failed configuration publish leaves the previous valid version active.
- Customer custom fields/templates/reports remain Tenant-isolated and permission-controlled.
- Upgrade preserves values or provides an explicit migration with a safe fallback.

Test feature entitlement, capability activation, permission, configuration and extension grant as separate dimensions.

## 10. Mobile/offline and device testing

- Offline cache contains only minimum task data and expires under the approved policy.
- Revoked/expired permissions/grants cannot be used at server synchronization.
- Offline commands preserve original actor/device/occurred-at/correlation references and are server-revalidated.
- Duplicate sync batches do not duplicate delivery/stock/financial effects.
- Partial sync exposes per-command outcome; no silent loss.
- App restart, upgrade, device clock change, storage exhaustion, lost device and account sign-out are tested.
- Offline POS/stock posting/accounting/payment settlement remain disabled unless a separate approved workflow-specific gate is completed.

## 11. Performance, scale and Tenant fairness

Use conservative, expected and stress workload envelopes defined by ARC-SCALE-001 and ODR-027. No numeric targets are inferred from development VPS capacity.

Measure API latency percentiles by operation class; throughput/concurrent writes for POS, collection, billing, procurement, stock and accounting; queue age/throughput/retries/dead letters; report/export/forecast resource isolation; database connection/storage saturation; object upload/download/restore throughput; Tenant usage quotas/noisy-neighbour/fair scheduling; scale-out, deploy/rollback and tenant migration between pool/bridge/silo tiers.

Test overload, backpressure, queue saturation and provider outage while proving accepted money/stock transactions are not silently lost.

## 12. Accessibility, localization and usability

- Core tasks should be keyboard operable with visible focus and meaningful labels.
- Validation and errors must be understandable and associated with the relevant field/action.
- Sensitive data is not disclosed by screen-reader labels, notifications or screenshots unintentionally.
- English, Marathi and Hindi resources must support Unicode names, addresses, documents and templates.
- INR, Indian number/date presentation and IST business-date boundaries are tested separately from stored values.
- Small owner-operated journeys must not require employee creation or enterprise-only setup; large Tenants must still have advanced controls without separate product forks.

## 13. Test data, environments and reproducibility

Maintain local developer, shared integration, staging/release-candidate, load/performance and isolated recovery/restore environments. Each environment has separate credentials, data stores, storage locations and service identities. Use synthetic or de-identified data outside production.

Every test report records commit/build, contract/schema/configuration versions, environment, dataset/profile, relevant entitlements/permissions, tool versions, execution time, expected result, actual result and failures. Secrets must be synthetic or ephemeral.

## 14. Defect classification and release gates

Severity is determined by impact:
- **Blocker/Critical:** cross-Tenant disclosure, privilege escalation, financial/stock corruption, unauthorized posting, lost accepted transactions, unrecoverable data loss, secret exposure or unsafe statutory output.
- **High:** material workflow failure, duplicate money/stock effect, audit evidence loss, broken recovery, broad client incompatibility or major Tenant unfairness.
- **Medium:** important workflow degradation with safe workaround and no integrity/security compromise.
- **Low:** cosmetic or low-impact usability issue with no data/security/integrity effect.

Release gate:
1. All applicable fixed invariants and security-negative tests pass.
2. Critical financial, stock, payment, delivery and concurrency cases pass.
3. No unresolved critical/high defect without documented, time-bounded risk acceptance by authorized human governance.
4. API/event/configuration/extension compatibility checks pass.
5. Migration/rollback and backup/restore rehearsal pass for the release risk level.
6. Performance and noisy-neighbour results meet approved workload envelope.
7. Monitoring, alert ownership and rollback/runbook readiness exist.
8. Requirements traceability and acceptance evidence are updated.
9. Documentation and user/operator change notes are complete.
10. Release approver identity and decision are auditable.

No universal code-coverage percentage substitutes for risk-based release evidence. Domain teams may set coverage thresholds after test maturity and technology selection; critical business invariants require explicit tests.

## 15. Ownership and improvement

Each module has an accountable product/domain owner function and a technical test owner assigned by the delivery organization. These are operational responsibilities, not automatic application permissions. Permission to approve a release, waive a test or accept risk must be explicitly assigned and audited.

After incidents, escaped defects or major releases, update tests, requirements, runbooks and architecture decisions so the same failure class is prevented. Repeated manual regression should be automated where economically reasonable.

## 16. Related authoritative documents

FRS-001; NFR-001; ERS-001; RTM-001; ODR-002/004/005/008/015/020/026–028/033–036; ARC-PV-001; ARC-IAM-001; ARC-IAM-CAT-001; ARC-IAM-MAP-001; ARC-IAM-CTRL-001; ARC-AUTH-MATRIX-001; ARC-CONC-001; ARC-API-001; ARC-EVT-001; ARC-MOB-001; ARC-SCALE-001; ARC-SEC-001; ARC-OBS-001; ARC-DR-001.
