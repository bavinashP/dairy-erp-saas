# Dairy Business Domain Landscape and Capability Model

**Status:** Working baseline — domain discovery  
**Scope:** Indian dairy retail, doorstep delivery, procurement, and broader dairy operations  
**Authority:** Product Vision and Scope remains the product-scope authority. This document decomposes that scope into business domains; it does not select technical services.

## 1. Modelling principles

1. Model business capabilities independently of tenant size, staffing level, and client device.
2. A capability may exist in the product, be commercially entitled, activated, configured and used as separate lifecycle states. Its existence in the product model does not force every dairy to operate it.
3. Distinguish a business concept from its physical location, legal registration, SaaS entitlement, and user access scope.
4. Do not assume every tenant processes milk. Reselling packaged products, distributing loose milk, collecting milk, processing milk, and manufacturing value-added products are distinct operating capabilities.
5. Do not assume every tenant has employees, a GST registration, a payment gateway, a warehouse, a chilling facility, or a dedicated branch manager.
6. Preserve traceability from source transaction to stock movement, customer/supplier balance, financial posting, and analytical result.
7. A business transaction may involve more than one branch, but the responsible branch, stock location, and financial ownership must remain explicit.
8. Tenant-configurable policies cannot override platform security, tenant isolation, authorization, or data-integrity rules.

## 2. Business capability map

| Domain | Core responsibility | Typical capabilities | Conditionality |
|---|---|---|---|
| SaaS Platform Administration | Operate the SaaS product | Tenant onboarding, plans, entitlements, platform support, platform audit | Every SaaS deployment |
| Tenant and Business Setup | Represent a dairy business | Business profile, legal/trade identity, operating profile, settings | Every tenant |
| Branch and Location Management | Represent operating locations | Branches, outlets, collection points, warehouses, processing sites, service areas | One or many as needed |
| Identity and Workforce Access | Control personnel access | Users, role assignments, permissions, branch scopes, sessions, devices | Every authenticated user |
| Customer Management | Maintain customer accounts | Contacts, addresses, delivery instructions, status, account balance | Customer-facing business operations |
| Subscription Management | Manage recurring demand | Recurrence pattern, product, quantity, delivery slot, pause/skip/change lifecycle | When recurring supply is offered |
| Order and Sales Management | Capture non-recurring demand and sales | Ad-hoc orders, counter sales, sales returns, discounts | When those channels are used |
| Supplier and Farmer Management | Maintain supply sources | Farmer/supplier profiles, contacts, terms, settlement details | Procurement operations |
| Milk Procurement | Acquire raw milk | Collection sessions, quantity, quality, rates, acceptance/rejection, supplier settlement | When raw milk is procured |
| Purchased Goods Procurement | Acquire resale and operating goods | Purchase orders, receipts, supplier invoices, returns | When goods are purchased |
| Milk Quality and Testing | Record quality evidence | Sample, test, parameter, result, equipment, rejection reason | Based on procurement/processing model and policy |
| Processing and Packaging | Convert raw inputs to finished goods | Batch, recipe/formula, yield, loss, packaging, traceability | Only where tenant processes/packages |
| Product and Price Catalogue | Define sellable/buyable items | Product types, units, packs, variants, price lists, effective dates | Every product-selling tenant |
| Inventory and Warehousing | Control stock | Receipts, issues, transfers, adjustments, expiry, batch/lot, wastage, linked custody events and reconciliation | As stock is physically managed |
| Distribution and Delivery | Fulfil doorstep demand | Branch-owned Routes/manual routes, date/shift DeliveryPlans, run allocation, personnel and optional vehicle assignment, dispatch readiness, stops, custody, delivery outcomes, returns and exceptions | When delivery is offered; fleet/optimization capabilities remain optional |
| Billing and Receivables | Charge customers and track dues | Billing cycles, statements, invoices where applicable, debit/credit adjustments, receipts | Customer credit/recurring billing |
| Payments and Reconciliation | Record and reconcile money movement | Cash, UPI QR, bank transfer, gateway reference, matching, refund | Whenever money is received/paid |
| Accounting and Finance | Represent financial position | Accounts, vouchers, journals, ledgers, cash/bank, expenses, statements | According to selected finance scope |
| HR and Payroll | Manage workforce | Employee records, attendance, shifts, leave, payroll and statutory workflows | When workforce management is used |
| Asset and Maintenance | Track operational assets | Equipment, chillers, vehicles, service, calibration, maintenance, shared-resource availability/use and custody | When assets/resources are tracked |
| Quality, Hygiene and Compliance | Record controlled practices | Checklists, cleaning logs, temperature checks, corrective actions, documents | According to actual operations and applicability |
| Notifications and Workflow | Coordinate work | Reminders, approvals, alerts, escalation, templates | Cross-cutting |
| Reporting and MIS | Explain recorded business activity | Operational, financial, inventory, delivery, procurement and management reports | Cross-cutting |
| BI and Analytics | Explain patterns and drivers | KPIs, trends, segmentation, drill-down, comparisons | Cross-cutting |
| Forecasting and Projections | Estimate future outcomes | Demand, procurement, sales, cash flow, stock, staffing projections | Cross-cutting; data-dependent |
| Budgeting and Scenario Planning | Compare plan to actual | Budgets, scenarios, variance, what-if analysis | Optional capability by tenant/plan |
| Integration and API Management | Connect external systems | API clients, webhooks, payment/messaging/accounting adapters, imports/exports | Cross-cutting |
| Audit and Security Operations | Protect and evidence activity | Audit events, access review, anomaly signals, security operations | Platform-enforced |

## 3. Important domain distinctions

### 3.1 Tenant, business, legal entity, branch, and location
These terms must not be treated as synonyms:
- **Tenant:** SaaS data-isolation and subscription boundary.
- **Business profile:** Operational identity/configuration of the dairy customer.
- **Legal entity/registration:** Legal or tax identity; exact supported relationships remain open.
- **Branch:** Business operating unit under a tenant.
- **Location:** A physical or service location, such as outlet, collection point, warehouse, processing site, or delivery area.

A branch may contain or use multiple operational locations. The final relationship and accounting ownership require explicit modelling.

### 3.2 Product, stock item, and service
A sellable product is not necessarily a manufactured product. A tenant may buy packaged goods for resale, procure loose milk, pack it, or manufacture dairy products. The model must distinguish:
- Product definition and sellable unit.
- Procurement item and supplier-specific unit.
- Stock item and stock unit.
- Raw material, work-in-progress, and finished good where processing exists.
- Non-stock service/charge where required.

### 3.3 Customer subscription versus order
A subscription expresses a recurring supply instruction/entitlement. An order expresses a particular demand event. A daily fulfilment record must be generated or represented for the due occurrence without rewriting the underlying recurring agreement. Ad-hoc purchases remain separately traceable.

### 3.4 Payment record versus verified settlement
A manually entered UPI/cash/bank receipt is a business record. It is not proof of provider-side settlement unless corroborated through a reliable source or authorized reconciliation. Store source, reference, evidence, recorder, verification status, and reconciliation outcome distinctly.

### 3.5 SaaS plan versus tenant configuration
A SaaS plan determines capability availability, usage allowance and commercial/service tier. Tenant activation records whether the capability is enabled. Tenant configuration determines how the enabled capability behaves. Permission assignments determine which principals may view or perform its operations. Approved extensions add capabilities through governed contracts. These are separate dimensions; none may silently grant another's authority or weaken platform security controls.

## 4. Operating profiles to support

The product should allow capability combinations such as:
- Retail counter dairy with no doorstep delivery.
- Subscription-based doorstep delivery with monthly customer settlement.
- Loose milk purchased from farmers and delivered after repacking.
- Packaged branded products purchased from companies/distributors and resold.
- Mixed loose milk and packaged-product operations.
- Multi-branch retail and delivery business.
- Collection-centre operation supplying a processing unit.
- Processing/packaging dairy with batch and quality controls.
- Owner-operated business with no employee accounts.
- Business with separate procurement, sales, delivery, finance, and management staff.

These are scenarios for validation, not an exhaustive or mutually exclusive classification.

## 5. Cross-domain transaction chains

1. **Recurring customer supply:** Customer → Subscription → Due occurrence → Daily demand/fulfilment → Stock allocation → Delivery outcome → Billing → Receipt/reconciliation.
2. **Counter sale:** POS session → Sale → Stock issue → Payment tender(s) → Receipt → Cash/bank reconciliation.
3. **Farmer milk procurement:** Supplier/farmer → Collection session → Quantity/sample/test → Acceptance/rejection → Rate calculation → Procurement payable → Settlement.
4. **Packaged goods purchase:** Supplier → Purchase order (optional) → Goods receipt → Supplier invoice → Stock increase → Supplier payable → Payment.
5. **Milk processing:** Accepted raw milk → Batch input → Quality/process records → Yield/loss → Finished goods → Packaging lots → Stock availability.
6. **Branch transfer:** Transfer request → Source reservation/issue → In-transit custody → Destination receipt/variance → Stock ledger.
7. **Customer month-end billing:** Fulfilment/sales/adjustments → Billing period close → Statement/invoice as applicable → Receivable → Receipt allocation → Balance reconciliation.
8. **Management insight:** Authoritative transactions → validated reporting dataset → KPI/analysis → forecast/scenario → management decision.

## 6. Shared business controls

The following are cross-domain requirements to detail later:
- Stable transaction identifiers and traceability.
- Explicit tenant and branch context.
- Effective-dated prices/rates/configuration.
- Unit-of-measure conversion and rounding policy.
- Controlled status transitions.
- Reversal/correction rather than destructive editing of posted financial/stock transactions.
- Duplicate prevention and idempotent retry behavior.
- Concurrency-safe allocation, posting, and balance updates.
- Audit history for material changes.
- Reconciliation between operational records and financial/stock ledgers.
- Defined cut-off and business-date behavior.
- Permission checks at the point of action and data retrieval.
- Historical snapshots or reproducible calculations for reports where source values may later change.

## 7. Shared-site accountability and custody capability

Shared-site custody is a cross-cutting operational control used by inventory, procurement, milk collection, transport, processing, delivery and asset/resource management where the Tenant enables it. It distinguishes stock ownership and ledger attribution from physical custody, actual handling, workforce assignment and permission. The capability supports proportionate single-party capture through high-assurance handover evidence, without forcing the high-assurance process on every small Tenant. Custody events do not independently create stock or financial movements. Full domain policy is in [Shared-Site Accountability and Custody Management](SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md).

## 8. Route execution and transport capability

Route planning, personnel/transport assignment and dispatch are distinct capabilities. A one-person business can use a one-off manual Branch-owned Route and one DeliveryRun without maintaining a fleet registry or route optimizer. A larger Tenant can group multiple runs in a DeliveryPlan, split demand across vehicles, validate capacity/readiness, configure independent dispatch approval and add mapping/telematics/optimization adapters. Route/Branch/ServiceArea ownership, explicit permissions, stock allocation, custody and product-handling controls remain fixed. The authoritative workflow is in [Delivery Lifecycle and Exception Rules](DELIVERY_LIFECYCLE_AND_EXCEPTION_RULES.md) and ODR-002 section 16.37.

## 9. Exclusions and boundaries
Customer self-service login and consumer e-commerce storefront remain outside the approved current boundary. A future proposal must be handled through change control. No technical microservice boundaries are implied by this domain map.

## 10. Research notes
- NDDB describes milk collection operations involving producer identification, quantity measurement, sampling/testing, rate calculation, transaction receipt, shift summaries, and milk dispatch. This supports modelling collection as a controlled multi-step transaction rather than a single purchase entry.
- NDDB describes raw-milk testing, chilling/storage, transport, receiving inspection, and processing as distinct stages. These capabilities apply only to tenants whose operating model includes those stages.
- FSSAI materials distinguish hygiene requirements across petty food operators and manufacturing/processing/packaging/storage/distribution, with a specific dairy and milk-products hygiene section. Product workflows must therefore be configurable by actual activity and applicability; legal applicability must be separately verified.

## 11. Sources
1. NDDB, Milkipedia — testing, storage, transportation, and processing of milk: https://www.nddb.coop/ccnddb/milkipedia
2. NDDB, Dairying through Cooperatives — milk procurement infrastructure manual: https://beta.nddb.coop/wp-content/uploads/2025/11/Manual_Component_A_Strengthening_Milk_Procurement_Infrastructure.pdf
3. FSSAI, Hygiene Requirements: https://fssai.gov.in/inspection/hygiene-requirements
4. FSSAI, Food Safety Management System Guidance Documents: https://www.fssai.gov.in/business/guidance
5. FSSAI, Food Safety and Standards Regulations: https://fssai.gov.in/food-law/regulations

**Caution:** This is domain analysis, not legal advice or a final compliance applicability determination.

## 12. Product variability and growth

The platform must present a composable capability model rather than separate product editions with permanently different code. Tenant business profiles and starter templates are editable starting points only; one Tenant may combine capabilities and evolve over time. Plan entitlement, capability activation, operational configuration, permission assignment and extension enablement are independent controls.

Each domain must classify its rules as fixed, configurable, customizable, extensible or externally dependent. Domain-specific settings and constraints remain defined by the owning domain specification; cross-product configuration precedence, lifecycle and extension safety are governed by ARC-PV-001. Configuration changes must preserve transaction snapshots and historical meaning.

See [Product Configurability, Customization, Extensibility and Scale Policy](../04-architecture/PRODUCT_CONFIGURABILITY_CUSTOMIZATION_EXTENSIBILITY_AND_SCALE_POLICY.md).
