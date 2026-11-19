# Dairy ERP Module Dependency Map

**Status:** Working baseline — logical dependencies  
**Purpose:** Identify business-level dependencies and shared capabilities. This is not a microservice decomposition or implementation sequence.

## 1. Dependency principles

- A dependency means one capability consumes or relies on another capability's business data or outcome.
- It does not mean the dependent module must be implemented in the same release or deployed separately.
- Shared platform capabilities should be designed once and consistently reused.
- Avoid circular ownership of master data and transaction posting.
- A source transaction should remain authoritative; reports and projections consume validated records rather than becoming transaction masters.
- Optional capabilities must not be forced on tenants whose operating profile does not require them.

## 2. Shared foundation capabilities

| Shared capability | Used by | Responsibility |
|---|---|---|
| Tenant and business context | All tenant modules | Establish tenant boundary and business identity |
| Branch/location context | Operations, stock, POS, delivery, reporting | Resolve authorized operating scope |
| Identity and authorization | All protected actions | Authenticate principal and enforce permission/scope |
| Product/unit catalogue | Procurement, inventory, sales, subscriptions, processing | Common product, unit, conversion, and pack definitions |
| Customer master | Subscription, order, POS, billing, delivery, reporting | Customer identity and account relationships |
| Supplier/farmer master | Procurement, purchase, payable, quality | Supplier identity and terms |
| Pricing/rate engine | Sales, subscriptions, procurement, billing, processing cost | Effective-dated pricing/rate calculations |
| Calendar/shift/schedule | Procurement, delivery, attendance, reports | Business date, shift, recurrence, and cut-off context |
| Document/attachment service | Compliance, procurement, finance, HR, quality | Controlled evidence/document storage |
| Audit/event history | All material workflows | Traceability of actions and state changes |
| Notification/workflow | Approvals, reminders, exceptions | Routing and delivery of operational messages |
| Financial posting/ledger | Sales, billing, procurement, payroll, expenses, payments | Consistent financial effects and balances |
| Stock ledger | Purchase, procurement, processing, POS, delivery, transfer | Authoritative stock movement and valuation inputs |
| Custody and shared-resource accountability | Inventory, procurement, milk collection, transport, processing, delivery, asset/maintenance | Physical custody, evidence-backed handover, shared resource use and incidents; linked to but independent from stock and financial ledgers |
| Reporting data foundation | Reports, BI, forecast, budget | Validated historical and current facts |

## 3. Logical module dependencies

| Module/capability | Depends on | Provides to |
|---|---|---|
| SaaS Platform Administration | Platform identity, plan catalogue, billing/entitlement policy | Tenant provisioning and feature access |
| Tenant/Business Setup | SaaS tenant, identity | Branch, configuration, legal/compliance profiles |
| Branch/Location Management | Tenant/business context | POS, inventory, procurement, processing, delivery, HR, reports |
| User/Role/Permission Management | Tenant, branch/location model | All modules and APIs |
| Customer Management | Tenant, branch, contact/address | Subscription, sales, billing, delivery |
| Supplier/Farmer Management | Tenant, branch/procurement scope | Milk procurement, purchase, payable, quality |
| Product/Unit Catalogue | Tenant configuration, unit policy | Procurement, processing, stock, sales, subscription, reporting |
| Pricing and Rate Management | Product/unit, customer/supplier context, effective dates | Procurement, sales, subscription, billing, margin analysis |
| Subscription Management | Customer, product, pricing, schedule/calendar, service area | Demand generation, delivery, billing |
| Order Management | Customer, product, pricing, availability | Picking, delivery, billing, reporting |
| POS/Sales | Product, price, branch/register, stock, payment | Stock ledger, receipts, receivables, reports |
| Milk Procurement | Supplier/farmer, collection point, quantity/unit, quality/rate | Raw-milk stock, supplier payable, processing, reports |
| Milk Collection and Farmer Settlement | Supplier/farmer, collection location, measurement, sample/test, rate chart | Accepted raw-milk stock, farmer payable/settlement, quality and procurement analytics |
| Purchased Goods Procurement | Supplier, product, purchase/receipt, tax/accounting policy | Stock ledger, supplier payable, reports |
| Quality and Testing | Product/material, supplier, sample, equipment, quality policy | Procurement acceptance, processing release, compliance |
| Processing and Packaging | Raw-material stock, quality, versioned process definitions, equipment where used | WIP, finished goods, batch lineage, inventory, yield and cost analysis |
| Inventory/Warehouse | Product/unit, Branch-owned StockLocations, source transactions, lot policy, custody and approval policies | Stock ledger, availability, reservations, same-Branch internal transfers, separately governed Branch stock-attribution transfers, valuation inputs and reports |
| Batch/Lot Traceability | Supplier/collection lots, inventory movements, process batches | Expiry control, recalls, downstream traceability and compliance evidence |
| Stock Count/Reconciliation | Stock ledger, location, count sessions, transaction cut-offs | Approved variance adjustments and inventory accuracy metrics |
| Distribution/Delivery | Subscription/order, customer/address, Branch-owned Route/ServiceArea, DeliveryPlan, demand allocations, stock, workforce, optional transport resource and product-handling profile | Dispatch, stop execution, custody/handovers, fulfilment, exceptions, billing, vehicle/participant utilization and performance reports |
| Billing/Receivables | Fulfilment, sales, price/tax policy, customer account, adjustments | Customer balance, statements/invoices, finance, reports |
| Payments/Reconciliation | Receipt/payment source, account/cash custody, open items | Allocated settlement, finance, customer/supplier balance |
| Accounting/Finance | Financial events, chart of accounts, posting rules, payment reconciliation | Double-entry journals, subledger reconciliation, financial statements and audit reports |
| Expense/Petty Cash | Vendor/employee, branch/custodian, approval policy, cash/bank accounts, budget | Expense postings, reimbursements, petty-cash movements and variance reports |
| Cash/Bank Reconciliation | Cash/bank account registry, receipts/payments, statement evidence, import profiles | Reconciliation matches, exceptions, cash/bank control and settlement reports |
| Payroll | Employee/engagement, attendance, compensation versions, statutory rule registry | Payslips, employee liabilities, payroll journals and labour-cost analytics |
| Tax Configuration/Reporting | Tenant tax profile, product classification, transaction facts, effective-dated tax rules | Tax snapshots, statutory registers, tax-to-GL reconciliation and export data |
| Budgeting/Financial Analytics | Posted ledger, reconciled operational facts, KPI definitions, dimensions and assumptions | Budget versions, variance reports, forecasts, scenarios and projections |
| External Accounting Integration (Tally) | Accounting ledger, mapping registry, voucher/field mapping specification, sync agent, company identity | Tally master/voucher exchange, audit packages, compatibility and reconciliation results |
| HR/Payroll | Employee records, shifts/attendance, compensation/policy | Payroll expense, workforce availability, reports |
| Asset/Maintenance | Asset register, location, work orders, schedules, SharedResourceUse and custody evidence | Availability, custody, maintenance/cleaning/calibration, downtime and approved cost allocation |
| Quality/Compliance | Applicable business profile, facility/process/equipment, custody event and condition evidence | Holds/releases, temperature/condition exceptions, corrective actions, audit and compliance reporting |
| Reporting/MIS | Authorized operational/financial facts | Dashboards, period reports, management review |
| BI/Analytics | Validated historical facts, dimensions, KPI definitions | Trends, drill-down, segmentation, management insight |
| Forecasting/Projections | Historical data, known future events, assumptions, model validation | Demand/procurement/cash/stock/staffing forecasts |
| Budgeting/Scenario Planning | Actuals, chart/dimensions, assumptions, forecasts | Budget versions, variance, what-if decisions |
| Integrations/API Management | Stable domain contracts, auth, tenant context, audit | External provider/system connectivity |

### 3.1 Inventory transfer dependency boundaries

- Same-stock-owning-Branch StockLocation transfer uses `inventory.stock.transfer` and does not change Branch stock-ledger attribution.
- Cross-Branch stock handling at a shared OperatingLocation can change physical custody while the stock remains attributed to its owning Branch; it depends on the exact stock permissions, StockTaskAccessGrant where required and custody workflow.
- A change of Branch stock-ledger attribution depends on the separate StockAttributionTransfer workflow, source/destination Branch approvals, immutable transfer envelope, accepted-quantity-only posting and legal/accounting/tax applicability checks. It does not automatically create sale/purchase or financial postings.
- Stock ledger remains authoritative for quantity/location/status/attribution effects. Custody records are distinct and linked only where physical custody changes.

## 4. High-impact business dependency chains

### 4.1 Subscription-to-cash
Customer → Subscription → Schedule occurrence → Demand → Stock allocation → Delivery outcome → Billable fulfilment → Billing → Receivable → Receipt → Reconciliation → Financial posting → MIS/analytics.

### 4.2 Farmer procurement-to-finished goods
Supplier/farmer → Collection → Quantity/sample/test → Acceptance → Rate/payable → Raw milk stock → Processing batch → Quality release → Packaging lot → Finished goods stock → Sales/delivery → Margin and traceability reports.

### 4.3 Packaged goods resale
Supplier → Purchase/receipt → Stock → POS/order/delivery → Sale/fulfilment → Payment/receivable → Supplier/customer reconciliation → Profitability reporting.

### 4.4 Workforce-to-operations
Employee/personnel → Availability/shift → Assignment → Procurement/POS/delivery/maintenance activity → Approval/handover → Attendance/payroll → Operational and labour-cost analysis.

### 4.5 Actuals-to-planning
Posted operational/financial records → Reconciliation and close → Reporting dataset → KPI/trend analysis → Forecast → Budget/scenario → Variance → Management action.

## 5. Dependency and ownership risks to resolve

1. Product catalogue versus stock item: define shared identity and tenant-specific product extensions.
2. Branch versus warehouse/outlet/collection point: avoid one overloaded location entity.
3. Subscription versus generated order/fulfilment: avoid double counting demand or revenue.
4. Delivery quantity versus billable quantity: define the authoritative fulfilment event and adjustment path.
5. Procurement payable versus accounting ledger: define one authoritative posting contract.
6. Receipt record versus bank statement: preserve separate evidence and reconciliation state.
7. Processing batch versus stock lot: define lineage and quantity reconciliation.
8. Customer/supplier account balance versus financial ledger: define reconciliation and ownership.
9. Operational reports versus analytics warehouse: define freshness, correction, and historical reproducibility.
10. Forecast outputs versus actual/budget values: keep classifications and versions separate.

## 6. Architecture implications (not technology decisions)

- Stable domain contracts and identifiers are needed between dependent capabilities.
- Financial and inventory effects should be committed atomically or through a controlled posting/outbox pattern; exact implementation is deferred.
- Event-driven updates may support downstream analytics and notifications, but source transactions remain authoritative.
- Long-running work such as report generation, imports, notifications, and forecast runs should not block core counter/delivery transactions.
- Cross-branch and cross-domain reporting needs explicit permission and data-scope rules.
- High-volume reporting must not compromise transactional consistency or tenant isolation.
- CustodyEvent and StockMovement are distinct linked facts; custody/asset use must not create duplicate stock or financial effects. Shared-site permissions and evidence must remain independently scoped.
- DeliveryPlan, DeliveryRun, personnel assignments, transport-resource assignments and RunExecutionEvents remain separate. Dispatch snapshots are immutable; route/assignment changes require linked events and concurrent demand/resource claims.
- Integration failures must be retryable without duplicating financial, inventory, or delivery effects.

## 7. Boundary and evolution policy

The module map is a logical business dependency map, not a physical service deployment plan. The initial architecture uses cohesive bounded contexts inside a modular monolith. Each module owns its domain rules and authoritative writes; other modules use published contracts/events rather than direct writes to private data. New modules must be added through explicit capability ownership, dependency, permission, audit, API/event, reporting, data-retention and operational analysis.

Capability existence, plan entitlement, tenant activation, tenant configuration, user permission and extension enablement are separate dimensions. Optional modules must not impose unnecessary setup on small Tenants. A Tenant may progressively enable additional capabilities without changing the identity of existing records or rewriting prior transactions.

Independent service extraction is permitted only after measurable scale, security, availability, team ownership or release-independence benefits outweigh distributed-system complexity. See ARC-PV-001 for the authoritative boundary, extension and evolution policy.

The remaining work is domain-specific detail, validation of each dependency chain, and a capability-to-plan-entitlement catalogue; these must not weaken security or data integrity.
