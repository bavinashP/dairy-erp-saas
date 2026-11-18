# Analytics, Business Intelligence and Forecasting Architecture

**Document ID:** ARC-BI-001  
**Status:** Technology-neutral architecture baseline — tenant KPI ownership and commercial report entitlements require product approval  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Budgeting, Financial Analytics and Projections; ARC-TEN-001, ARC-IAM-001, ARC-EVT-001, ARC-OBS-001, ARC-SCALE-001, NFR-001; ODR-022/023/024/027/030.  
**Purpose:** Define a reliable, secure and scalable reporting, MIS, BI, budgeting, projection and forecasting capability for small owner-operated dairies through multi-branch businesses.

## 1. Architecture decisions

1. **Operational source of truth:** Transactional domain records and the double-entry accounting ledger remain authoritative. Analytics must never become the source of truth for sales, stock, payments, receivables, payables or journals.
2. **Logical separation:** Reporting/analytics is a distinct capability and workload boundary, but it does not require a separate database or warehouse on day one.
3. **Progressive architecture:** Begin with governed read models and pre-aggregations from transactional data. Introduce a dedicated analytical database/warehouse only when measured query volume, history, concurrency or isolation requires it.
4. **Asynchronous heavy work:** Large reports, exports, scheduled refreshes and forecasts run as durable jobs with progress, cancellation policy, idempotency and tenant scope.
5. **Consistent definitions:** KPIs, financial measures and operational metrics have versioned definitions, owners, lineage, date basis and approval status.
6. **Actual, budget, target, forecast and scenario are separate semantic categories.** They must never be silently combined or presented as equivalent.
7. **Tenant and branch security:** Authorization is enforced before aggregation, drill-down and export. Aggregation is not a reason to bypass tenant/branch restrictions.
8. **Explainability before complexity:** Start with transparent formulas, moving averages, seasonal baselines and rule-based models. Advanced ML is added only after data sufficiency and measurable benefit.
9. **Reproducibility:** Every published report/forecast records data cutoff, definition/model version, parameters and filters.
10. **India-focused defaults:** INR, Indian business-date conventions and IST defaults apply; multi-currency and alternative date-format configuration are outside the approved product scope.

## 2. Analytics capability layers

| Layer | Responsibility | Example |
|---|---|---|
| Source domain | Authoritative operational/financial facts | POS sale, delivery, stock movement, procurement, receipt, journal |
| Capture and change stream | Reliable change propagation | Transactional outbox/domain events |
| Data quality and reconciliation | Completeness, validity, duplicate and late-data checks | Reconcile POS totals to posted sales |
| Read model/aggregate | Fast tenant-safe query structures | Daily branch/product sales summary |
| Semantic/KPI layer | Governed business definitions | Delivery completion rate, gross margin |
| Reporting/MIS | Standard operational/financial reports | Daily collection, ageing, branch P&L |
| BI and dashboards | Trends, drill-down, comparison and alerts | Branch performance dashboard |
| Budget/scenario | Approved targets and hypothetical assumptions | Monthly branch expense budget |
| Forecasting | Estimates with confidence/limitations | Milk demand and cash-flow projection |
| Delivery/export | Authorized presentation and extraction | PDF/CSV/XLSX scheduled report |

Each layer has a clear owner and lineage. Derived values must be traceable to source facts and transformation versions.

## 3. Data domains and canonical analytical grain

Use explicit fact grains. Do not combine quantities at incompatible grains or sum ratios directly.

Candidate fact families:
- Sales line fact: one posted sale line/credit-note line.
- Subscription occurrence fact: one planned service occurrence and its fulfilment/billing outcome.
- Delivery stop fact: one assigned stop/attempt/outcome per run.
- Procurement/collection fact: one accepted/rejected supplier/farmer collection entry or measured lot.
- Quality test fact: one test/sample/parameter/result with method and timestamp.
- Stock movement fact: one posted stock movement line with source/destination and lot where applicable.
- Processing batch fact: one batch operation/input/output/loss/yield measurement.
- Invoice/receivable fact: one invoice line or receivable movement at a defined grain.
- Receipt/allocation fact: one payment receipt and allocation/reversal line.
- Payable/settlement fact: one supplier/farmer payable movement and settlement allocation.
- Accounting journal fact: one posted debit/credit journal line; debit and credit remain separately represented.
- Expense fact: one posted expense transaction/line.
- Payroll cost fact: one finalized payroll component/period/employee scope, subject to sensitive access.
- Budget fact: one approved budget version at account/period/branch/cost-centre grain.
- Forecast fact: one forecast run, horizon, period, entity and measure.
- Audit/operational fact: selected aggregate event measures; detailed audit access remains in its own protected subsystem.

Every fact must declare its grain, additive/semi-additive/non-additive measures, currency/quantity unit, business date, recorded timestamp, tenant, branch and source identity. Ratios and averages must be recomputed from governed numerators/denominators rather than summed.

## 4. Shared dimensions and organization semantics

Candidate dimensions:
- Tenant/business.
- Legal entity and tax registration, when the legal-entity model is approved.
- Branch/outlet/operational location.
- Warehouse/stock location.
- Product, product category, brand and pack/variant.
- Customer and customer segment.
- Supplier/farmer and procurement point.
- Route, area, delivery run and shift.
- Employee/team, with sensitive attributes segregated.
- Sales channel/POS register.
- Account/ledger/voucher type.
- Payment method and reconciliation status.
- Batch/lot/expiry and quality grade/parameter.
- Calendar date, business period and time-of-day segment.
- Budget version, scenario and forecast model version.

Branch is an operational dimension, not automatically a legal entity, GST registration, cost centre or profit centre. Those relationships must come from explicit master data and accounting policy. Preserve slowly changing attributes where historical reports must reflect the values effective at transaction time.

## 5. KPI and metric governance

A KPI cannot be published as an official metric until its definition is approved. Each definition records:
- Stable KPI ID, display name, description and owner.
- Formula, numerator, denominator, unit and rounding.
- Fact grain and aggregation behavior.
- Business date basis, timezone and period boundary.
- Included/excluded states, cancellations, reversals and late-arriving records.
- Source-of-truth entities and lineage.
- Tenant/branch dimensions and permitted drill-down.
- Refresh cadence, expected freshness and late-data policy.
- Definition version, effective date and superseded definition.
- Target/threshold source and approval state.
- Test cases with representative worked examples.

Initial KPI catalogue candidates:
- Sales value, units/litres sold, average selling price and sales mix.
- Subscription active count, pause/skip rate, retention and billed-versus-delivered.
- Delivery completion/attempt/failure rate and on-time performance.
- Milk procured, accepted/rejected quantity, average rate and quality distribution.
- Processing yield, packaging output, wastage and variance.
- Stock on hand, stockout, expiry exposure, ageing and stock variance.
- Gross margin and contribution margin with explicit cost methodology.
- Customer receivable ageing, days sales outstanding and collection efficiency.
- Supplier/farmer payable ageing and settlement timeliness.
- Cash/bank position, reconciliation difference and forecast liquidity.
- Expense by category/branch, payroll/labour cost and branch profitability.
- Trial balance, P&L, balance sheet and cash flow derived from posted ledger data.
- Budget variance, forecast accuracy and operational capacity utilization.

Do not label gross margin, profit or branch profitability as authoritative until cost allocation, inventory valuation and legal-entity/branch accounting treatment are defined.

## 6. Standard report and dashboard catalogue

### 6.1 Owner/management
- Daily business summary: sales, collections, deliveries, procurement, stock exceptions and cash position.
- Branch comparison: sales, margin, customer base, subscription fulfilment, delivery efficiency and operating costs.
- Product performance: volume, revenue, margin, returns, expiry and stockouts.
- Customer receivables and collection ageing.
- Supplier/farmer procurement and settlement status.
- Expense and cash-flow dashboard.
- Budget-versus-actual and forecast-versus-actual.

### 6.2 Operations
- Morning/evening delivery plan, completion and exceptions.
- Subscription occurrence generation and missed/changed schedule.
- Milk collection, quality acceptance/rejection and farmer-rate analysis.
- Processing batch yield, losses, packaging and traceability.
- Stock ageing, expiry, wastage, transfers and count variance.
- POS shift/register totals, voids, refunds and cashier reconciliation.
- Procurement, receiving and supplier outstanding.

### 6.3 Finance/accounts
- Trial balance, ledger, journal register and voucher drill-down.
- Profit and loss, balance sheet and cash flow.
- Customer ageing, receipt allocation and unapplied receipts.
- Supplier/farmer payable ageing, advances and settlement.
- Bank/cash book and reconciliation differences.
- GST/tax reports only from approved tax configuration and applicability rules.
- Tally export/sync status and voucher reconciliation; this is not a substitute for accounting source records.

### 6.4 Workforce and compliance
- Attendance, shift coverage, leave and payroll summaries subject to permissions.
- Quality/compliance exceptions, corrective actions and traceability reports.
- Asset maintenance schedule, downtime and repair costs.

Every report definition specifies whether it is operational, managerial, financial, statutory, or analytical. Statutory reports must have separate legal/tax verification and cannot rely solely on generic BI calculations.

## 7. Actuals, budgets, targets, forecasts and scenarios

- **Actual:** Posted/validated source transactions and operational facts, with reversals represented explicitly.
- **Budget:** An approved, versioned financial/operational plan for a period and scope.
- **Target:** A management goal or threshold; not necessarily a financial budget.
- **Forecast:** A model-generated estimate based on data cutoff and assumptions.
- **Projection:** A forward calculation using explicit assumptions/rules; may be simpler than a statistical forecast.
- **Scenario:** A hypothetical set of changed assumptions; never a posted business transaction.

Separate storage, visual labels, filters and export columns. Never overwrite a budget with a forecast or replace actuals with projections. Every forecast/scenario must identify model/assumption version and generated timestamp.

Budget workflow baseline:
Draft → Submitted → Reviewed → Approved → Published → Revised/Closed.
Revisions create a new version and preserve the approved prior version. Approval rights and thresholds are separately permissioned. Budget approval does not post accounting transactions.

## 8. Forecasting strategy and model governance

### 8.1 Phased method
1. Baseline: historical averages, moving averages, weekday/seasonality profiles and explicit business rules.
2. Improved statistical methods: only when sufficient clean history exists and backtesting demonstrates improvement.
3. Advanced ML: only when the data volume, quality, explainability, monitoring and maintenance costs justify it.

No advanced model is mandatory for MVP. A transparent baseline that is measurable and explainable is preferable to an unvalidated complex model.

### 8.2 Candidate forecasts
- Milk demand by product, branch, shift and day.
- Subscription volume and churn/pause trends.
- Milk procurement need and farmer supply variability.
- Inventory replenishment, expiry and stockout risk.
- Processing/packing capacity, yield and wastage.
- Route workload, delivery time and staffing requirement.
- Sales/revenue and gross-margin outlook.
- Cash-flow and liquidity from receivables, payables, payroll and expenses.
- Supplier/farmer settlement cash requirement.
- Workforce requirement and seasonal workload.

### 8.3 Forecast run record
Each run stores:
- Forecast ID, tenant/branch scope and model name/version.
- Run timestamp and source-data cutoff timestamp.
- Training/history window and source dataset version.
- Forecast horizon, time grain, measure and units.
- Features/assumptions and external factors used.
- Output values and prediction intervals/confidence where method supports them.
- Validation/backtest metrics and known limitations.
- Manual override, actor, reason and approval if applicable.
- Actual outcomes when later available for accuracy measurement.

Avoid data leakage: a forecast must not train on records occurring after its source cutoff. Backtests use time-based splits, not random splits that mix future and past.

### 8.4 Accuracy and safety
- Compare model results to a simple baseline.
- Track suitable error measures by horizon and product/branch; do not use one metric blindly for low-volume/intermittent demand.
- Show sample size, data gaps and uncertainty.
- Suppress or label forecasts where history is insufficient.
- A forecast must not automatically create procurement, payroll, pricing or financial postings without a separately authorized approval workflow.
- Monitor drift and retrain/recalibrate only under versioned governance.

## 9. Data movement and refresh

- Publish source changes through the transactional outbox or a proven change-capture mechanism.
- Consumers are idempotent and tenant-scoped.
- Separate near-real-time operational summaries from scheduled heavy analytical refreshes.
- Store source watermark, last successful refresh, data cutoff and late-arriving correction state.
- Reprocess affected aggregates when source transactions are reversed/corrected.
- Closed-period financial reports must be reproducible from immutable posted facts and the relevant accounting/KPI definition version.
- Do not run unbounded full-table scans on the transactional write path.
- Introduce warehouse/OLAP technology only after the capacity architecture demonstrates need; maintain source-to-aggregate reconciliation.

## 10. Security, privacy and access control

- Authorize before query execution and again before export/download.
- Tenant filter is mandatory and derived from trusted server context; branch scope is separately enforced.
- Field-level masking applies to bank, salary, identity and customer contact data.
- Export permission is distinct from view permission; sensitive report downloads are audited.
- Cross-branch comparisons require an explicit permission and scope.
- Platform-level aggregate analytics must use a separate access policy and avoid exposing identifiable tenant information unless an approved operational need exists.
- Forecast training datasets must follow tenant isolation and data-purpose policy; no cross-tenant model training by default.
- Cache keys include tenant, branch, principal/policy version, definition version, filter and data cutoff.
- Report jobs revalidate authorization and tenant status at execution and download time.
- Retention, deletion and legal holds apply to source data, snapshots, exports and model artifacts.

## 11. Performance and scale

- Common dashboards use pre-aggregated read models when measured query cost requires them.
- Heavy reports and forecasts are asynchronous with status, progress, retry and expiry.
- Enforce row/date-range/export-size limits and tenant-aware concurrency.
- Use pagination/drill-down and avoid loading full fact tables into application memory.
- Cache only with explicit freshness and permission invalidation.
- Separate analytical workload from POS, delivery and financial posting.
- Provide downloadable results through private object storage and short-lived authorized URLs.
- Follow ARC-SCALE-001 capacity envelopes and noisy-neighbour controls.

## 12. Tally and accounting boundary

- Financial statements and ledger reports originate from the platform's posted double-entry ledger.
- Tally synchronization status, exported voucher batches, mapping versions and reconciliation differences are reportable.
- Tally is an external accounting interchange/desktop workflow; it must not silently become a second conflicting source of truth.
- Import/export reports identify source system, company/ledger mapping, voucher period, batch ID, duplicate handling and reconciliation status.
- Never claim that a successful file export proves that Tally imported or accepted every voucher.

## 13. Acceptance and reconciliation tests

1. Reconcile daily POS/subscription sales totals to posted sales and journal totals.
2. Reconcile stock movement and closing balance to inventory ledger.
3. Reconcile procurement quantities, quality acceptance and farmer payable calculation.
4. Reconcile receipts and allocations to customer balances and bank/cash records.
5. Reconcile trial balance totals and debit/credit equality to posted journal lines.
6. Compare branch actuals to a specific approved budget version.
7. Show forecast and scenario values separately from actuals and budgets.
8. Reproduce a historical report using its original KPI definition and source cutoff.
9. Prevent unauthorized tenant/branch report query, drill-down and export.
10. Demonstrate that a heavy forecast does not delay POS or accounting posting beyond approved service budgets.
11. Correct/reverse a source transaction and verify affected aggregates are updated with history preserved.
12. Verify a forecast's source cutoff prevents future-data leakage.

## 14. Selected policy baselines and remaining accountable approvals

The architecture and standard report families are defined. The product/engineering baselines are selected under ODR-022/023/024: stable versioned KPI formulas, Tenant-configurable targets/dimensions, advisory baseline/statistical forecasts with backtesting, and versioned budgets/scenarios separate from posted actuals. The following still require accountable business/owner assignment before launch:
- Assign the accountable business function/person for each official KPI formula and target. No job title alone gains report access or approval rights.
- Approve the commercial mapping of reports/analytics to SaaS plans or add-ons; plan entitlement never grants data permission.
- Tenants configure budget approval thresholds and specific approver permissions within maker-checker safeguards; any mandatory platform minimums remain fixed where integrity/risk requires.
- Initial forecast defaults are short-horizon daily/weekly where sufficient history exists; Tenant users may select supported horizon and scenario inputs. Prioritize additional forecast families from pilot evidence.
- Cross-Tenant benchmarking remains off by default and requires a separately reviewed privacy/consent/aggregation policy before activation.
- Whether a specific report is legally/statutorily relied upon; each such report requires separate legal/accounting validation.

## 15. Exit criteria

Development readiness requires source fact grains, KPI dictionary, report permissions, data lineage, refresh strategy, budget versioning, forecast-run schema and reconciliation tests. Production readiness requires approved official KPI definitions, report entitlement catalogue, data quality thresholds, measured refresh/load behavior, privacy review and forecast backtesting evidence.
