# Functional Requirements Specification (FRS)

**Document ID:** FRS-001  
**Status:** Proposed baseline — product-owner review required  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, approved product-owner statements, and linked domain specifications.  
**Purpose:** Define observable system capabilities and testable functional requirements before UX, API, database, and implementation design.

## 1. Scope and interpretation

This specification covers the comprehensive Dairy ERP SaaS product. It is not a commitment that every capability ships in the first release. Delivery phases may sequence implementation but must not silently remove capabilities from the approved product scope.

The system is an API-first business operations platform. Authorized dairy personnel use web and mobile clients; the SaaS platform owner uses platform administration capabilities. Customer self-service login, consumer storefront/e-commerce and customer mobile accounts are outside current scope.

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
| FRS-GEN-014 | Exports and reports MUST enforce the same tenant, branch, role and sensitive-data permissions as interactive views. | Must | Unauthorized export is denied and audited |
| FRS-GEN-015 | Business workflows MUST support owner-operated operation without requiring employee records or artificial staff roles. | Must | Owner completes setup, sale, delivery and close alone |

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
| FRS-IAM-002 | A user MAY hold multiple roles/duties and authorized scopes. | Must |
| FRS-IAM-003 | Authorization MUST evaluate tenant, branch/location, action, resource state, assignment and approval limit where applicable. | Must |
| FRS-IAM-004 | The system MUST support platform-level, tenant-wide and branch/location-scoped permissions. | Must |
| FRS-IAM-005 | Backend authorization MUST be applied to every API and background operation; hiding a UI control is insufficient. | Must |
| FRS-IAM-006 | The system MUST support configurable approval/separation-of-duties policies for high-risk actions (financial posting, refunds, stock adjustment, payroll finalization, permission changes, Tally bulk sync); whether approval is required for a given action is governed by the approved policy. | Must |
| FRS-IAM-007 | Device/session lifecycle, revocation and security event history MUST be supported. | Must |
| FRS-IAM-008 | Customer self-service identities MUST NOT be provisioned under the current approved product scope. | Must |
| FRS-IAM-009 | Ordinary employees MUST be restricted by default to their own assigned branch; cross-branch visibility requires an explicit, audited scope grant. Tenant Owner may be granted all-branch operational visibility, while cross-branch read and mutation permissions remain distinct. | Must | Branch A employee cannot retrieve Branch B data through UI, API, search, reports, exports, attachments or background jobs; owner all-branch view succeeds only with the required permissions |

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
| FRS-INV-003 | Stock balances MUST derive from an auditable stock movement ledger, not direct mutable quantity edits. | Must |
| FRS-INV-004 | Stock MUST be scoped to stock location and distinguish available, reserved, quarantined, damaged, expired and in-transit quantities. | Must |
| FRS-INV-005 | Receipts, issues, reservations, transfers, returns, counts, wastage and adjustments MUST have controlled lifecycle and audit. | Must |
| FRS-INV-006 | Batch/lot, expiry and FEFO/FIFO allocation MUST be supported where product/tenant policy requires it. | Must |
| FRS-PRD-001 | Processing capability MUST support versioned process definitions, input lots, output lots, WIP, yield, by-products, losses and quality release. | Must |
| FRS-PRD-002 | Tenants that only resell packaged goods MUST not be forced to configure processing. | Must |
| FRS-PRD-003 | Traceability MUST allow forward and backward lineage between source collection/purchase lots, processing batches and finished goods lots. | Must |

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

The following remain open and must be resolved through the Open Decisions Register rather than assumptions: legal entity/books structure (ODR-001/003); location taxonomy (ODR-002); user membership and detailed authorization (ODR-004/005); subscription cut-off and recurrence precedence (ODR-006/007); units and precision (ODR-008); quality and procurement rates (ODR-009/010); processing and stock policy (ODR-011/012); pricing/billing/tax treatment (ODR-013/016); payment verification (ODR-015); detailed CoA and accounting policy (ODR-017); labour applicability (ODR-018); mobile packaging/offline guarantees (ODR-019/020); notification providers (ODR-021); KPI catalogue/forecast horizons/budget workflow (ODR-022/024); SaaS commercial rules (ODR-025); data lifecycle (ODR-026); scale and recovery objectives (ODR-027/028); integration priorities (ODR-029); technology/deployment/localization decisions (ODR-030/032).

## 19. Approval gate

This document is a proposed requirements baseline. Product-owner approval is required before treating any proposed business policy as final. No implementation or schema generation should begin solely from this draft until Gate C and Gate D review are complete.
