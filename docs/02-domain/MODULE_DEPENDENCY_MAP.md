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
| Purchased Goods Procurement | Supplier, product, purchase/receipt, tax/accounting policy | Stock ledger, supplier payable, reports |
| Quality and Testing | Product/material, supplier, sample, equipment, quality policy | Procurement acceptance, processing release, compliance |
| Processing and Packaging | Raw-material stock, quality, process definitions, equipment | Finished goods, batch traceability, inventory, cost analysis |
| Inventory/Warehouse | Product/unit, location, source transactions | Availability, allocation, stock valuation, reports |
| Distribution/Delivery | Subscription/order, customer/address, route, stock allocation, workforce | Fulfilment, delivery exceptions, billing, performance reports |
| Billing/Receivables | Fulfilment, sales, price/tax policy, customer account, adjustments | Customer balance, statements/invoices, finance, reports |
| Payments/Reconciliation | Receipt/payment source, account/cash custody, open items | Allocated settlement, finance, customer/supplier balance |
| Accounting/Finance | Financial events, chart/accounting policy, payment reconciliation | Ledgers, statements, profitability, compliance reports |
| HR/Payroll | Employee records, shifts/attendance, compensation/policy | Payroll expense, workforce availability, reports |
| Asset/Maintenance | Asset register, location, work orders, schedules | Availability, maintenance cost, compliance evidence |
| Quality/Compliance | Applicable business profile, facility/process/equipment, evidence | Holds/releases, corrective actions, audit and compliance reporting |
| Reporting/MIS | Authorized operational/financial facts | Dashboards, period reports, management review |
| BI/Analytics | Validated historical facts, dimensions, KPI definitions | Trends, drill-down, segmentation, management insight |
| Forecasting/Projections | Historical data, known future events, assumptions, model validation | Demand/procurement/cash/stock/staffing forecasts |
| Budgeting/Scenario Planning | Actuals, chart/dimensions, assumptions, forecasts | Budget versions, variance, what-if decisions |
| Integrations/API Management | Stable domain contracts, auth, tenant context, audit | External provider/system connectivity |

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
- Integration failures must be retryable without duplicating financial, inventory, or delivery effects.

## 7. Next refinement

The next domain pass should produce:
- A canonical glossary and entity relationship concepts.
- State-transition models for subscription, delivery, procurement, stock, billing, and payment.
- Exception and reversal matrices.
- A requirements traceability matrix mapping domain capabilities to functional and non-functional requirements.
- A validated capability-to-plan entitlement matrix without using plans to weaken security or data integrity.
