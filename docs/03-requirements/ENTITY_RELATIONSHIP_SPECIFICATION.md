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
13. Ordinary employees are branch-isolated by default: branch-attributed records and branch-specific relationships are accessible only within the actor's assigned branch scope. A cross-branch exception requires an explicit, auditable authorization grant. For shared-site stock work, a grant must be limited to a named worker, target Branch, shared OperatingLocation/StockLocation, permitted actions, effective period and granting authority; it exposes only minimum task/stock data and does not confer general access to the target Branch. Stock movements remain attributed to the stock-owning Branch and record the acting worker, their normal Branch context and the grant reference. Grant expiry/revocation blocks future actions without rewriting historical transactions. Tenant Owner may receive all-branch visibility without thereby receiving every mutation or sensitive-field permission.
14. Shared-site stock grants are action-specific and least-privilege. A grant to receive, put away, pick, transfer or count stock does not imply permission to adjust/write off stock, approve transactions, change prices, purchase, export data or post financial entries. APIs, search, reports, exports, files, caches, jobs, analytics, mobile/barcode workflows and integrations must enforce the same scope; UI hiding alone is insufficient.

## 4. Platform and SaaS commercial entities

| Entity | Ownership | Core relationships | Notes |
|---|---|---|---|
| PlatformPrincipal | Platform | Has platform role assignments and sessions | Separate from tenant user membership |
| PlatformRole / PlatformPermission | Platform | Assigned to PlatformPrincipal | Platform permissions never imply tenant data access |
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
| TenantMembership | Tenant | Links UserAccount to Tenant and status | Defines tenant association |
| PersonnelRecord | Tenant | May link to Person; belongs to one or more employment/engagement records | Not mandatory for owner-only operation |
| Engagement | Tenant | Links PersonnelRecord to branch/department, engagement type and effective dates | Employee/contractor legal classification needs review |
| Role | Platform template or tenant-defined, subject to policy | Assigned through scoped RoleAssignment | Role bundle does not itself grant record scope |
| Permission | Platform catalogue | Linked to Role through RolePermission | Action/resource capability |
| RoleAssignment | Tenant or platform scope | Links principal, role, tenant and optional branch/location scope | Effective dates; ordinary employee default is own assigned branch; all-branch scope must be explicit and audited |
| DirectPermissionGrant | Tenant | Optional exceptional grant with reason/expiry | Guarded and audited |
| ApprovalLimit | Tenant | Associated with role/person/scope/category/amount | Effective-dated |
| ApprovalDelegation | Tenant | Links delegator, delegate, approval category/action, resource/Branch scope, limits and validity | Explicit `approval.delegate` permission; max 30 calendar days; non-transferable, non-renewing and revocable; cannot widen underlying authority |
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
| UnitOfMeasure | Controlled catalogue/tenant extension | Used by products, procurement, stock, sales and processing | Unit conversion/precision policy open |
| UnitConversion | Controlled/tenant scoped | Defines valid conversion between units | Versioned where changes affect calculations |
| PackDefinition | Tenant | References product/SKU and content unit/quantity | Pack content differs from selling unit |
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
| RouteTemplate | Tenant; belongs to exactly one Branch | Defines reusable/planned stop sequence | Branch 1:N RouteTemplate. A Route may cover one or more ServiceAreas only where its owning Branch has an effective BranchServiceAreaAssignment; no implicit cross-branch route sharing |
| DeliveryRun | Tenant; references one Branch and a RouteTemplate owned by that same Branch | References date, shift, route/dispatch plan and assigned personnel | Branch/Route ownership must match; preserve route/plan version and execution context in the historical run |
| DeliveryStop | Tenant | Belongs to DeliveryRun and references customer/service address/demand | |
| DeliveryAttempt | Tenant | Belongs to stop; records time, actor and outcome | Multiple attempts allowed |
| Fulfilment | Tenant | References source demand/occurrence/order and actual outcome | Planned and actual quantities distinct |
| FulfilmentLine | Tenant | Belongs to fulfilment; references source line and product | Delivered/returned/billable quantities |
| DeliveryEvidence | Tenant | Belongs to attempt/fulfilment | Evidence policy configurable |
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
| StockMovement | Tenant | References product, lot, from/to locations, source document and movement type | Authoritative stock history |
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
| Asset | Tenant | Belongs to location and optional cost centre | |
| MaintenancePlan / WorkOrder | Tenant | References asset, schedule, tasks and vendor/personnel | |
| ComplianceRequirement / Evidence | Tenant | Requirement applicability and attached evidence | Current-law review |
| AuditEvent | Platform or tenant | Actor, action, target, timestamp, context and outcome | Append-only policy |
| Attachment | Platform storage metadata with tenant ownership | Linked to source entity | Access/retention controls |
| ApprovalRequest / ApprovalAction | Tenant/platform | References target record, policy, approver and outcome | Immutable history |
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

1. PlatformPrincipal 1:N platform role assignments.
2. Tenant 1:1/N TenantBusinessProfile versions; Tenant 1:N Branch, TenantMembership, TenantConfiguration and TenantSubscription history.
3. Tenant 1:N OperatingLocation; one Branch may be associated with 0:N OperatingLocations, and an OperatingLocation may be unassigned or used by one or more Branches through explicit governed, effective-dated associations. One OperatingLocation may have multiple OperationalCapabilities; StockLocation is a separate inventory-control subdivision and is not a physical OperatingLocation. Multiple Branches may maintain separate StockLocations within the same physical OperatingLocation; every StockLocation must resolve to one explicit Branch/inventory-ownership scope, and stock balances/movements/counts/reservations remain attributable to that scope. Physical sharing does not merge stock or grant cross-branch stock access.
4. Branch and ServiceArea have an approved many-to-many service-allocation relationship through an explicit effective-dated association. One Branch may serve 0:N ServiceAreas and one ServiceArea may be served by 1:N Branches according to configured assignments. Historical delivery records preserve the branch/service context applicable when they were created; changing an assignment does not rewrite completed records. This association does not grant cross-branch data access.
5. Branch 1:N RouteTemplate; each RouteTemplate belongs to exactly one Branch. A RouteTemplate may cover one or more ServiceAreas only where its owning Branch has an effective BranchServiceAreaAssignment. RouteTemplate is not shared across Branches by implication; another Branch serving the same ServiceArea uses its own RouteTemplate. Each DeliveryRun references a Branch and a RouteTemplate owned by that same Branch; mismatch is invalid. Completed runs retain the route/plan version and operational context used at execution.
6. UserAccount 1:N TenantMembership; TenantMembership 1:N RoleAssignment and DeviceRegistration as policy allows.
7. Party 1:N PartyRole, PartyContact and PartyAddress; CustomerAccount/SupplierAccount are role-specific account projections/extensions of Party.
8. CustomerAccount 1:N CustomerSubscription and CustomerOrder; CustomerSubscription 1:N SubscriptionLine; SubscriptionLine 1:N ScheduleVersion and ScheduleOccurrence.
9. ScheduleOccurrence / OrderLine 1:N demand allocation and fulfilment references; a source demand line may be partially fulfilled by multiple fulfilment lines only under explicit policy.
10. DeliveryRun 1:N DeliveryStop; DeliveryStop 1:N DeliveryAttempt; successful/partial attempts may create linked Fulfilment records.
11. SupplierAccount 1:N MilkCollectionEntry and PurchaseOrder; MilkCollectionEntry N:1 MilkCollectionSession; collection entries N:1 effective ProcurementRateVersion snapshot.
12. MilkCollectionEntry 1:N MilkSample/QualityResult and at most one active settlement allocation for the same payable quantity; split settlement requires explicit line-level allocation.
13. ProductionBatch 1:N ProductionInputLine and ProductionOutputLine; StockLot may be consumed by many batches and each output lot must link to its producing batch.
14. Product/SKU 1:N StockLot and StockMovement; StockMovement references source document and stock location(s).
15. SalesTransaction 1:N SalesLine; Invoice 1:N InvoiceLine; BillableItem must not be billed more than permitted quantity under billing policy.
16. Receipt 1:N ReceiptAllocation; a receipt may allocate to multiple invoices and an invoice may receive multiple receipts.
15. PaymentAccount 1:N Receipt, SupplierPayment, CashMovement and BankStatementImport as applicable.
16. BankStatementImport 1:N BankStatementLine; ReconciliationSession 1:N ReconciliationMatch; match cardinality may be one-to-one, one-to-many or many-to-one under validation.
17. Operational source event 0..1 or policy-defined N:1 JournalEntry through idempotent PostingRuleVersion; JournalEntry 1:N JournalLine; every posted journal balances.
18. ExpenseClaim 1:N ApprovalAction and ExpenseLine; approved expenses generate posting instructions; payment is separately linked.
19. PersonnelRecord 1:N Engagement, AttendanceRecord and PayrollLine over time; PayrollRun 1:N PayrollLine; PayrollLine 1:N PayrollComponentLine.
20. AccountingEntity 1:N FinancialYear, LedgerAccount, AccountingPeriod and JournalEntry; period close applies to posting scope.
21. SaaS accounting source record 0..1 active ExternalVoucherMapping per target company/export purpose; SyncBatch 1:N SyncBatchItem.
22. Source facts 1:N analytical derivations over time; KPI definition version identifies how a metric was computed.
23. Budget 1:N BudgetVersion; BudgetVersion 1:N BudgetLine; ForecastRun 1:N ForecastValue; Scenario 1:N ScenarioResult.

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
- Quantity values must include unit and precision context.
- Unit conversion must be explicit; incompatible units cannot be added or compared.
- Posted stock movements cannot be edited in place; corrections use reversal/adjustment movements.
- Available stock is derived from posted movements, reservations and status constraints.
- Lot traceability must remain connected through transfers and production consumption/output.

### 13.4 Financial integrity
- Money uses exact decimal representation and an explicit rounding policy.
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
2. ODR-002: detailed shared-site governance, operational accountability, complete permission matrix and exception/revocation workflows; dispatch responsibility, driver/vehicle assignment and route execution policies also require separate workflow specification. The approved baseline permits multiple Branches to use one physical OperatingLocation while keeping StockLocations and stock ownership/control branch-segregated. Shared-site workers may receive explicit, limited, effective-dated, auditable stock-handling grants for another Branch, without general access to that Branch's unrelated operational data. Each such grant requires approval by an authorized user holding the required stock-owning-Branch approval permission before activation; approval is bound to the exact grant scope/version. Whether the worker's home Branch must also consent remains undecided.
3. ODR-004/005: cross-tenant user identity, membership multiplicity, role scope and permission delegation.
4. ODR-008: supported UOM, precision, conversion and rounding.
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
- [ ] Approve unit/quantity precision and monetary rounding policy.
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

The worker's home-Branch consent is not yet decided and must not be treated as an implicit prerequisite or as categorically unnecessary. The grant remains least-privilege and does not confer unrelated target-Branch data access. The approved lifecycle requires immediate denial of new tasks and further stock mutations after revocation/expiry; completed history remains immutable; in-progress tasks are held for review and controlled reconciliation. A valid grant or authorized reassignment is required for further stock-affecting work. Notifications/grace periods, home-Branch consent, complete permission matrix and detailed accountability remain unresolved; Emergency Override and the limited delegated-approval policy are approved in sections 16.3 and 16.4.


### 16.2 ODR-002 — grant lifecycle and in-progress stock tasks

A revoked or expired shared-site stock-task grant immediately ceases to authorize new tasks or further stock-affecting transitions. Server-side authorization must be revalidated at every such transition; stale UI/session/cache state must never authorize a write. Completed transactions and their audit records remain immutable.

A task already in progress is held for review, preserving completed steps, quantities, custody evidence and outstanding work. It is neither auto-completed nor auto-rolled-back. Further stock-affecting work requires a currently valid grant covering the exact worker, stock scope and action, or reassignment to another currently authorized worker. The authorized user holding the required stock-owning-Branch approval permission decides whether to reauthorize, reassign or close/cancel through controlled reconciliation. Partial physical handling and stock effects are reconciled using explicit correction/compensation records where needed; original events are never erased. Non-mutating safety/incident reporting and audit capture may continue while held.

The authorization check and stock-affecting commit must be concurrency-safe and atomic with respect to grant revocation/expiry. Idempotency and race-condition tests must cover revocation overlapping an in-flight operation. No automatic distributed rollback or exactly-once guarantee is implied. Notification/grace-period policy, home-Branch consent, complete permission matrix and detailed accountability remain open; Emergency Override and delegated approval are approved in sections 16.3 and 16.4.


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

The delegator must hold the current approval permission for the exact category/resource/scope and the separate `approval.delegate` permission for that same scope. The delegate must be an active authenticated user in the same Tenant and eligible for the delegated scope. The delegation itself grants only the specified approval capability; it does not silently grant general Branch membership or read access. Necessary decision information must be independently accessible to the delegate or provided through a minimum permission-filtered approval packet.

Each record identifies delegator, delegate, Tenant, Branch/resource scope, approval category/action, applicable limits, reason, effective start/end and authorization evidence. The effective approval limit is the lowest applicable limit among the delegator's current authority, the delegation and governing policy. A delegation cannot exceed or outlive the delegator's effective authority.

Maximum validity is 30 calendar days, with an exclusive end timestamp. No automatic renewal or extension is allowed; a new record requires fresh authorization. The delegator or a separately authorized user with delegation-revocation permission may revoke immediately. Loss, suspension, expiry or narrowing of the delegator's approval/delegation permission, scope or Tenant membership automatically invalidates the delegation. Suspension, expiry or revocation of the delegate's account or Tenant membership also invalidates it.

Permission/access administration, Emergency Override activation/extension/review, stock adjustments/write-offs, financial-period close/reopen and sensitive data deletion are non-delegable. Other categories are delegable only when their governing policy explicitly marks them as such. Onward/subdelegation, circular or self-delegation is prohibited. A delegate cannot approve an action they initiated or approve the creation/amendment of their own delegation. Delegation cannot bypass maker-checker, independent approval or multi-stage approval requirements.

Every delegated approval must record the actual delegate, delegator, delegation identifier/version, exact resource and immutable payload/version/hash, policy version, applicable limits, decision, timestamp and reason. Delegation validity, both parties' current status, scope and limits must be revalidated server-side and atomically with the approval commit. Revocation/expiry racing with an uncommitted approval must prevent that approval from committing; a previously committed valid approval remains immutable. Retries must be idempotent.
