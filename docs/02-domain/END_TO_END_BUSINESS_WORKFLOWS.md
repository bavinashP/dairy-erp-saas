# End-to-End Dairy Business Workflows

**Status:** Working baseline — workflow discovery  
**Purpose:** Describe business events and hand-offs before defining screens, APIs, tables, or implementation services.

## 1. Workflow modelling conventions

Each workflow identifies:
- Trigger and initiating actor.
- Required business context.
- Main steps and decisions.
- Resulting records and downstream effects.
- Exceptions, reversals, and controls.
- Open rules that must not be guessed.

A workflow may be simplified or not used by a tenant if its operating profile does not require it. Configuration must not bypass authorization, traceability, or data integrity.

## 2. Tenant onboarding and operational setup

**Trigger:** A dairy business subscribes to the SaaS platform.

1. Platform operator creates or approves tenant onboarding.
2. Tenant owner/business operator supplies business profile and contact details.
3. Record legal identity and registrations only to the extent applicable and verified.
4. Create one initial branch; permit additional branches independently of GST registration.
5. Configure operational locations such as outlets, delivery areas, warehouses, collection points, and processing sites as required.
6. Select SaaS plan and provision entitlements/limits.
7. Configure business preferences: products, units, pricing, shifts, delivery patterns, billing cycle, payment modes, and language.
8. Invite/create personnel accounts only where required.
9. Assign roles and branch scopes.
10. Complete setup checks and activate business operations.

**Outputs:** Tenant, business profile, branch/location records, plan entitlements, configuration, initial access assignments, setup audit.

**Exceptions/open rules:** Trial and activation policy; owner verification; multiple legal identities; tenant suspension/reactivation; migration from existing records.

## 3. Customer onboarding

**Trigger:** Staff registers a customer or converts an inquiry into an active account.

1. Search for an existing customer within permitted tenant scope to avoid duplicates.
2. Capture minimum contact identity and one or more service/delivery addresses.
3. Capture delivery instructions, preferred contact channel, and relevant notes.
4. Assign branch/account ownership and service area where required.
5. Set customer-specific price/credit terms only with appropriate permission.
6. Record status and effective date.
7. If recurring supply is requested, create a subscription separately.
8. If customer only buys at counter, do not force a subscription.

**Outputs:** Customer profile, address, account relationship, optional credit/pricing configuration.

**Exceptions/open rules:** Duplicate matching; shared household contact; customer transfer between branches; inactive/blocked customer; privacy and retention.

## 4. Recurring subscription setup and lifecycle

**Trigger:** Authorized staff agrees a recurring supply arrangement with a customer.

1. Select customer and service branch/route.
2. Select product, unit, quantity, and delivery slot (morning/evening or other configured slot).
3. Define recurrence: daily, alternate days, selected weekdays, every N days, or another supported recurrence rule.
4. Define start date and, where applicable, end date.
5. Validate that service area, route capacity, product availability, and business cut-off rules permit activation.
6. Confirm price basis and billing treatment.
7. Activate subscription and preserve the agreed terms/version.
8. Generate due occurrences according to recurrence and calendar policy.
9. Apply authorized pause, skip, resume, quantity change, product change, address change, or cancellation as effective-dated changes.
10. Preserve prior occurrences and fulfilment history when future subscription terms change.

**Outputs:** Subscription agreement/configuration, versioned terms, due occurrences, audit trail.

**Exceptions/open rules:** Same-day activation; cut-off after which a skip/change is not accepted; holidays; customer absence; delivery failure; stock shortage; product substitution; proration; arrears; backdated changes; temporary versus permanent changes.

**Critical distinction:** Do not implement recurrence as a simplistic daily flag. A recurrence rule must be evaluated against effective dates, selected days, interval, delivery slot, branch calendar, pauses, exceptions, and cut-off rules.

## 5. Daily demand planning and fulfilment

**Trigger:** A business-date/shift planning cycle begins.

1. Determine subscriptions due for the selected branch, service date, and delivery slot.
2. Include approved one-time orders and authorized manual additions.
3. Exclude paused, skipped, cancelled, or otherwise ineligible occurrences according to effective rules.
4. Consolidate demand by product, unit, route, and delivery stop while retaining customer-level traceability.
5. Compare demand with available stock and expected replenishment.
6. Record shortages, approved substitutions, or unfulfilled quantities with reason.
7. Allocate stock and prepare route/stop manifests.
8. Dispatch the run and assign responsible personnel/vehicle where applicable.
9. Capture per-stop outcomes: delivered, partial, not delivered, customer unavailable, refused, damaged, or other configured reason.
10. Record actual quantity, time/evidence where required, returns, and exceptions.
11. Reconcile dispatched quantity against delivered quantity, returns, wastage, and remaining stock.
12. Make fulfilled quantities available to billing according to the billing policy.

**Outputs:** Demand snapshot, allocation, pick/dispatch records, route run, stop outcomes, stock movements, fulfilment records, exception records.

**Exceptions/open rules:** Late subscription changes; route reassignment; partial delivery; cash collected by delivery staff; customer credit; product return; delivery proof; failed connectivity/offline sync; duplicate submissions.

## 6. Counter/POS sale

**Trigger:** Customer requests goods at the counter.

1. Open or select an authorized POS session/register.
2. Scan/search/select products and quantities.
3. Resolve current price, discount, tax treatment, and customer-specific terms.
4. Check stock availability and branch/location.
5. Add permitted discounts or customer details with required approval.
6. Accept one or more payment tenders, or record approved credit sale.
7. Confirm sale atomically with stock issue and payment/receivable records.
8. Print/share receipt where configured.
9. Close or reconcile POS session: opening float, cash sales, other tenders, refunds, cash withdrawals, and closing count.

**Outputs:** Sale, sale lines, stock issue, tender records, receipt, POS session totals.

**Exceptions/open rules:** Suspended sale; split tender; cash shortage/overage; refund; exchange; void after payment; offline POS; price override; negative stock policy.

## 7. Farmer/raw-milk procurement

**Trigger:** A farmer/supplier presents raw milk for a collection session or milk is received at a designated point.

1. Identify supplier/farmer and collection point.
2. Select collection date, shift/session, and milk type/category as applicable.
3. Measure quantity using configured unit/equipment.
4. Collect and identify sample where testing is performed.
5. Record test parameters and results, including operator/equipment and test time.
6. Determine acceptance, partial acceptance, quarantine, or rejection according to configured quality policy.
7. Calculate payable rate using the applicable approved rate chart and quality factors.
8. Generate supplier transaction/receipt with quantity, test results, rate, and payable amount.
9. Move accepted milk into the appropriate raw-milk stock/location or dispatch batch.
10. Reconcile collection session totals, equipment readings, receipts, and dispatch quantities.
11. Accumulate supplier payable and settle according to agreed cycle, deductions, advances, and payment method.

**Outputs:** Collection session, milk receipt, sample/test record, accepted/rejected quantity, rate calculation, supplier ledger/payable, stock movement, dispatch record.

**Exceptions/open rules:** No testing equipment; manual testing; test dispute/retest; adulteration suspicion; equipment failure; late entry; corrected weight; rate effective-date change; supplier advance/deduction; partial rejection; payment reversal.

**Important:** For a small reseller buying already packaged milk, this workflow may not apply. Use supplier purchase/goods receipt instead.

## 8. Purchased packaged goods procurement

**Trigger:** Business needs to replenish resale products or operating supplies.

1. Identify supplier and products.
2. Create purchase order if the tenant uses ordering/approval controls.
3. Receive goods against expected or direct purchase.
4. Record actual quantities, batch/lot, expiry, damage, and discrepancy where relevant.
5. Record supplier invoice/reference and applicable charges/tax data as required.
6. Update stock only for accepted quantities.
7. Record supplier payable and due date/terms.
8. Return rejected/damaged goods through a traceable return process.
9. Reconcile supplier statement and payments.

**Outputs:** Purchase order (optional), goods receipt, supplier invoice record, stock ledger, payable, supplier return.

**Exceptions/open rules:** Partial receipt; price variance; invoice mismatch; expired/near-expiry goods; supplier credit note; purchase without PO; goods received at a branch different from the ordering branch.

## 9. Milk processing and packaging (conditional workflow)

**Trigger:** Accepted raw milk is released for processing.

1. Receive and identify raw-milk lot/source.
2. Perform required receiving tests and acceptance checks.
3. Record transfer into processing storage.
4. Create processing batch and declare input quantities.
5. Record applicable processing steps, equipment, operator, time, temperature/parameters, and process checks.
6. Record output quantities by finished product, by-product, and loss/wastage.
7. Create packaging lots with pack size, quantity, date/time, and traceability references.
8. Move finished goods to sellable stock after required quality release.
9. Link finished goods to input lots and processing records for traceability.
10. Handle hold, rework, rejection, recall, or disposal with authorization and audit.

**Outputs:** Raw-material issue, processing batch, process/quality logs, yield/loss, finished-goods lots, packaging records, stock movements, release status.

**Exceptions/open rules:** Formula/recipe versioning; yield tolerance; rework; batch split/merge; recall; failed test; shelf-life calculation; outsourced processing.

## 10. Customer billing and month-end settlement

**Trigger:** Billing period closes or an authorized interim bill is requested.

1. Identify customer and billing period.
2. Gather eligible fulfilled subscription quantities, POS/ad-hoc credit sales, charges, returns, discounts, and adjustments.
3. Apply effective prices and billing rules that were valid for each transaction.
4. Reconcile delivery records against billable quantities.
5. Calculate opening balance, period charges, credits, receipts, and closing amount due/advance.
6. Generate statement and, where legally/applicably required, invoice document.
7. Publish/share statement through authorized channel.
8. Record customer dispute or correction request.
9. Apply approved credit/debit adjustment without rewriting the original posted transaction.
10. Carry closing balance forward.

**Outputs:** Billing run, bill lines, statement/invoice record, receivable entries, adjustments, customer balance snapshot.

**Exceptions/open rules:** Mid-period subscription change; missed/partial delivery; advance payment; payment received after close; customer dispute; backdated correction; tax applicability; statement-only versus tax invoice.

## 11. Payment receipt and reconciliation

**Trigger:** Money is received or a payment claim is submitted.

1. Select payer/customer/supplier and related open balance or transaction.
2. Record amount, date/time, payment mode, receiving account/QR/cash location, reference, and recorder.
3. For cash, record custody and handover/reconciliation where applicable.
4. For UPI/bank transfer, store the reference/evidence available to the business.
5. Mark manually entered payments as recorded/pending verification unless verified through an approved evidence or integration workflow.
6. Match verified bank/provider transactions to recorded receipts.
7. Allocate receipt to one or more outstanding items according to policy.
8. Record excess as advance/unallocated credit where supported.
9. Reconcile unmatched, duplicate, reversed, failed, or disputed entries.
10. Post financial effects exactly once.

**Outputs:** Receipt/payment record, allocation, reconciliation item, customer/supplier ledger effect, audit record.

**Exceptions/open rules:** Shared personal bank account; one UPI payment for multiple customers; partial payment; wrong payer reference; duplicate UTR; reversal/refund; cash shortage; offline collection.

## 12. Branch transfer

**Trigger:** A branch requests stock from another branch/location.

1. Create transfer request with source, destination, products, and quantities.
2. Validate permission and available stock.
3. Reserve or issue stock from source.
4. Record the actual handover to a carrier/custodian, dispatch time, vehicle/container/seal and required quantity/condition evidence under the configured custody assurance profile.
5. Record transit custody and each intermediate carrier/holding handover where applicable.
6. Mark quantity in transit under stock policy; keep physical custody evidence separate from the stock ledger.
7. Receive at destination; capture actual receiver, time, quantity/condition and required evidence. Record accepted quantity and variance separately.
8. Complete the handover only for acknowledged/accepted quantities; preserve pending/disputed custody and create discrepancy workflow for the remainder.
9. Preserve linked source/destination stock ledger and custody events without duplicating stock effects.

**Outputs:** Transfer request, source issue, in-transit record, destination receipt, variance/claim.

**Exceptions/open rules:** Partial receipt; lost/damaged goods; missing carrier/receiver acknowledgement; custody dispute; temperature/condition excursion; transfer cancellation; external liability/insurance; branch closure; inter-entity transfer. Custody evidence follows DOM-CUST-001; legal ownership/liability remains external-review dependent.

## 13. Employee/field activity workflow

**Trigger:** A staff member is assigned operational work.

1. Maintain employee/personnel profile separately from login account.
2. Assign explicit, scoped permission bundles and/or direct permission grants within the assigning authority's delegation; roles are optional editable permission bundles and do not independently grant access.
3. Create shift/availability and work assignment where workforce scheduling is used.
4. Assign route, collection session, POS register, or task.
5. Capture attendance/work completion according to configured policy.
6. Record cash, stock, equipment, and handover accountability where applicable.
7. An authorized user with the exact review/approval permission and scope reviews exceptions and approvals.
8. Feed approved records to payroll/HR and operational reports as configured.

**Outputs:** Employee profile, access assignment, shift/assignment, attendance/task, custody/handover, approval/audit.

**Exceptions/open rules:** Owner is also operator; one person performs multiple roles; temporary staff; shared devices; user disabled while assigned work remains open.

## 14. Management reporting and planning cycle

**Trigger:** Daily operations review, period close, or planning session.

1. Validate source transactions and close/reconcile operational periods.
2. Refresh reporting datasets according to defined freshness policy.
3. Review operational metrics: demand, fulfilment, route performance, procurement, quality, wastage, stock, and returns.
4. Review financial metrics: sales, gross margin, receivables, payables, cash/bank, expenses, and overdue balances.
5. Compare branches, products, routes, shifts, customers, and suppliers using permitted scopes.
6. Generate forecast inputs from approved historical data and known future events.
7. Produce forecast/projection with horizon, assumptions, confidence/accuracy metrics, and version.
8. Compare forecast with actual results and record model performance.
9. Create budget/scenario where enabled and compare plan against actual.
10. Record management action/owner where workflow tracking is required.

**Outputs:** Reports, KPI snapshots, forecast versions, scenario/budget versions, variance analysis, action records.

**Exceptions/open rules:** Missing historical data; abnormal seasonality; new branch; product discontinuation; manual overrides; data correction after report publication; cross-branch permission.

## 15. Cross-workflow invariants

- Every material transaction is associated with the correct tenant and, where relevant, branch/location.
- Posted stock and financial effects are not silently overwritten.
- Corrections use traceable reversal/adjustment mechanisms.
- Retry of the same command must not create duplicate sale, delivery, payment, receipt, or stock movement.
- Concurrent actions must not oversell stock, double-allocate a route stop, or double-post a receipt.
- A subscription definition is not itself proof that a delivery occurred.
- A dispatch is not proof that a customer received the goods.
- A manually entered UPI/bank receipt is not automatically verified.
- A report must state its data scope, period, and freshness.
- Forecasts must be distinguished from actuals and budgets.

## 16. Source notes

NDDB's published procurement workflow describes producer identification, quantity measurement, sampling/testing, rate calculation, transaction receipt, and payment. NDDB also describes chilling, transport, receiving checks, and subsequent processing as distinct activities. These inform the workflow separation above:
- https://beta.nddb.coop/wp-content/uploads/2025/11/Manual_Component_A_Strengthening_Milk_Procurement_Infrastructure.pdf
- https://www.nddb.coop/ccnddb/milkipedia

FSSAI's milk-sector guidance and regulations identify hygiene/handling controls relevant to milk and milk products; applicability and exact legal obligations must be separately verified:
- https://www.fssai.gov.in/business/guidance
- https://fssai.gov.in/food-law/regulations