# Budgeting, Financial Analytics and Projections

**Status:** Working baseline — domain requirements

## 1. Purpose and distinctions
Provide decision support without confusing actual, target, budget, forecast, projection and scenario. A forecast is an estimate based on data and assumptions, not a promise. Reports must disclose period, data freshness, formula, filters, assumptions and confidence/limitations.

## 2. Reporting foundation
Use validated operational and posted financial facts with common dimensions: tenant, branch, outlet, stock location, product/category, customer segment, supplier/farmer, route/area, shift, employee/team, channel, voucher/account and date. Dimensions must respect access permissions. Branch is not automatically a legal entity or cost centre.

## 3. Management reporting
Core dashboards and reports:
- Daily sales, collection, delivery and exception dashboard.
- Subscription active base, churn/pause/skip, fulfilment and billed-versus-delivered.
- Product/customer/branch/channel sales and gross margin.
- Milk procurement quantity, quality, rate and farmer payable.
- Stock ageing, expiry, wastage, yield and stock variance.
- Customer receivables ageing and collection efficiency.
- Supplier/farmer payables and settlement cycle.
- Cash position, bank reconciliation and upcoming obligations.
- Expense category and branch profitability.
- Payroll and labour cost.
- Trial balance, P&L, balance sheet and cash-flow views from accounting ledger.
- KPI trend, target attainment and operational service levels.

## 4. KPI governance
Every KPI must define owner, formula, numerator/denominator, unit, grain, date basis, inclusion/exclusion, source of truth, refresh cadence, dimensions, rounding and version. Example KPIs include delivery completion rate, subscription retention, litres procured, rejection rate, yield percentage, wastage, stockout rate, gross margin, contribution margin, receivable days, collection efficiency and labour cost ratio. Do not publish a KPI until its formula and data lineage are approved.

## 5. Budgeting
Support annual, quarterly, monthly and configurable period budgets. Budget dimensions may include account, branch, cost centre, product family, headcount or project. Support draft, review, approval, published, revised and closed versions; scenario assumptions; allocation from parent to branch; controlled transfers; and actual-versus-budget comparison. Preserve original approved budget when revised; record who changed it and why.

## 6. Forecasting and projection domains
Potential models:
- Demand and subscription volume by product/shift/area.
- Milk procurement requirement and farmer supply variability.
- Inventory replenishment and expiry risk.
- Production/packing capacity and yield.
- Route workload and delivery staffing.
- Cash-flow forecast from receivables, payables, payroll, expenses and planned purchases.
- Sales, margin and branch profitability projections.
- Workforce requirement and seasonal demand.

Start with transparent rule-based/statistical baselines; advanced ML is optional and must demonstrate value. Model inputs, training window, version, assumptions, run timestamp, overrides and actual outcome comparison must be retained.

## 7. Scenario planning
Allow authorized users to compare base, optimistic and conservative scenarios with explicit editable assumptions (price, volume, yield, procurement rate, wastage, staffing, expense, collection delay and opening cash). Scenario outputs must be separate from posted transactions and official financial statements.

## 8. Data quality and reproducibility
Reconcile source data before forecasts. Identify missing days, outliers, manual corrections, late-arriving transactions and closed-period adjustments. Reports for closed periods should be reproducible. Forecasts must identify source-data cut-off and exclude future actuals beyond that cut-off to avoid leakage.

## 9. Access and performance
Enforce tenant and branch permissions at query/export level. Aggregated data must not leak across tenants. Use asynchronous jobs for heavy reports and forecast runs; cache only with explicit tenant/permission/date/filter keys and freshness rules. Do not block POS, delivery or posting transactions for analytics.

## 10. Reports and exports
Provide drill-down from KPI to source operational record and, where applicable, journal/voucher. Export PDF/CSV/XLSX with generation metadata, filters, timezone/business-date context and permission checks. Tally export is a separate accounting data exchange, not a substitute for management analytics export.

## 11. Acceptance scenarios
1. Reconcile daily sales dashboard to posted POS/subscription source totals.
2. Compare branch actuals to approved budget version.
3. Show forecast separately from actual and budget.
4. Reproduce a historical report using its original KPI definition/version.
5. Generate cash forecast from receivable/payable schedules and disclose assumptions.
6. Run scenario changes without modifying ledger transactions.
7. Prevent branch user from exporting unauthorized branch data.
8. Queue heavy forecast jobs without slowing transactional APIs.
9. Explain KPI formula and drill down to source facts.

## 12. Open decisions
MVP KPI set; budget approval roles; forecast horizon and granularity; data warehouse strategy; refresh latency; export limits; forecasting method and evaluation thresholds.