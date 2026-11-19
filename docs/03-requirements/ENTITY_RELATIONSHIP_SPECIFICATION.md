# Entity Relationship Specification (ERS)

**Document ID:** ERS-001  
**Status:** Proposed logical baseline — not a physical database schema  
**Product:** Dairy ERP SaaS (India)  
**Purpose:** Define canonical entities, ownership, relationships, cardinalities, integrity invariants and data boundaries before physical ERD/database design.

## 1. Scope and modelling boundary

This is a logical/conceptual entity relationship specification. It does not prescribe SQL tables, ORM models, column types, indexes, partitions, database engine, microservices or deployment topology. Those choices require separate architecture and technology evaluation.

The ERS is subordinate to the Project Vision and Scope and must remain consistent with the Canonical Business Glossary, Core Domain Entity Catalogue, Module Dependency Map, domain lifecycle specifications and FRS.

## 2. Relationship notation and cardinality

- **1 : 1** — one record relates to one record, where explicitly required.
- **1 : 0..N** — one parent may have zero or many children.
- **N : M** — many-to-many; implement through an explicit association concept with its own lifecycle where attributes/permissions exist.
- **Optional** — relationship may be absent for valid operating profiles.
- **Tenant-owned** — record must have an unambiguous tenant ownership path.
- **Branch-attributed** — branch/location is recorded when the business event is performed at or belongs to a specific operating location; do not force branch on tenant-wide records.

## 3. Global identity and ownership rules

1. Every entity has a stable internal identity independent of names, mobile numbers, external system IDs or Tally identifiers.
2. Every tenant-owned record has a provable tenant ownership path. Tenant scope is never inferred solely from a client-supplied branch ID.
3. Branch/location attribution is explicit and validated against the tenant. A branch is not automatically a legal entity, GST registration, warehouse or outlet.
4. Platform-owned records (plans, platform accounts, platform audit) are not tenant-owned. Tenant records must not be visible to platform users by default merely because they administer the SaaS.
5. Cross-tenant relationships are prohibited unless a specifically designed, audited platform capability explicitly permits them.
6. A user identity and tenant membership are distinct; the membership/assignment relationship defines tenant and branch scopes.
7. A person/personnel record is distinct from a login account. A tenant owner can operate without an employee record.
8. Master data, agreements, dated occurrences, execution records, financial postings, external mappings and analytical projections are separate entity classes.
9. Historical transactions preserve effective versions/snapshots of relevant product, unit, rate, tax, schedule and policy facts.
10. Balances are derived from immutable/append-only movement or posting histories; a mutable balance is not the sole evidence.
11. Deletion, archival, retention, legal hold and anonymization rules are not assumed here; each entity class requires lifecycle policy.
12. Any relationship that crosses tenant boundaries must be blocked by both domain validation and persistence-level integrity strategy.
13. Ordinary employees are branch-isolated by default: branch-attributed records and branch-specific relationships are accessible only within the actor's assigned branch scope. A cross-branch exception requires an explicit, auditable authorization grant. For shared-site stock work, a grant must be limited to a named worker, target Branch, shared OperatingLocation/StockLocation, permitted actions, effective period and granting authority; it exposes only minimum task/stock data and does not confer general access to the target Branch. Stock movements remain attributed to the stock-owning Branch and record the acting worker, their normal Branch context and the grant reference. Grant expiry/revocation blocks future actions without rewriting historical transactions. All-Branch visibility is granted only through explicit tenant-configurable read permissions and Branch scopes; Tenant Owner status, role title and account type confer no automatic visibility or mutation authority.
14. Shared-site stock grants are action-specific and least-privilege. A grant to receive, put away, pick, transfer or count stock does not imply permission to adjust/write off stock, approve transactions, change prices, purchase, export data or post financial entries. APIs, search, reports, exports, files, caches, jobs, analytics, mobile/barcode workflows and integrations must enforce the same scope; UI hiding alone is insufficient.

## 4. Platform and SaaS commercial entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| PlatformPrincipal | Platform | Has platform role assignments and sessions | Separate from tenant user membership |
| PlatformRole / PlatformPermission | Platform | Assigned to PlatformPrincipal | Platform permissions never imply tenant data access |
| SupportAccessGrant | Platform-governed authorization record | References one PlatformPrincipal/support case, one Tenant and exact declared resource/action/purpose scope, effective interval and approval/revocation evidence | Temporary, least-privilege access; support identity has no implicit Tenant data access and the grant is revalidated for every request |
| Tenant | Platform-governed root | Has business profile, branches, tenant memberships, configuration and SaaS subscription | Root isolation boundary |
| TenantBusinessProfile | Tenant | Belongs to one Tenant | Legal identity and operating profile are not branch |
| TenantSubscription | Tenant | References one Plan and its effective entitlement snapshot(s) | SaaS commercial subscription, not dairy customer subscription |
| Plan | Platform | Has versioned feature/limit definitions | Plan updates must not silently rewrite existing contractual snapshot |
| FeatureEntitlement | Platform/tenant snapshot | Belongs to Plan version or tenant override | Grants feature availability/limits only |
| TenantEntitlementOverride | Tenant-scoped governance | References entitlement, actor and effective interval | Must be audited and cannot weaken platform invariants |
| TenantConfiguration | Tenant | Has typed settings and versions | Configuration schema/version controlled |
| Branch | Tenant | Belongs to one Tenant; may be associated with multiple OperatingLocations | Branch is a management/operating unit, not necessarily a physical site |
| OperatingLocation | Tenant | Represents an actual physical/operational site; may be unassigned or used by one or more Branches through explicit governed, effective-dated associations | One site may support multiple operational capabilities; shared physical use does not merge branch stock or grant cross-branch access |
| OperatingLocationCapability | OperatingLocation / tenant configuration | Associates one or more operational capabilities with an OperatingLocation | Examples: retail sales, milk collection, storage, processing and packaging; multiple capabilities may coexist at one site |
| StockLocation | Tenant; associated with an explicit Branch/inventory-ownership scope and an OperatingLocation or other approved holding context | Represents a stock-holding position or subdivision within/associated with a physical site | Distinct from OperatingLocation; e.g. refrigerator, room, shelf, bin or zone. Multiple Branches may maintain separate StockLocations within one shared physical OperatingLocation; ownership, balances, reservations, movements, counts and control remain branch-attributed |
| ServiceArea | Tenant | May be served by one or more Branches through an explicit effective-dated Branch-ServiceArea association | A Branch may serve multiple ServiceAreas; reassignment is prospective and historical delivery context is immutable; geographic overlap does not grant cross-branch access |
| BusinessCalendar / ShiftDefinition | Tenant or branch override | Used by schedules, collection, delivery, attendance and reporting | Versioned/effective-dated |
| BusinessProfileCapability | Tenant/branch | Enables/configures optional capability | Entitlement and local configuration are separate |

## 5. Identity, workforce and authorization entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| Person | Privacy/identity scope to be decided | May be linked to UserAccount and PersonnelRecord | Avoid duplicate identity silos; privacy scope open |
| UserAccount | Platform identity | Has authentication factors, sessions/devices and memberships | Login credential identity |
| TenantMembership | Tenant | One current membership per UserAccount/Tenant pair; links identity to Tenant and lifecycle status | Independent Tenant relationship; one user may hold memberships in multiple Tenants without cross-Tenant data access |
| AccessGroup | Tenant | Named, Tenant-scoped group of eligible TenantMembership principals; may receive scoped PermissionAssignments | Group membership itself grants no access; bundle/permission assignments remain independently evaluated |
| GroupMembership | Tenant | Links one TenantMembership to one AccessGroup with effective dates and membership audit | Membership is only an assignment target relationship; it does not itself grant a permission or widen Tenant/Branch scope |
| PersonnelRecord | Tenant | May link to Person; belongs to one or more employment/engagement records | Not mandatory for owner-only operation |
| Engagement | Tenant | Links PersonnelRecord to branch/department, engagement type and effective dates | Employee/contractor legal classification needs review |
| PermissionBundle / Role | Optional platform starter or Tenant-configured named bundle of atomic permissions | Permission membership through versioned bundle definitions; assignment does not itself grant a scope | Bundle name/title has no independent authority; only current contents plus explicit scoped assignment contribute permissions |
| PermissionCatalogueEntry | Platform-governed atomic permission definition with stable key, owner/resource/action, allowed scopes, risk, delegation/approval, data sensitivity, audit and lifecycle metadata | Referenced by PermissionBundleVersion and PermissionAssignment; unknown/retired keys fail closed | Semantic meaning is immutable after publication; key display labels/translations are separate |
| PermissionBundleVersion | Immutable published snapshot of one PermissionBundle's explicit catalogue keys and metadata version | Belongs to a Tenant bundle or platform starter; RoleAssignment references a selected version | Bundle changes are versioned; no silent privilege widening of existing assignments |
| RoleAssignment | Tenant or platform scope | Assigns an optional PermissionBundleVersion to a principal within explicit scope/effective period | Expands only the assigned version's atomic permissions; role name/title/account type grants no authority |
| PermissionAssignment | Tenant (and platform scope only where defined) | Grants or denies one PermissionCatalogueEntry to a principal or governed access group, with explicit scope, limits, effective period and reason | Direct/group assignment is configurable only within the assigner's delegation authority; explicit deny, audit and revocation apply |
| ExtensionPermissionRegistration | Platform registry entry for a versioned extension's namespaced resource/action keys | Belongs to an approved extension identity/version and records scope, risk, data and contract metadata | Registration does not grant access; Tenant assignment and any host permission grant remain separate |
| ApprovalPolicyProfile | Tenant | Named editable starter or customer policy profile for one or more domain/risk scopes | Template name is not an authority; effective permissions remain independent |
| ApprovalPolicyVersion | Tenant | Immutable effective-dated version of one ApprovalPolicyProfile, including precedence and configuration snapshot | Published version is immutable; material changes create a new version |
| ApprovalPolicyRule | Tenant | Rule within one ApprovalPolicyVersion that matches a registered PermissionCatalogueEntry/action, resource scope, risk class and threshold conditions | Cannot grant underlying permission; fixed safeguards prevail |
| ApprovalRequirement | Tenant/workflow | Reviewer count/quorum, independence, stage order, delegability, expiry and evidence requirements matched by a policy rule | Maker-checker and non-delegable controls cannot be bypassed |
| ApprovalLimit | Tenant | Scoped amount/quantity/value ceiling for a policy/action/resource or principal-specific constraint | Effective-dated and unit-aware |
| ApprovalPolicyEvaluationSnapshot | Tenant/workflow | Immutable result of resolving every applicable ApprovalPolicyVersion/Rule for one ApprovalRequest at a specific evaluation event/sequence | Stores evaluator version, scope, threshold calculations, precedence, final controls and predecessor snapshot reference; never updated in place |
| ApprovalPolicyMatch | Tenant/workflow | Links one ApprovalPolicyEvaluationSnapshot to each matched ApprovalPolicyVersion and ApprovalPolicyRule | Retains match basis, effective dates, scope and whether the rule tightened/added a requirement |
| ApprovalDelegation | Tenant | Links delegator, delegate, approval category/action, resource/Branch scope, limits and validity | Explicit `approval.delegation.create` permission; max 30 calendar days; non-transferable, non-renewing and revocable; cannot widen underlying authority |
| ApprovalRequest | Tenant/platform | References one target resource/action, exact payload/version, its initial and any subsequent immutable ApprovalPolicyEvaluationSnapshots, initiator and approval state | Each snapshot identifies all matched versions/rules and combined requirements for one evaluation event; later stricter evaluations are linked, never overwritten; a task assignment/routing event does not grant permission or authorize the target action |
| ApprovalAction | Tenant/platform | Records one actual approver/delegate decision for a specific ApprovalRequest stage and policy-evaluation snapshot | Immutable; identifies actor, permission/scope, matched policy versions, threshold evidence, decision, reason and time |
| DeviceRegistration | Platform/tenant association | Belongs to UserAccount and optional TenantMembership | Revocable, device policy |
| UserSession | Platform identity | Belongs to UserAccount/device | Revocation and audit |

## 6. Customer, supplier and product entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| Party | Tenant | Common identity root for customer, supplier, farmer, vendor or other party roles | Avoid duplicate records when one party has multiple roles; tenant ownership does not imply unrestricted visibility to every branch employee |
| PartyRole | Tenant | Associates Party with one or more business roles | Role-specific data in extensions; branch-specific relationships/visibility must be explicit where applicable |
| CustomerAccount | Tenant | References Party; has addresses, contacts, pricing/credit policy | Customer login not in current scope |
| SupplierAccount | Tenant | References Party; may be Farmer/MilkProducer | Supplier and farmer may be same party |
| FarmerProfile | Tenant | Extends SupplierAccount with producer/collection attributes | Optional |
| PartyContact | Tenant | Belongs to Party | Multiple contacts |
| PartyAddress | Tenant | Belongs to Party; may be marked service/billing/supply address | Effective-dated and verified as needed |
| CustomerServiceAddress | Tenant | Links CustomerAccount to Address and service branch/area | Subscription may use a specific address |
| Product | Tenant/catalogue scope | Has variants/SKUs, units, tax classification and category | Sellable/procurable identity |
| ProductVariant | Tenant | Belongs to Product | Optional |
| SKU | Tenant | References product/variant and inventory identity | Uniqueness policy open |
| ProductCategory | Tenant | Parent-child hierarchy | Optional classification |
| UOMDimension | Platform-governed catalogue | Defines one canonical physical/operational dimension and compatibility rules for units | Core dimensions: mass, volume, count, time and temperature; additional dimensions require controlled registration |
| UnitOfMeasure | Platform-governed canonical catalogue entry or registered Tenant extension within an allowed dimension | References one UOMDimension; used by products, procurement, stock, sales, processing and measurements | Stable machine ID, symbol, formal name, localized labels, exact reference factor where applicable and lifecycle version; labels do not alter semantics |
| UnitConversionVersion | Platform or Tenant-governed versioned rule | References source/target UOMs, dimension, exact factor/formula, applicability, conditions and effective period | Same-dimension standard conversions use exact factors; contextual cross-dimension conversion requires approved inputs/formula; generic incompatible conversion prohibited |
| SKUBaseUOMAssignment | Tenant | Assigns one stock base UOM to one SKU for an effective interval | Exactly one active base UOM per stock-managed SKU; Product default may be inherited only where compatible; historical movements retain prior assignment |
| PackDefinitionVersion | Tenant | References SKU/package, count UOM, content quantity/UOM, packaging level and effective interval | Pack count and content remain separate; packaging transformations are explicit stock/production events |
| PrecisionProfileVersion | Platform catalogue and Tenant selection | Defines supported source/input, stored, calculation, posting and display precision for a UOM/device/workflow | Tenant selects validated supported profiles; device accuracy and business tolerance are separate |
| RoundingPolicyVersion | Platform/Domain default with permitted Tenant policy selection | Defines currency/quantity scale, rounding mode, operation order, boundary, residual treatment and effective interval | Statutory rule takes precedence; finalized transactions retain policy version |
| MeasurementEvidence | Tenant | Links a source measurement event to raw value/UOM, method/device, resolution, calibration evidence, operator and timestamp | Source observation is immutable; accepted/converted/posted quantities are separate derived facts |
| PriceList | Tenant | Has effective-dated price entries | Scope may be branch/customer/channel |
| PriceEntry | Tenant | References product/SKU, unit, price list and effective interval | Snapshot at transaction |
| TaxClassification | Controlled, effective-dated | Referenced by product/transaction and tax rule | Verified tax policy required |

## 7. Subscription, order, sales and delivery entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| CustomerSubscription | Tenant | Belongs to CustomerAccount; has service address, branch and lines | Agreement/configuration |
| SubscriptionLine | Tenant | Belongs to CustomerSubscription; references product/SKU, unit, quantity, price basis | Multiple lines per subscription |
| RecurrenceRule | Tenant | Belongs to SubscriptionLine or schedule version | Daily, every-N-days, weekdays, calendar rules |
| SubscriptionScheduleVersion | Tenant | Belongs to subscription/line; effective interval and recurrence definition | Immutable once used to generate occurrences |
| ScheduleException | Tenant | References schedule and date/range/shift | Skip, pause, holiday, override, substitution |
| ScheduleOccurrence | Tenant | Generated from subscription line and schedule version; one business date/shift | Unique generation key prevents duplicates |
| CustomerOrder | Tenant | Belongs to customer; has order lines and demand source | One-time/ad-hoc |
| OrderLine | Tenant | Belongs to order; product, quantity, unit and price snapshot | |
| SalesTransaction | Tenant | Originates from POS/order/subscription fulfilment under explicit sales policy | Source references prevent double counting |
| SalesLine | Tenant | Belongs to sales transaction | Quantity, price, tax, discount snapshots |
| POSRegister | Tenant/branch | Belongs to a branch/outlet | Optional |
| POSSession | Tenant | Belongs to register and operator | Opening/closing cash and reconciliation |
| Tender/PaymentCapture | Tenant | Belongs to sale/receipt and method | Distinct from settlement evidence |
| BranchServiceAreaAssignment | Tenant; references one Branch and one ServiceArea | Records which Branch is authorized/assigned to serve a ServiceArea for an effective period | Explicit many-to-many mapping; retain assignment history; do not infer access grants from this mapping |
| RouteTemplate / ManualRoute | Tenant; belongs to exactly one Branch | Defines a reusable or one-off manual stop sequence/route plan | Branch 1:N routes. A route may cover only ServiceAreas with effective BranchServiceAreaAssignment; a one-person business may create a one-off manual route without maintaining a reusable template |
| DeliveryPlan | Tenant; belongs to one Branch and a business date/shift/planning window | Groups demand allocation and one or more DeliveryRuns | One-run plan is supported; simple client flows may create the wrapper implicitly |
| DeliveryDemandAllocation | Tenant; references source demand line/occurrence/order, DeliveryPlan and optionally DeliveryRun | Records planned/allocated quantity and version for a run/stop | Prevents duplicate allocation/fulfilment across concurrent runs; partial allocation is explicit |
| DeliveryRun | Tenant; belongs to one Branch and one DeliveryPlan; references a Route/ManualRoute owned by that same Branch | Actual date/shift execution, immutable route/manifest snapshot and run state | Branch/Route/Plan ownership must match; assignments and vehicle are separate; preserve the dispatch snapshot |
| DeliveryRunPersonnelAssignment | Tenant; references one DeliveryRun and one authorized user/principal or explicitly scoped external party | Effective-dated participant and operational duty, including accountable run assignee, driver, delivery handler or helper | Duty label grants no permission; owner self-assignment needs no employee profile; current permissions and required workforce assignment remain independent |
| DeliveryRunTransportAssignment | Tenant; references one DeliveryRun/run segment and an optional Asset or governed transport-resource/carrier reference | Time-bounded primary vehicle/transport-resource assignment and readiness evidence | Vehicle is optional; changes are sequential and audited; simultaneous multi-vehicle splits use separate runs |
| RunExecutionEvent | Tenant; references DeliveryRun and optional stop, participant and transport assignment | Records depart/arrive, route deviation, delay, vehicle/participant replacement, hold, resume or other execution event | Append-oriented; does not rewrite the dispatched manifest |
| DeliveryStop | Tenant | Belongs to DeliveryRun and references customer/service address/demand allocation | |
| DeliveryAttempt | Tenant | Belongs to stop; records time, actor and outcome | Multiple attempts allowed |
| Fulfilment | Tenant | References source demand/occurrence/order and actual outcome | Planned and actual quantities distinct |
| FulfilmentLine | Tenant | Belongs to fulfilment; references source line and product | Delivered/returned/billable quantities |
| DeliveryEvidence | Tenant | Belongs to attempt/fulfilment/run event as applicable | Evidence profile configurable and versioned |
| SubscriptionChangeHistory | Tenant | References subscription/line and effective version | Historical audit |

## 8. Procurement, quality, processing and inventory entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| PurchaseOrder | Tenant | References supplier, branch and lines | Optional policy |
| PurchaseOrderLine | Tenant | References product/unit and expected quantity/rate | |
| GoodsReceipt | Tenant | References PO or direct receipt and location | Partial receipt supported |
| GoodsReceiptLine | Tenant | References PO line/product and accepted/rejected quantities | |
| MilkCollectionSession | Tenant | Belongs to collection point/branch and shift/date | |
| MilkCollectionEntry | Tenant | References farmer/supplier and session | Measurement event |
| MilkSample | Tenant | References collection entry or lot | |
| QualityTest / QualityResult | Tenant | Test references sample/material and versioned parameter | |
| QualityDecision | Tenant | References tested material and decision actor/policy | Accept/reject/quarantine/release |
| ProcurementRateVersion | Tenant | Effective-dated rate formula/quality bands | Snapshot used on collection |
| FarmerSettlement | Tenant | Groups accepted collection entries for a farmer/period | Calculation separate from payment |
| FarmerSettlementLine | Tenant | References collection entries/adjustments | Prevent duplicate settlement |
| ProcessingDefinitionVersion | Tenant | Defines process steps and expected input/output | Optional capability |
| ProductionBatch | Tenant | References definition version, facility and inputs/outputs | |
| ProductionInputLine | Tenant | References source stock lot and consumed quantity | |
| ProductionOutputLine | Tenant | References finished product/lot and output quantity | |
| StockLocation | Tenant | Must resolve to one explicit Branch/inventory-ownership scope and a physical holding context; multiple Branch-specific StockLocations may exist within one shared OperatingLocation | Physical co-location does not merge ownership or authorize cross-branch stock access |
| StockLot | Tenant | References product/SKU, source receipt/production and expiry/batch | Optional tracking policy |
| StockMovement | Tenant | References product, lot, from/to locations, source document and movement type | Authoritative stock quantity/location/status history; distinct from physical custody events |
| StockAttributionTransfer | Tenant | References distinct source/destination Branches, transfer version, approval stages and one or more transfer lines | Changes operational Branch stock-ledger attribution only after source release, destination acceptance and accepted-quantity posting; does not determine legal/economic title |
| StockAttributionTransferLine | Tenant | Belongs to one transfer; references product/SKU, lot/serial/container, quantity/UOM, source/destination StockLocations, accepted/rejected/outstanding quantities and linked movement references | Partial acceptance is explicit; only accepted quantity may be attributed to the destination Branch |
| StockAttributionTransfer | Tenant | References distinct source and destination Branches, transfer parties/approval stages, route/custody context and immutable transfer version | Changes operational Branch stock-ledger attribution only after source release, destination acceptance and final posting; does not determine legal title or accounting entity |
| StockAttributionTransferLine | Tenant | Belongs to one transfer; references product/SKU, lot/serial/container, quantity/UOM, source and destination StockLocations, accepted/rejected/outstanding quantities and linked movement references | Partial acceptance is explicit; only accepted quantities may post to destination attribution |
| StockAttributionTransferApproval | Tenant | References one transfer version and source or destination Branch approval stage | Exact Branch-scoped permission and approval policy snapshot required; both sides mandatory; distinct actors only where fixed/active separation-of-duties policy requires |
| StockAttributionTransferReconciliation | Tenant | References transfer/line, discrepancy, evidence and controlled resolution | Append-oriented; correction/reversal never overwrites original movement, approval or custody history |
| StockTaskAccessGrant | Tenant | Named worker, stock-owning Branch, shared OperatingLocation/StockLocation and request/approval authority | One worker only; task-specific cross-Branch authorization exception; never general Branch membership or a permission grant |
| StockTaskAccessGrantVersion | Tenant | Belongs to one StockTaskAccessGrant; immutable approved payload, action/resource/task/quantity scope, effective period, reason and policy version | Material amendments create a new version; approval binds to exact version/hash; historical versions remain immutable |
| StockTask | Tenant | References source operational work and the exact grant version used for the cross-Branch stock action | Independent task and stock ledger facts; any required workforce assignment is separate |
| StockTaskHold | Tenant | References one in-progress StockTask and the grant version/expiry/revocation/invalidation event that caused the hold | Preserves completed steps, posted effects, current custody and outstanding work; blocks further stock mutation |
| StockTaskReconciliation | Tenant | References a StockTaskHold, authorized decision actor, evidence and outcome | Records reauthorization, reassignment, controlled cancellation/closure, correction/compensation or escalation without rewriting original events |
| CustodySubject | Tenant | References stock quantity/lot, container, asset or other tracked resource | Identifies what is physically held/handled; ownership and stock attribution remain separate |
| CustodyAssignment | Tenant | Assigns a named person/service/external party as recorded custodian for a subject/scope and effective period | Does not confer stock ownership, general access or mutation permission |
| CustodyEvent | Tenant | References subject, actor/handler, custodian, location, event/action, time, evidence and policy version | Append-oriented physical custody fact; does not itself create stock or financial movement |
| CustodyHandover | Tenant | Links source/receiving parties, subject, expected/actual quantity and handover lifecycle | No automatic acceptance on timeout or notification; partial receipt/dispute is explicit |
| CustodyEvidence | Tenant | Links sensor/measurement/scan/seal/signature/document/photo/quality evidence to a custody event | Source, timestamp, device/calibration/profile and evidence class remain traceable |
| CustodyIncident | Tenant | References discrepancy, loss, damage, contamination, temperature/condition exception or dispute | Review/disposition separate from stock adjustment, financial posting or legal liability |
| SharedResourceUse | Tenant | References asset/resource, using Branch/capability, custodian, booking, usage period and evidence | Shared use does not transfer asset ownership or grant unrelated Branch access |
| StockReservation | Tenant | References demand line and stock location/lot | Not a physical issue |
| StockCountSession | Tenant | Belongs to stock location and cut-off | |
| StockCountLine | Tenant | References product/lot and counted quantity | |
| StockAdjustment | Tenant | References count/exception and approval | Creates movement |
| WastageRecord | Tenant | References product/lot/reason and source | Creates controlled movement |
| TraceabilityLink | Tenant | Connects input lots to output lots through production/transfer | Forward/backward lineage |

## 9. Billing, payments and accounting entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| BillingProfile | Tenant | Defines customer billing preferences/cycle | Policy and legal document type separate |
| BillingRun | Tenant | Period/branch/customer scope, status and run version | Idempotent |
| BillableItem | Tenant | References fulfilment/sale/adjustment and source line | No double billing |
| Invoice | Tenant/books scope | Belongs to customer and billing entity; has lines and tax snapshot | Document classification per applicability |
| InvoiceLine | Tenant | References source billable item and product/service | |
| CustomerStatement | Tenant | Periodic view of account activity | Not itself invoice |
| CreditNote / DebitNote | Tenant/books scope | References original invoice or adjustment | Does not rewrite original |
| CustomerAccountEntry | Tenant | Receivable subledger debit/credit event | Derived from posted documents |
| Receipt | Tenant | Records money received and receiving account/method | Not bank confirmation |
| ReceiptAllocation | Tenant | Allocates receipt to invoice/account items | Partial/multiple allocation |
| Refund | Tenant | References original receipt/payment and approval | Separate outflow |
| PaymentAccount | Tenant | Cash, bank, UPI, gateway or clearing account | Mask sensitive details |
| BankStatementImport | Tenant | References account, source file, profile and import batch | |
| BankStatementLine | Tenant | Belongs to import and account | External evidence |
| ReconciliationSession | Tenant | Account and period | |
| ReconciliationMatch | Tenant | Links internal transactions and statement lines | Many-to-many where supported |
| SupplierBill | Tenant/books scope | References supplier and receipt/purchase | |
| SupplierAccountEntry | Tenant | Payable subledger event | |
| SupplierPayment | Tenant | References supplier and payment account | |
| Expense | Tenant | References category, branch, vendor/claim and evidence | Expense recognition distinct from payment |
| ExpenseClaim | Tenant | References claimant and expenses/lines | Approval workflow |
| PettyCashFloat | Tenant | Belongs to branch/location and custodian | Balance from movements |
| CashMovement | Tenant | References float/account and source voucher | |
| EmployeeAdvance | Tenant | References personnel and disbursement/settlement events | |
| AccountingEntity | Tenant | Represents books/legal accounting scope | Multiple entities only after ODR-003 |
| FinancialYear / AccountingPeriod | Tenant/books | Belongs to AccountingEntity | Close/reopen history |
| AccountGroup / LedgerAccount | Tenant/books | Chart of Accounts hierarchy | |
| JournalEntry | Tenant/books | References source event/voucher type/period | Posted entry immutable |
| JournalLine | Tenant/books | Belongs to JournalEntry and LedgerAccount | Debit/credit and dimensions |
| PostingRuleVersion | Tenant/books or controlled template | Maps source event to journal lines | Effective-dated |
| CostCentre | Tenant/books | Optional analytic dimension | Not necessarily branch |
| FinancialClose | Tenant/books | Period close checklist and actor | |
| ReversalLink | Tenant/books | Links original posting and reversal | |
| OpeningBalanceBatch | Tenant/books | Controlled migration/opening entries | |
| ExternalAccountingCompany | Tenant | References Tally company identity and desktop agent | Optional |
| ExternalMasterMapping | Tenant | Maps SaaS master to external master/company | Versioned |
| ExternalVoucherMapping | Tenant | Maps source journal/voucher to Tally voucher identity | Idempotency and reconciliation |
| SyncDevice | Tenant/platform | Paired desktop agent identity | Revocable |
| SyncBatch / SyncBatchItem | Tenant | Records direction, payload, checksum, result and retry | Per-record status |
| SyncConflict | Tenant | References conflicting source/target records | Explicit resolution |

## 10. HR, payroll, compliance and asset entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| Department / Team | Tenant | Optional organizational grouping | |
| ShiftAssignment | Tenant | Links personnel to shift/location/date range | |
| AttendanceRecord | Tenant | References personnel, shift and source evidence | Corrections audited |
| LeaveType / LeaveRequest | Tenant | Leave policy and personnel request | |
| CompensationStructureVersion | Tenant | Effective-dated pay components | |
| PayrollPeriod | Tenant/books | Defines processing interval | |
| PayrollRun | Tenant | References period, pay group and calculation version | |
| PayrollLine | Tenant | References personnel and compensation snapshot | Sensitive |
| PayrollComponentLine | Tenant | Earning/deduction/contribution details | |
| Payslip | Tenant | Finalized employee document | Access restricted |
| PayrollPayment | Tenant | References payroll payable and payment account | Partial settlement possible |
| StatutoryRuleVersion | Controlled/tenant applicability | Source, jurisdiction, effective date and formula | Legal verification required |
| StatutoryLiability | Tenant/books | Generated from approved payroll/tax calculation | |
| Asset | Tenant | Belongs to location and optional cost centre; may be explicitly shared for approved use by multiple Branches | Ownership, physical custody, permitted use, maintenance and cost allocation remain distinct |
| AssetUse / ResourceBooking | Tenant | References asset/resource, Branch/capability, permitted user, time window and purpose | Availability/use record; no implicit ownership, permission or financial posting |
| MaintenancePlan / WorkOrder | Tenant | References asset, schedule, tasks and vendor/personnel | |
| ComplianceRequirement / Evidence | Tenant | Requirement applicability and attached evidence | Current-law review |
| AuditEvent | Platform or tenant | Actor, action, target, timestamp, context and outcome | Append-only policy |
| Attachment | Platform storage metadata with tenant ownership | Linked to source entity | Access/retention controls |
| ApprovalRequest / ApprovalAction | Tenant/platform | References target record, policy, actual approver and outcome; ApprovalAction may reference the ApprovalDelegation used | Immutable history; delegated actor and delegator remain separately attributable |

| Notification / DeliveryAttempt | Tenant/platform | References recipient, template, channel and source event | Delivery is not business commit |
| IntegrationConnection / Job | Tenant/platform | External provider, scoped credentials and job state | Secrets not exposed in logs |

## 11. Reporting, KPI, budget and projection entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| KPI definition/version | Platform template or tenant extension | Defines formula, grain, dimensions, date basis and source | Versioned |
| ReportDefinition | Platform/tenant | Parameters, permissions and output contract | |
| ReportRun | Tenant/platform | References definition, filters, snapshot/cut-off and requester | Async-capable |
| AnalyticalFact / Aggregate | Tenant-isolated analytical store | Derived from source operational/financial events | Not transaction source of truth |
| Dimension / DimensionMember | Platform/tenant | Branch, product, party, route, channel, cost centre etc. | Governed |
| Budget | Tenant/books | Period, scenario and owner | |
| BudgetVersion | Tenant/books | Draft/approved/revised version | Prior versions retained |
| BudgetLine | Tenant/books | Account/dimension/period target | |
| ForecastRun | Tenant | Model/version, data cut-off and horizon | |
| ForecastValue | Tenant | Dimension, period, metric and predicted value/range | Never actual |
| Scenario | Tenant | Assumption set and base reference | |
| ScenarioResult | Tenant | Computed output for scenario/version | No posting side effects |
| AssumptionSet | Tenant | Price, volume, yield, cost, collection and other assumptions | Auditable |

## 12. Principal relationship map

The following is the logical relationship spine. Optional relationships depend on tenant operating profile.

1. PlatformPrincipal 1:N platform role assignments and 0:N SupportAccessGrants. Each SupportAccessGrant identifies one target Tenant, support purpose/case, exact resource/action scope, validity period and approval/revocation evidence; it never grants global Tenant access.
2. Tenant 1:1/N TenantBusinessProfile versions; Tenant 1:N Branch, TenantMembership, TenantConfiguration and TenantSubscription history.
3. Tenant 1:N OperatingLocation; one Branch may be associated with 0:N OperatingLocations, and an OperatingLocation may be unassigned or used by one or more Branches through explicit governed, effective-dated associations. One OperatingLocation may have multiple OperationalCapabilities; StockLocation is a separate inventory-control subdivision and is not a physical OperatingLocation. Multiple Branches may maintain separate StockLocations within the same physical OperatingLocation; every StockLocation must resolve to one explicit Branch/inventory-ownership scope, and stock balances/movements/counts/reservations remain attributable to that scope. Physical sharing does not merge stock or grant cross-branch stock access.
4. Branch and ServiceArea have an approved many-to-many service-allocation relationship through an explicit effective-dated association. One Branch may serve 0:N ServiceAreas and one ServiceArea may be served by 1:N Branches according to configured assignments. Historical delivery records preserve the branch/service context applicable when they were created; changing an assignment does not rewrite completed records. This association does not grant cross-branch data access.
5. Branch 1:N Route/ManualRoute; each belongs to exactly one Branch and covers only ServiceAreas with an effective BranchServiceAreaAssignment. DeliveryPlan belongs to one Branch and groups one or more DeliveryRuns; a simple one-run experience may create the plan wrapper implicitly. Each DeliveryRun belongs to the same Branch as its DeliveryPlan and Route, and records the route/manifest version used at dispatch. Another Branch serving the same ServiceArea uses its own route and plan.
6. DeliveryPlan 1:N DeliveryDemandAllocation and 1:N DeliveryRun. Each DeliveryDemandAllocation references one source demand line and its planned/allocated quantity; split allocations across runs are explicit and their total cannot exceed the eligible source quantity except through a separately authorized exception.
7. DeliveryRun 1:N DeliveryStop and 0:N DeliveryRunPersonnelAssignment / DeliveryRunTransportAssignment / RunExecutionEvent. An active run has at most one accountable run assignee at a time; it may have additional participants with explicit operational duties. Vehicle/resource is optional; assignments are effective-dated and versioned. A run has one primary transport resource per indivisible active segment; sequential replacement is permitted with a recorded handover, while concurrent multiple-vehicle execution uses separate runs under a shared plan.
8. DeliveryRunPersonnelAssignment references a current Tenant principal or a narrowly scoped external-party assignment. Its duty label never implies permissions. Current authorization and any required workforce assignment are independent prerequisites.
9. DeliveryRunTransportAssignment references an Asset or explicitly governed transport-resource/carrier identity where used. Exclusive resource booking prevents conflicting overlapping assignments; capacity, readiness and evidence are policy-dependent.
10. UserAccount 1:N independent TenantMembership records, with at most one current membership per UserAccount/Tenant pair. A TenantMembership may have 0:N MembershipBranchAssignments, 0:N PermissionAssignments/RoleAssignments and 0:N GroupMemberships as governed by policy. AccessGroup 1:N GroupMembership; each GroupMembership references one TenantMembership and one same-Tenant AccessGroup with an effective interval. GroupMembership is an assignment-target relationship only and grants no permission. AccessGroup may receive scoped PermissionAssignments; group permission effects are evaluated using the group's current membership and explicit scope. A MembershipBranchAssignment records workforce/operating context only; it is not an authorization grant.
11. Platform 1:N PermissionCatalogueEntry; every published key has one canonical semantic definition/version and allowed scope/risk/delegation/data/audit metadata. Keys are unique and language-neutral; a key cannot be silently repurposed.
12. PermissionBundle 1:N PermissionBundleVersion; each version is an immutable set of registered PermissionCatalogueEntry keys. RoleAssignment references one explicit published bundle version, principal, scope and effective period.
13. PermissionAssignment references exactly one registered PermissionCatalogueEntry and one principal or governed access group, with allow/deny effect, scope, limits, effective interval, reason and audit/version evidence. An assignment cannot refer to an unknown, retired or platform-only key outside its permitted authority.
14. ExtensionPermissionRegistration references one approved extension identity/version and one namespaced permission definition. Registration is separate from Tenant assignment and does not grant core-resource access.
15. Party 1:N PartyRole, PartyContact and PartyAddress; CustomerAccount/SupplierAccount are role-specific account projections/extensions of Party.
16. CustomerAccount 1:N CustomerSubscription and CustomerOrder; CustomerSubscription 1:N SubscriptionLine; SubscriptionLine 1:N ScheduleVersion and ScheduleOccurrence.
17. ScheduleOccurrence / OrderLine 1:N demand allocation and fulfilment references; a source demand line may be partially fulfilled by multiple fulfilment lines only under explicit policy.
18. DeliveryStop 1:N DeliveryAttempt; successful/partial attempts may create linked Fulfilment records.
19. SupplierAccount 1:N MilkCollectionEntry and PurchaseOrder; MilkCollectionEntry N:1 MilkCollectionSession; collection entries N:1 effective ProcurementRateVersion snapshot.
20. MilkCollectionEntry 1:N MilkSample/QualityResult and at most one active settlement allocation for the same payable quantity; split settlement requires explicit line-level allocation.
21. ProductionBatch 1:N ProductionInputLine and ProductionOutputLine; StockLot may be consumed by many batches and each output lot must link to its producing batch.
22. Product/SKU 1:N StockLot and StockMovement; StockMovement references source document and stock location(s).
23. StockAttributionTransfer 1:N StockAttributionTransferLine; each transfer references one source Branch and one destination Branch within the same Tenant; each referenced StockLocation must resolve to the corresponding Branch attribution scope.
24. Each StockAttributionTransfer has source-side and destination-side ApprovalActions bound to the same immutable transfer version/payload. Both exact Branch-scoped permissions/approvals are mandatory before dispatch; final destination attribution posting requires actual accepted quantities and all applicable legal/accounting/tax gates.
25. Each StockAttributionTransferLine preserves requested, dispatched, received, accepted, rejected, disputed and outstanding quantities separately. StockMovement records source-to-transit and accepted destination attribution effects as applicable; CustodyHandover/Event is separately linked only when physical custody changes. No quantity effect is duplicated.
23. StockAttributionTransfer 1:N StockAttributionTransferLine; each transfer references one source Branch and one destination Branch within the same Tenant, and both source/destination StockLocations must resolve to the respective Branch attribution scope.
27. Each StockAttributionTransfer has source-side and destination-side ApprovalActions bound to the same immutable transfer version/payload. Both exact Branch-scoped approvals are required before the transfer may dispatch; final attribution posting requires actual accepted quantities and all applicable entity/accounting/tax gates.
28. Each StockAttributionTransferLine preserves requested, dispatched, accepted, rejected, disputed and outstanding quantities separately. StockMovement records source-to-transit and accepted destination attribution effects as applicable; CustodyHandover/Event is separately linked only where physical custody changes. No duplicate quantity effect is permitted.
29. A stock/asset/container CustodySubject may have 0:N CustodyEvents; each event records the applicable actor/handler, custodian, location and policy/evidence version. CustodyAssignment is effective-dated; one primary custodian is recorded for a subject segment at a time unless an explicit co-custody profile is used.
24. CustodyHandover 1:N linked CustodyEvents and has separately identified source/receiving custody parties. Partial receipt/dispute is represented for the accepted and outstanding subject/quantity portions; timeout does not imply acceptance.
25. CustodyEvidence N:1 CustodyEvent; CustodyIncident may reference the subject, handover and one or more events. SharedResourceUse references one resource/asset, one using Branch/capability and a bounded usage interval. Allowed Branch/capability use is an explicit effective-dated association, not inferred from physical location.
26. CustodyEvent and StockMovement may be associated through an explicit cross-reference where the same operation has both physical-custody and stock-ledger effects. Neither requires a one-to-one relationship; a custody-only event has no synthetic StockMovement.
30. SalesTransaction 1:N SalesLine; Invoice 1:N InvoiceLine; BillableItem must not be billed more than permitted quantity under billing policy.
31. Receipt 1:N ReceiptAllocation; a receipt may allocate to multiple invoices and an invoice may receive multiple receipts.
32. PaymentAccount 1:N Receipt, SupplierPayment, CashMovement and BankStatementImport as applicable.
33. BankStatementImport 1:N BankStatementLine; ReconciliationSession 1:N ReconciliationMatch; match cardinality may be one-to-one, one-to-many or many-to-one under validation.
34. Operational source event 0..1 or policy-defined N:1 JournalEntry through idempotent PostingRuleVersion; JournalEntry 1:N JournalLine; every posted journal balances.
35. ExpenseClaim 1:N ApprovalAction and ExpenseLine; approved expenses generate posting instructions; payment is separately linked.
36. PersonnelRecord 1:N Engagement, AttendanceRecord and PayrollLine over time; PayrollRun 1:N PayrollLine; PayrollLine 1:N PayrollComponentLine.
37. AccountingEntity 1:N FinancialYear, LedgerAccount, AccountingPeriod and JournalEntry; period close applies to posting scope.
38. SaaS accounting source record 0..1 active ExternalVoucherMapping per target company/export purpose; SyncBatch 1:N SyncBatchItem.
39. Source facts 1:N analytical derivations over time; KPI definition version identifies how a metric was computed.
40. Budget 1:N BudgetVersion; BudgetVersion 1:N BudgetLine; ForecastRun 1:N ForecastValue; Scenario 1:N ScenarioResult.
38. Tenant 1:N ApprovalPolicyProfile. A profile may have 1:N immutable ApprovalPolicyVersions, each with its own effective interval, status and configuration snapshot. A published version is never edited in place.
39. ApprovalPolicyVersion 1:N ApprovalPolicyRule; each rule references one or more registered PermissionCatalogueEntry/action semantics and declares scope, risk, threshold/UOM, evidence and approval requirement. ApprovalRequirement and ApprovalLimit are associated only where the matched rule requires them.
40. ApprovalRequest 1:N ApprovalPolicyEvaluationSnapshot; each request has one initial immutable snapshot at submission and may have 0:N subsequent linked re-evaluation snapshots when approval stages or final commit require current-policy validation. Snapshots are sequenced and append-only; the initial and earlier evaluations are never overwritten. Each snapshot 1:N ApprovalPolicyMatch records every contributing policy version/rule, match basis, threshold evaluation and combined requirement.
41. ApprovalRequest 1:N ApprovalAction; each action is bound to the exact source payload/version, stage, effective permission/delegation and policy-evaluation version used. Completed decisions remain immutable.
42. ApprovalDelegation 1:N ApprovalAction where a delegated decision is used; each such ApprovalAction references exactly one active delegation version and records the actual delegate and delegator separately. Non-delegated ApprovalAction has no delegation reference.

43. UOMDimension 1:N UnitOfMeasure; a UnitOfMeasure has exactly one dimension. Units with incompatible dimensions cannot be added/compared/converted through a generic conversion.
44. UnitConversionVersion references one source and one target UnitOfMeasure, with applicable product/material/workflow/location conditions and an immutable effective interval/version. Changes supersede prospectively and do not alter historical transaction results.
45. Each stock-managed SKU has one effective SKUBaseUOMAssignment at a time, referencing one UnitOfMeasure dimension-compatible with the stock identity. Product-level defaults may be inherited only where compatible; non-stockable services have no stock base-UOM requirement.
46. PackDefinitionVersion references a SKU/package, discrete count UOM and declared content quantity/UOM; a package/case hierarchy is explicit and effective-dated. Pack/unpack events reference the applicable version and preserve original and resulting quantities.
47. MeasurementEvidence N:1 source measurement event; it retains the original value/UOM, source resolution, device/method, calibration/source evidence and timestamp. Normalized, accepted, converted and posted values remain separately attributable.
48. PrecisionProfileVersion and RoundingPolicyVersion are referenced by each relevant transaction/calculation snapshot; a later profile/policy change does not rewrite historical outcomes.
49. StockTaskAccessGrant 1:N StockTaskAccessGrantVersion; each version is an immutable authorization envelope for exactly one named worker and one stock-owning Branch/resource scope. A grant has no group/role-wide authority.
50. Each submitted StockTaskAccessGrantVersion has one approval workflow bound to its exact version/payload hash; ApprovalRequest 1:N ApprovalAction records each required approval stage and the actual approver/delegate evidence. A version cannot become active until all required approvals are committed.
51. ApprovalLimit is interpreted as a scoped limit on an approval policy/action or explicit principal-specific ceiling; it is not assigned by job title or role label. A stricter principal ceiling or domain limit prevails.
52. StockTaskAccessGrantVersion 1:N StockTask references the version used for each cross-Branch task. A task's committed StockMovement and CustodyEvent references remain independently attributable.
53. A StockTaskHold references the in-progress task and the ended grant version; one hold may have 0:N StockTaskReconciliation events. Reconciliation decisions are append-oriented; the hold is closed only when physical custody, stock effects and outstanding work have a recorded outcome.

## 13. Mandatory integrity invariants

### 13.1 Tenant and scope integrity
- Tenant-owned records cannot reference a party, product, branch, user membership, stock location, journal or document owned by another tenant.
- A branch/location reference must belong to the same tenant and be active/valid for the event date.
- Branch-neutral records are allowed only where the domain defines them as tenant-wide.
- User-provided tenant/branch identifiers are never trusted without authorization and ownership validation.

### 13.2 Temporal and historical integrity
- Effective-dated rates, price lists, tax classifications, recurrence rules, posting rules, compensation and statutory rules must be selected by an explicit business timestamp/date.
- Finalized transactions preserve the version/snapshot actually used.
- Changes to master data cannot rewrite completed delivery, issued invoice, settled procurement, posted journal or finalized payroll history.

### 13.3 Quantity and stock integrity
- Every quantity-bearing fact includes a UOM/dimension and source/precision context where applicable; incompatible dimensions cannot be added, compared, averaged or generically converted.
- Every stock-managed SKU resolves to exactly one effective base UOM; Product defaults may be inherited only by compatible variants/SKUs. Non-stockable services have no stock base-UOM requirement.
- Original input/source quantity and UOM remain distinct from accepted, converted, normalized, posted and displayed quantities. MeasurementEvidence preserves source value, method/device, resolution, timestamp and calibration/source evidence where relevant.
- UnitConversionVersion is explicit, versioned and effective-dated. Cross-dimensional conversions require an approved contextual formula and required inputs; no universal milk density factor is inferred.
- PackDefinitionVersion keeps discrete pack count and content quantity/UOM separate; packaging transformations are explicit events and are not generic dimensional conversions.
- PrecisionProfileVersion and RoundingPolicyVersion are captured in relevant transaction snapshots; precision layers and business tolerance remain separate.
- Posted stock movements cannot be edited in place; corrections use reversal/adjustment movements.
- Available stock is derived from posted movements, reservations and status constraints.
- Lot traceability must remain connected through transfers and production consumption/output.

### 13.4 Financial integrity
- Money uses exact decimal representation and an explicit, versioned rounding policy; binary floating-point is not authoritative.
- Ordinary INR posting defaults to paise precision, while unit rates/intermediates may use a separately governed higher precision; a verified statutory rounding rule overrides a commercial default only within its legal scope.
- Posted monetary results retain calculation, tax/rounding policy version and reproducible inputs; journals balance exactly at posting precision.
- Posted JournalEntry contains at least one debit and credit and equal total debit/credit.
- JournalLine has one positive side (debit or credit), not both.
- Control-account postings reconcile with subledger entries.
- Posted financial records are immutable; correction uses linked reversal/adjustment.
- A source event cannot create duplicate postings when a request is retried.
- Receipt, provider confirmation, bank settlement and reconciliation are different facts/states.

### 13.5 Lifecycle and concurrency integrity
- State transitions are validated against current persisted state, actor permission and version.
- Schedule occurrence generation has a deterministic uniqueness key including subscription line, business date, shift and schedule version/policy.
- A stale update cannot overwrite newer approved/posted state silently.
- Approval actions reference the exact version/value approved; material changes after approval invalidate or restart approval as policy requires.
- Closed periods and completed batches cannot be modified without controlled reopen/correction.

### 13.6 Tally and external integration integrity
- Tally names/IDs are external mapping attributes, never SaaS primary keys.
- External mapping is company-scoped and versioned.
- One source voucher cannot be exported twice as duplicate detailed and summarized accounting events.
- Retry must check prior mapping/acknowledgement before creating a new target voucher.
- Unsupported fields, transformations and lossy mappings are reported and may be blocked by policy.
- Desktop agent pairing and company selection are explicit, revocable and audited.

### 13.7 Privacy and audit
- Sensitive personnel, bank, tax and identity attributes require field-level access classification.
- Attachments inherit source tenant/record access constraints.
- Audit history must identify actor, action, target, time, outcome and relevant correlation IDs.
- Audit and integration logs must not contain passwords, OTPs, UPI PINs, access tokens or unnecessary full financial identifiers.

## 14. Candidate uniqueness and alternate-key rules

The following are logical candidates, not final database constraints. Each requires validation against business edge cases before physical design:

- Tenant: stable tenant key/slug under platform uniqueness policy.
- Tenant membership: one active membership per user and tenant unless multiple membership records are explicitly versioned.
- Branch code: unique within tenant for active branch identity.
- Operating location code: unique within tenant or parent scope per approved taxonomy.
- SKU: unique within tenant under approved variant/location policy.
- Schedule occurrence: unique by subscription line + schedule version + business date + shift + occurrence sequence.
- POS receipt/voucher number: unique within register/branch/series and relevant period according to numbering policy.
- Journal voucher number: unique within accounting entity + voucher series + financial period as configured.
- Statement import: file checksum plus account/profile and row fingerprints for duplicate detection; identical amounts alone are not unique.
- External voucher mapping: unique by tenant + accounting entity + target company + source event identity/version + export purpose.
- Sync batch item: unique by batch + source record identity/version + direction.

## 15. Entity lifecycle and deletion policy requirements

Physical ERD must classify each entity as:
- Mutable master with version/history.
- Append-only event/ledger.
- Draft workflow record.
- Finalized immutable business document.
- Derived analytical projection.
- External mapping/technical record.
- Sensitive personal data.

For each class define create/update/delete/archive/restore, retention, legal hold, export and anonymization behavior. Hard deletion of posted accounting, stock, delivery, audit or finalized statutory evidence is prohibited except through a separately approved legal retention/deletion procedure that preserves required integrity.

## 16. Unresolved modelling decisions

1. ODR-001/003: whether tenant may own multiple legal entities/books/GST registrations and exact accounting ownership.
2. ODR-002: shared-site custody/resource accountability is specified in section 16.10/ODR-002 section 16.36; route, personnel/vehicle assignment and dispatch accountability are specified in section 16.11/ODR-002 section 16.37; the complete cross-Branch permission matrix is resolved in section 16.7. Further scenario validation and external legal/contract/insurance/vehicle-qualification applicability review remain. The approved baseline permits multiple Branches to use one physical OperatingLocation while keeping StockLocations and stock ownership/control branch-segregated. Shared-site workers may receive explicit, limited, effective-dated, auditable stock-handling grants for another Branch, without general access to that Branch's unrelated operational data. Each such grant requires approval by an authorized user holding the required stock-owning-Branch approval permission, or a currently valid delegate under section 16.4, before activation; approval is bound to the exact grant scope/version. The recommended Home-Branch/workforce-assignment boundary is in section 16.6.
3. ODR-005: the canonical product-wide atomic permission-key inventory and conceptual PermissionCatalogueEntry / PermissionBundleVersion / ExtensionPermissionRegistration entities are selected in ARC-IAM-CAT-001 section 15 and ERS sections 5/12. Remaining readiness dependencies are domain-specific approval thresholds, exact resource/state/API mapping, sensitive-field metadata and verified acceptance coverage. ODR-004 membership cardinality is resolved under ODR section 16.9.
4. ODR-008: canonical UOM dimensions, SKU base-UOM rules, pack/count separation, precision layers, conversion governance and monetary rounding hierarchy are selected in DBR-004 and ODR-008 section 16.40. Device/instrument tolerances, workflow-specific validated profiles, Legal Metrology applicability, statutory tax rules, inventory valuation and target-version Tally precision remain evidence-gated.
5. ODR-011/012: processing and inventory valuation/ownership policy.
6. ODR-014/016/017: billing legal document model, tax treatment and accounting books policy.
7. ODR-018: employee/contractor classification and payroll statutory data model.
8. ODR-020: offline local storage, sync conflict and data expiry.
9. ODR-022/023/024: KPI grain, analytics history, forecast model and budget dimension policy.
10. ODR-026: retention, archival, deletion and legal hold.
11. ODR-027/028/031: scale, sharding/partitioning, tenancy isolation tier and recovery targets.
12. Tally mapping depth: detailed versus summarized sales, inventory sync and TDL-specific fields.

## 17. ERD readiness checklist

Before physical schema design:
- [ ] Resolve or explicitly model alternatives for legal entity/books and branch/location taxonomy.
- [ ] Validate canonical Party/Customer/Supplier/Farmer identity model with realistic examples.
- [ ] Complete cardinality and optionality review for all high-volume workflows.
- [ ] Verify DBR-004 / ODR-008 section 16.40 is reflected in conceptual ERS; map each SKU base UOM, transaction UOM, contextual conversion, measurement evidence, precision profile, rounding policy version and historical snapshot. Complete device/process test vectors and external legal/accounting/Tally compatibility evidence.
- [ ] Approve stock movement and accounting posting source-of-truth contracts.
- [ ] Define lifecycle/state machine and immutable snapshot boundaries.
- [ ] Define tenant/branch foreign-reference enforcement and row-scope threat tests.
- [ ] Define uniqueness, concurrency and idempotency keys for critical events.
- [ ] Define privacy classification, retention and audit requirements.
- [ ] Validate Tally exchange mappings against a supported release proof of concept.
- [ ] Map every FRS requirement to entities and acceptance tests.
- [ ] Complete NFR workload model before indexes, partitioning and capacity design.

**No physical database schema should be generated from this ERS until the ERD readiness checklist and Gate D review are complete.**


### 16.1 ODR-002 — approval authority for cross-branch stock-task grants

A cross-branch stock-task grant for a shared OperatingLocation must not become effective until an authorized user holding the required stock-owning-Branch approval permission approves the exact grant. The approved scope identifies the worker, stock-owning/target Branch, shared OperatingLocation and/or StockLocation, permitted actions and effective period. Material scope changes require approval before use. The approving actor and approval evidence must be retained in the audit history. A role title alone does not confer approval authority.

A no-blanket-Home-Branch-consent baseline is recommended in section 16.6. A stock-task grant cannot create or alter a workforce assignment; any required schedule/duty allocation must be separately authorized. The grant remains least-privilege and does not confer unrelated target-Branch data access. The approved lifecycle requires immediate denial of new tasks and further stock mutations after revocation/expiry; completed history remains immutable; in-progress tasks are held for review and controlled reconciliation. A valid grant or authorized reassignment is required for further stock-affecting work. The recommended grant-expiry notification/zero-grace baseline is in section 16.5; channel/provider delivery remains under ODR-021. The Home-Branch/workforce-assignment boundary is recommended in section 16.6; detailed custody workflow validation and external legal/contract responsibility remain; custody baseline is specified in section 16.10/ODR-002 section 16.36; detailed grant-version/task-reconciliation baseline is in section 16.12/ODR-002 section 16.38; the complete permission matrix is resolved in section 16.7; Emergency Override and delegated approval are specified in sections 16.3 and 16.4.


### 16.2 ODR-002 — grant lifecycle and in-progress stock tasks

A revoked or expired shared-site stock-task grant immediately ceases to authorize new tasks or further stock-affecting transitions. Server-side authorization must be revalidated at every such transition; stale UI/session/cache state must never authorize a write. Completed transactions and their audit records remain immutable.

A task already in progress is held for review, preserving completed steps, quantities, custody evidence and outstanding work. It is neither auto-completed nor auto-rolled-back. Further stock-affecting work requires a currently valid grant covering the exact worker, stock scope and action, or reassignment to another currently authorized worker. The authorized user holding the required stock-owning-Branch approval permission, or a currently valid delegate under the approved ODR-002 delegated-approval policy, decides whether to reauthorize, reassign or close/cancel through controlled reconciliation. Partial physical handling and stock effects are reconciled using explicit correction/compensation records where needed; original events are never erased. Non-mutating safety/incident reporting and audit capture may continue while held.

The authorization check and stock-affecting commit must be concurrency-safe and atomic with respect to grant revocation/expiry. Idempotency and race-condition tests must cover revocation overlapping an in-flight operation. No automatic distributed rollback or exactly-once guarantee is implied. Detailed grant versioning, automatic invalidation and task reconciliation are selected in ODR-002 section 16.38 and modelled in ERS section 16.12. The recommended grant-expiry notification/zero-grace baseline is in section 16.5; channel/provider delivery remains under ODR-021. The Home-Branch/workforce-assignment boundary is specified in section 16.6; the complete permission matrix is resolved in section 16.7; Emergency Override and delegated approval are specified in sections 16.3 and 16.4.


### 16.3 ODR-002 — emergency override

An Emergency Override is a narrowly scoped, time-limited exception for an immediate threat to human safety, milk/product quality, refrigeration integrity, contamination control, physical safety or material stock loss. It is not a convenience path for routine workload or ordinary approval delays.

Any authenticated user may report an emergency, but reporting does not authorize access or stock mutation. The normal path requires approval by an authorized user holding the required approval permission for the stock-owning Branch. If that approver is unavailable and delay would create material harm, only a predesignated Emergency Authority with an explicit, current and correctly scoped emergency-activation permission may self-activate the restricted override. Designation is permission-based; a job title or general administrator status is insufficient. If no such Emergency Authority is available, no software override is permitted.

The override is limited to the named actor, stock-owning Branch, affected OperatingLocation/StockLocation, affected stock/batch/task, minimum protective actions and effective period. Permitted actions may include viewing necessary task/stock details, protective movement, emergency receiving/put-away and quarantine/isolation. It does not authorize stock adjustment/write-off, valuation or price changes, purchasing/sales, unrelated customer/supplier/financial data, exports, permission administration or self-approval. Stock ownership remains unchanged.

Initial validity is 60 minutes. One explicitly authorized extension of up to 60 minutes is allowed, with a hard maximum of 120 minutes from initial activation and no automatic renewal. Extension requires a different authorized user with the relevant emergency-extension permission; a self-activating user cannot approve their own extension.

Activation, use, denied attempts, extension, revocation and expiry must be audited with actor, normal Branch context, stock-owning Branch, location, stock/batch/task, reason/evidence, activation mode, authorization evidence, exact scope/version, timestamps, each action and quantity/custody change, correlation/idempotency references, reviewer and reconciliation outcomes. Self-activated overrides require independent retrospective review by an authorized user with the relevant review permission, targeted within one business day after the override ends.

Expiry/revocation immediately blocks new tasks and further stock mutations. Completed events remain immutable; in-progress tasks are held, preserving completed steps, quantities, custody evidence and outstanding work. Further stock mutation requires a valid ordinary grant or authorized reassignment. Partial physical handling and discrepancies are resolved through explicit correction/compensation records, never by erasing original events. Non-mutating incident reporting may continue.

Authorization must be revalidated server-side and atomically coordinated with every stock-affecting commit across APIs, mobile/barcode, jobs, retries and integrations. Expiry/revocation races, stale caches/offline clients and duplicate retries must not bypass authorization or create duplicate stock effects. No automatic distributed rollback or exactly-once guarantee is implied.


### 16.4 ODR-002 — permission-based delegated approval

An ApprovalDelegation is a temporary, explicit authorization for a named delegate to perform only specified approval action(s) within a defined Tenant/Branch/resource scope and applicable limits. It does not transfer the delegator's identity, role, general permissions or general data access.

The delegator must hold the current approval permission for the exact category/resource/scope and the separate `approval.delegation.create` permission for that same scope. The delegate must be an active authenticated user in the same Tenant and eligible for the delegated scope. The delegation itself grants only the specified approval capability; it does not silently grant general Branch membership or read access. Necessary decision information must be independently accessible to the delegate or provided through a minimum permission-filtered approval packet.

Each record identifies delegator, delegate, Tenant, Branch/resource scope, approval category/action, applicable limits, reason, effective start/end and authorization evidence. The effective approval limit is the lowest applicable limit among the delegator's current authority, the delegation and governing policy. A delegation cannot exceed or outlive the delegator's effective authority.

Maximum validity is 30 calendar days, with an exclusive end timestamp. No automatic renewal or extension is allowed; a new record requires fresh authorization. The delegator or a separately authorized user with delegation-revocation permission may revoke immediately. Loss, suspension, expiry or narrowing of the delegator's approval/delegation permission, scope or Tenant membership automatically invalidates the delegation. Suspension, expiry or revocation of the delegate's account or Tenant membership also invalidates it.

Permission/access administration, Emergency Override activation/extension/review, stock adjustments/write-offs, financial-period close/reopen and sensitive data deletion are non-delegable. Other categories are delegable only when their governing policy explicitly marks them as such. Onward/subdelegation, circular or self-delegation is prohibited. A delegate cannot approve an action they initiated or approve the creation/amendment of their own delegation. Delegation cannot bypass maker-checker, independent approval or multi-stage approval requirements.

Every delegated approval must record the actual delegate, delegator, delegation identifier/version, exact resource and immutable payload/version/hash, policy version, applicable limits, decision, timestamp and reason. Delegation validity, both parties' current status, scope and limits must be revalidated server-side and atomically with the approval commit. Revocation/expiry racing with an uncommitted approval must prevent that approval from committing; a previously committed valid approval remains immutable. Retries must be idempotent.


### 16.5 ODR-002 — recommended grant-expiry notification and zero-grace baseline

A shared-site stock-task grant ends at its exact effective end timestamp (exclusive). There is no grace period: new tasks and further stock-affecting actions are denied immediately at expiry or revocation. A delayed, failed, unread or undelivered notification never extends authorization. In-progress tasks are held under section 16.2; they are not auto-completed or rolled back.

Send an immediate activation confirmation to the assigned worker and grant creator/activating user, including the exact approved scope, actions and expiry. For grants lasting more than 24 hours, send one reminder 24 hours before expiry and one reminder one hour before expiry. For grants lasting more than one hour but less than 24 hours, send only the one-hour reminder. For grants lasting one hour or less, send no pre-expiry reminder; the activation confirmation and expiry event are sufficient. Suppress reminders when the grant has already been revoked, invalidated, superseded or expired; never send a late reminder after expiry.

Expiry, revocation and automatic invalidation notices are immediate and go to the assigned worker, grant creator and only users with the relevant current grant-review/reconciliation permission for the stock-owning Branch. Do not broadcast to all Branch users or infer a notification obligation to the worker's home Branch. Validate each recipient's active account/Tenant membership and permission to receive the notification category before sending. If access has been lost, suppress Branch, stock and task details and send only a generic access-status notice if separately permitted. Include only minimum grant/task status information; external channels must not disclose unrelated business data.

In-app notification is mandatory. Push, email or SMS may be used only where configured, permitted and consented under the tenant/user notification policy. Provider selection, templates, retry/backoff and channel preferences remain governed by ODR-021. Notification delivery is informational, not an authorization token or prerequisite for expiry enforcement. Delivery failure does not extend, restore or preserve access and cannot reverse a committed transaction.

Any extension must be a newly approved grant version effective before the current version expires. A pending request or approval does not extend the current grant. If no new version becomes effective before expiry, the current version expires and in-progress tasks are held under section 16.2. No silent extension, automatic renewal or grace period is allowed.


### 16.6 ODR-002 — recommended Home-Branch and workforce-assignment boundary

Home-Branch consent is not a blanket prerequisite for every cross-Branch shared-site stock-task grant. The stock-task grant authorizes only the named worker's specified inventory actions for the stock-owning Branch, within the approved StockLocation/task scope and effective period. It does not assign a shift or change schedule, attendance, engagement/employment terms, compensation, cost allocation or other workforce conditions.

Where the cross-Branch activity requires a workforce assignment, roster/shift change, attendance allocation, duty reassignment or cost allocation, that action must be separately authorized through the applicable workforce workflow by an authorized user with the specific workforce-assignment permission for the relevant scope. Any workforce assignment required by active tenant policy must be valid for the worker, site/Branch and time before the stock task is performed. Stock-task grant approval or ApprovalDelegation cannot substitute for this authorization.

If the stock task is already within a valid workforce assignment and does not change schedule, attendance or workforce allocation, no additional Home-Branch consent is required solely because the stock belongs to another Branch. The Home Branch controls workforce decisions only through explicit workforce permissions and workflows; it receives no stock ownership or general target-Branch visibility and has no automatic veto over each stock grant. The stock-owning Branch retains mandatory approval authority for the stock-task grant, including a currently valid delegate under section 16.4.

Any workforce assignment notification is governed by the separate workforce workflow and permission rules; it is not implied by stock-grant notifications. This is a recommended baseline, not a recorded product-owner approval.


### 16.7 ODR-002 — permission-first cross-Branch authorization

The cross-Branch stock-task action matrix and its permission-first rules are approved in ODR-002 section 16.7 and `../04-architecture/CROSS_BRANCH_PERMISSION_MATRIX.md`. A StockTaskAccessGrant is not a substitute for the worker's effective permission: the grant and the worker's independently effective permission must both cover the exact action, resource, Branch, StockLocation, task, limits and validity period. The grant provides only the approved task-specific cross-Branch exception and minimum task/stock visibility; it does not create general Branch membership or access to unrelated records.

The authenticated account identifies the actor only. Role names, job titles, seniority, account type and Tenant Owner status do not themselves grant data visibility, action capability, approval authority, export or administration. Roles are optional customer-configurable permission bundles. Tenant administrators with the relevant access-administration permission may assign, modify, restrict, expire and revoke eligible permissions within their delegated authority. Permission catalogue semantics remain stable; customer configuration controls assignment and scope. Direct/group grants, if enabled, are explicit, bounded and audited.

Read, create, update, submit, approve, post, reverse, count capture, variance approval, quarantine, release, export and access administration are distinct capabilities. Missing or ambiguous permission/scope defaults to deny. Tenant isolation, stock ownership, audit, separation of duties and concurrency integrity cannot be weakened by tenant configuration. Permission and grant revocation must be enforced across APIs, UI, mobile/barcode, reports, search, files, exports, jobs and integrations; stale caches/offline work cannot authorize later commits.


### 16.8 ODR-004 — Tenant membership and workforce Branch assignment cardinality

One global human UserAccount may have independent memberships in multiple Tenants. Each membership has its own state, effective period and audit history; membership in one Tenant never authorizes access to another Tenant. A UserAccount must have at most one current TenantMembership for a given Tenant. Historical membership changes are versioned/lifecycle events rather than duplicate simultaneous active memberships.

Within a TenantMembership, zero or more Branch/workforce assignments may be recorded. Where applicable, at most one current Home/Primary Branch may be designated at a time. These assignments provide organizational, scheduling or workforce context only; neither TenantMembership nor a Home/Branch assignment is a read or action permission. Access always requires explicit effective permission and resource scope. Cross-Branch stock-task grants remain a separate task-specific authorization. Service/integration identities are distinct and Tenant-bound. The first Tenant principal receives any onboarding capabilities through a narrow, explicit, auditable permission assignment, not an account-type or Owner bypass.


### 16.9 ODR-005 — permission assignment conceptual entities

The permission model distinguishes platform-governed PermissionCatalogueEntry definitions, optional named PermissionBundle/Role and immutable PermissionBundleVersion snapshots, scoped RoleAssignment, direct/group PermissionAssignment and optional Tenant-scoped AccessGroup/GroupMembership. A PermissionAssignment may be an explicit allow or deny and must include its principal or group target, Tenant/security boundary, registered permission key, scope, limits, effective period, reason and audit/version evidence. ExtensionPermissionRegistration is separately governed and never grants access by installation alone. Deny precedence, unknown-key denial and fail-closed conflict behavior are governed by ARC-IAM-CAT-001. The role label itself has no authority. This is a logical conceptual refinement only; no physical schema, SQL or ORM design is implied. The complete canonical key inventory v1 is in ARC-IAM-CAT-001 section 15 and ODR-005 section 16.39.

### 16.10 ODR-002 — shared-site custody and resource accountability

The recommended domain baseline is specified in `../02-domain/SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md` and ODR-002 section 16.36. Ownership, stock-owning Branch attribution, StockMovement, physical location, physical custody, actual handler, workforce assignment, permission/grant and approval are distinct facts. StockMovement remains authoritative for quantity/location/status effects; CustodyEvent records physical custody and is linked where applicable without creating duplicate stock or financial effects.

The conceptual model supports CustodySubject, CustodyAssignment, CustodyEvent, CustodyHandover, CustodyEvidence, CustodyIncident and SharedResourceUse. Handover acceptance requires the evidence defined by the effective, versioned Tenant assurance profile; timeout, missing response or notification delivery never implies acceptance. Pending/disputed handovers preserve the last verified custodian and known location. Partial receipt, rejection, damage, missing goods and condition/temperature excursions are explicit incidents with linked review and correction paths.

Custody assurance is configurable from a proportionate single-party capture to acknowledged or high-assurance evidence for cross-Branch, carrier, bulk, perishable or high-value operations. Shared facilities/equipment require explicit allowed-use, availability, custody, maintenance/cleaning/calibration and optional approved cost-allocation records. These records do not transfer ownership, grant cross-Branch data access or authorize stock/financial adjustments. Legal ownership/liability, insurance, contract and employment consequences remain outside software inference and require applicable external evidence/review.


### 16.11 ODR-002 — route execution, personnel/vehicle assignment and dispatch accountability

The recommended operational baseline is specified in `../02-domain/DELIVERY_LIFECYCLE_AND_EXCEPTION_RULES.md` section 13 and ODR-002 section 16.37. Route, DeliveryPlan, DeliveryRun, participant assignment, transport-resource assignment, dispatch authorization, execution events and physical custody are separate concepts. Route/Branch and Route/ServiceArea ownership remains fixed. A one-off manual Branch-owned route is supported so a small business need not maintain reusable route templates or a route-optimization service.

A DeliveryPlan may group several DeliveryRuns and explicit demand allocations. Each active run has one accountable run assignee and may have additional participants with operational-duty labels; the labels do not confer permissions. Vehicle assignment is optional, time-bounded and distinct from personnel assignment. Sequential vehicle replacement is recorded; simultaneous multiple-vehicle execution uses separate runs under the same plan with unique stop/quantity allocations. Capacity, resource availability and exclusivity checks apply where tracked/configured.

Dispatch is distinct from planning, assignment and execution. It validates Branch/Route/ServiceArea integrity, demand and stock allocations, current run assignments/permissions, required workforce allocation, transport resource readiness/capacity, relevant custody and product-handling evidence, and any required approval. Dispatch snapshots route, manifest, assigned people/resources and policy versions. Post-dispatch changes require linked exception/reassignment events.

Vehicle/driver qualification and food-business compliance evidence is recorded where required by Tenant policy or verified applicable rules; the product does not infer legal compliance from document upload. Exact mandatory document categories and external legal applicability remain evidence-dependent. Route optimization and GPS/telematics are optional extensions and cannot silently mutate a dispatched plan.


### 16.12 ODR-002 — detailed StockTaskAccessGrant lifecycle and task reconciliation

The recommended baseline is selected in ODR-002 section 16.38 and specified in [Open Decisions Register](../01-product/OPEN_DECISIONS_REGISTER.md). A StockTaskAccessGrant is one named worker's approved, Tenant-bound cross-Branch stock-task exception. It does not grant general Branch membership, create an independent permission, change workforce assignment or transfer stock ownership.

The logical model distinguishes:
- **StockTaskAccessGrant:** aggregate grant identity and current authorization status.
- **StockTaskAccessGrantVersion:** immutable submitted/approved authorization envelope with exact action/resource/task/product/lot/quantity/period scope, reason, version and payload hash.
- **ApprovalRequest / ApprovalAction:** existing governed approval workflow bound to the exact grant version and policy; each required stage and actual approver/delegate is retained.
- **StockTask:** operational work item referencing the grant version used.
- **StockTaskHold:** hold created when an in-progress task loses grant authority through expiry, revocation or invalidation.
- **StockTaskReconciliation:** append-oriented evidence of reauthorization, reassignment, correction/compensation, controlled closure or escalation.

The recommended grant-version authorization states are Draft, Submitted, Pending Approval, Rejected, Approved/Scheduled, Active, Superseded, Expired, Revoked and Invalidated. Effective start is inclusive and effective end exclusive. Only a fully approved version within its effective period and with the worker's independently current permission, membership, exact resource/task scope, limits and required workforce assignment can authorize a stock action. Material changes require a new immutable version and fresh approval. A worker change requires a new grant request for the replacement worker.

Grant authorization and task reconciliation are separate lifecycles. Expiry/revocation/invalidation immediately blocks new tasks and further stock-affecting transitions; completed history remains immutable. In-progress work enters a hold that preserves committed steps, quantities, custody, stock effects and outstanding work. A held task may proceed only through a currently approved grant for the same worker/scope, a new approved grant for a replacement worker with a real handover where needed, or controlled cancellation/closure and explicit reconciliation. No automatic completion, rollback, phantom custody transfer or silent balance correction is allowed.

Automatic invalidation covers loss of the named worker's active account/Tenant membership, the independently required stock-action permission, a workforce assignment required by active Tenant policy, target Branch activation or the approved stock-owning-Branch/StockLocation relationship. Loss of the original requester or later loss of the approver's permission does not retroactively erase a valid committed approval; current authority can explicitly revoke the grant and a suspected invalid approval is reviewed as a separate auditable decision.

No maximum grant duration is hardcoded by this conceptual model. Tenant policies may impose tighter duration, action, task, quantity, evidence and approval controls; zero-grace expiry/revocation, stock-owning-Branch approval, single-worker scope, independent permission, hold/reconciliation, concurrency and immutable history remain fixed. Exact lifecycle, versioning, extension, invalidation and race semantics are defined in ODR-002 section 16.38.


### 16.13 ODR-008 — canonical UOM, SKU base unit, precision and rounding conceptual model

The recommended UOM/precision baseline is specified in DBR-004 and ODR-008 section 16.40. ERS distinguishes UOMDimension, platform-governed UnitOfMeasure, UnitConversionVersion, SKUBaseUOMAssignment, PackDefinitionVersion, PrecisionProfileVersion, RoundingPolicyVersion and MeasurementEvidence. These are conceptual entities and do not prescribe physical database tables.

- UOMDimension and UnitOfMeasure are governed catalogues; each UOM belongs to one dimension. Tenant labels/custom registered units do not redefine canonical dimension or unit semantics.
- Each stock-managed SKU has one effective SKUBaseUOMAssignment at a time. Product-level default may be inherited only where dimensions/handling semantics match. A non-stockable service has no stock base UOM.
- UnitConversionVersion defines standard or contextual conversion with effective dates, applicability, evidence, precision and rounding. A generic cross-dimensional conversion is prohibited.
- PackDefinitionVersion identifies discrete count and declared content; a packaging transformation is explicit, effective-dated and auditable.
- MeasurementEvidence is linked to the source collection/quality/stock/process measurement event and retains the raw value/UOM, method/device, source resolution, operator/time and calibration/provenance where available.
- PrecisionProfileVersion defines validated source/stored/calculation/posting/display precision by UOM/device/workflow. RoundingPolicyVersion defines explicit mode, scale, order, boundary, residual and effective interval. Transaction snapshots preserve the versions used.
- Historical stock/financial transactions remain unchanged after unit, conversion, pack, precision or rounding policy changes. A SKU base-UOM change requires a prospective controlled migration/reconciliation or a new SKU where no safe conversion exists.

Remaining evidence dependencies are exact instrument accuracy/calibration/tolerances, workflow-specific process/settlement values, Legal Metrology product/activity applicability, statutory tax rounding beyond the recorded CGST Act section 170 scope, inventory valuation and target-version Tally precision. These are reviewed by their owning ODRs and do not block the conceptual ERS baseline.

### 16.14 ODR-005 — domain permission and approval control matrix

The selected logical baseline is specified in [Domain Permission and Approval Control Matrix](../04-architecture/DOMAIN_PERMISSION_AND_APPROVAL_CONTROL_MATRIX.md). It distinguishes the underlying permission for a domain action from any additional approval requirement. ApprovalPolicyProfile, ApprovalPolicyVersion, ApprovalPolicyRule and ApprovalRequirement are conceptual entities for Tenant-configurable approval workflows; they do not grant permissions.

A Tenant may define versioned policy profiles and scope them to domain action, risk, Branch/resource, amount/quantity/UOM, data sensitivity or period. Profiles may define whether approval is required, threshold calculation, independent reviewer count/quorum, stage order, maker-checker and evidence. A one-person business may use proportionate direct execution for actions where independent approval is not fixed or triggered; a larger business may require maker-checker or multi-stage approval. These profiles are editable templates, not user classes or role hierarchies.

Fixed rules prevail over Tenant policy. In particular, stock-owning-Branch approval of a cross-Branch stock-task grant, ODR-002 Emergency Override controls, non-delegable actions, independent approval where explicitly required, audit/history integrity and current permission/scope validation cannot be disabled. Generic approval workflow authority never substitutes for the exact domain permission. Every decision binds to the source payload/version and policy version and is revalidated at the commit boundary.

The v1 domain coverage and acceptance baseline are in ARC-IAM-CTRL-001. Numeric threshold values are Tenant policy values, not universal product constants; each domain must select supported values during configuration and implementation readiness. Exact API/resource/state mapping remains tied to finalized domain workflow/API contracts and is not a database-schema decision.



### 16.15 ODR-012 — Branch stock-attribution transfer

The operational baseline is selected in ODR-012 section 16.44 and defined in [Inventory, Stock Ledger and Reconciliation](../02-domain/INVENTORY_STOCK_LEDGER_AND_RECONCILIATION.md). A same-stock-owning-Branch movement between StockLocations remains an internal transfer. Cross-Branch physical handling at a shared site may change custody while leaving stock attribution with the source Branch. A change of Branch stock-ledger attribution requires a separate StockAttributionTransfer aggregate and dedicated permission family; it is not a location edit, StockTaskAccessGrant or custody handover alone.

The conceptual transfer records a distinct source Branch and destination Branch, source/destination StockLocations, exact product/lot/serial/container/UOM/quantity envelope, immutable version, source release approval, destination acceptance approval, dispatch/in-transit custody, actual received and accepted quantities, linked StockMovements, discrepancies and reconciliation. Both Branch-side approval stages are mandatory. The same principal may perform both only where it holds both independent Branch-scoped permissions and no fixed/active separation-of-duties policy requires different actors.

Only accepted quantities may be posted to destination Branch stock attribution. Disputed, rejected or outstanding quantities remain separately traceable to their actual last verified custody and source/in-transit attribution. Corrections after posting are linked compensating movements; original approvals, movements and custody events remain immutable. The model does not determine legal title, sale/purchase, GST, accounting entity, payable/receivable or financial journal treatment. Unknown/different legal or accounting entity relationships hold the dependent final posting path pending ODR-003/016/017 evidence and qualified review.

`inventory.stock.transfer` is reserved for same-stock-owning-Branch StockLocation transfer. `inventory.stock_attribution_transfer.*` is required for Branch attribution changes. StockTaskAccessGrant never authorizes this workflow.
