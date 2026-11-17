# Accounting, Double-Entry Bookkeeping and Financial Controls

**Status:** Working baseline — domain requirements  
**Scope:** Tenant-level accounting foundation for Indian dairy businesses, with explicit Tally-compatible data exchange readiness.  
**Important:** This is a product/domain design baseline, not legal, tax, or audit advice. Applicable accounting and statutory requirements require qualified review.

## 1. Accounting architecture principles

1. Use double-entry bookkeeping as the accounting foundation.
2. Every posted journal must balance: total debits equal total credits in the same accounting currency.
3. Operational modules must not directly edit ledger balances.
4. Operational events create validated financial posting instructions; the accounting ledger records the resulting journal entries.
5. Posted entries are immutable. Corrections use linked reversal and replacement/adjustment entries.
6. Preserve source transaction identity, actor, timestamps, business date, posting date, accounting period and audit history.
7. Tenant books are isolated. Branch, cost centre, department, location and other dimensions are explicit analytical dimensions, not substitutes for tenant/legal-entity boundaries.
8. Support small owner-operated businesses with simple workflows while retaining a sound ledger underneath.
9. Tally integration is an adapter/export concern; the internal accounting model must not be shaped only around one vendor's schema.

## 2. Accounting scope and operating profiles

Support configurable profiles:
- Basic cashbook and customer/supplier balances for very small operators.
- Full double-entry general ledger for businesses requiring formal books.
- Inventory-integrated accounting for stock and processing operations.
- Branch-wise cost/profit reporting.
- Multiple books/company mapping only where the tenant's actual legal/business structure requires it.

The system must not assume every tenant is GST-registered, has a separate legal entity per branch, uses a payment gateway, or employs an accountant.

## 3. Core accounting entities

The canonical model should include:
- Accounting entity / books scope.
- Financial year and accounting period.
- Chart of Accounts (CoA).
- Account group and ledger account.
- Journal entry and journal line.
- Voucher/document type and numbering series.
- Posting rule/template and version.
- Subledger account/control account.
- Cost centre and optional analytical dimensions.
- Tax code/rate/classification where applicable.
- Reconciliation statement and matched item.
- Period close/reopen record.
- Opening balance/import batch.
- Attachment/evidence reference.
- External system mapping and synchronization record.

Each entity must have stable internal IDs independent of account names or Tally identifiers.

## 4. Chart of Accounts

Provide a tenant-specific chart derived from a versioned starter template, not one immutable universal chart. Support account groups, parent-child hierarchy, account nature, normal balance, control-account designation, active dates and restrictions.

Potential account families include assets, liabilities, equity/capital, income, direct costs, expenses, cash/bank, customer receivables, supplier payables, inventory, taxes, advances, deposits and loans. Exact account names, statutory classifications and reporting mappings must be configurable and reviewed.

Prevent unsafe changes:
- Do not delete accounts with posted history.
- Deactivate or merge only through controlled workflows.
- Preserve historical account mapping.
- Restrict direct postings to control accounts where subledger reconciliation is required.
- Validate that an account is compatible with the journal line and posting source.

## 5. Double-entry journal model

A journal entry must contain:
- Unique internal ID and human-readable voucher number.
- Tenant/books scope.
- Voucher type and source module/reference.
- Voucher date, business date, posting timestamp and accounting period.
- Narration and optional attachment/evidence.
- Status: draft, pending approval, posted, reversed or cancelled before posting.
- Created/approved/posted actor references.
- External reference and integration correlation ID where applicable.

Each journal line must contain:
- Ledger account.
- Debit or credit amount (never both positive on the same line).
- Currency fixed to INR for this India-focused product baseline.
- Optional cost centre/branch/department/project dimensions.
- Party/subledger reference where applicable.
- Tax classification/reference where applicable.
- Line narration and source-line reference.

A posted journal must contain valid lines, at least one debit and one credit, and exact balanced totals under the defined precision policy. Do not use floating-point arithmetic for money.

## 6. Operational posting contracts

Operational modules own their source events; accounting owns financial posting. Define versioned posting rules for:
- Counter sale and sales return.
- Subscription billing, credit note and adjustment.
- Customer receipt, advance, refund and write-off.
- Supplier purchase, purchase return and debit/credit adjustment.
- Farmer milk procurement and settlement.
- Supplier/customer advance and allocation.
- Inventory valuation changes, stock loss and write-off.
- Processing input, WIP, output and production variance.
- Payroll, employee advance and reimbursement.
- Operating expenses, fixed assets and depreciation where in scope.
- Bank charges, interest and other financial transactions.

For each event define trigger, accounts, dimensions, tax handling, rounding, reversal, period restrictions and reconciliation control. Do not hardcode posting entries into UI screens or duplicate financial logic in multiple modules.

## 7. Subledgers and reconciliation

Customer receivables, supplier/farmer payables, inventory, cash and bank control accounts must reconcile to their detailed source subledgers.

A balance shown on a customer/supplier account is derived from posted documents, allocations, credit/debit adjustments and receipts/payments. Manual balance overwrite is prohibited. Opening balances must be imported as controlled, approved opening entries with source and cut-off date.

Support partial allocation, advance/unapplied receipts, payment on account, credit notes, refunds, write-offs with authorization and disputed items.

## 8. Accounting periods and close

Support configurable financial year and periods, with a default India-oriented financial-year template subject to confirmation. Period close should:
- Verify required reconciliations.
- Identify unposted/failed transactions.
- Validate journals and control-account balances.
- Record close actor/time and close checklist.
- Prevent ordinary backdated posting after close.
- Require controlled reopen/adjustment authority with reason and audit.

Do not silently mutate a closed period. Define whether late corrections are posted in an open period or through an approved reopen process.

## 9. Auditability and integrity

Maintain append-only audit history for creation, approval, posting, reversal, mapping, import/export and reconciliation. Capture before/after values for editable drafts and master changes. Posted financial facts remain immutable.

Use unique source-event references and idempotency keys so retries cannot create duplicate journals. Use database constraints and concurrency-safe posting; a pre-check alone is insufficient. Ensure source event and posting state cannot diverge silently: use atomic transactions or a durable outbox/retry/reconciliation pattern.

## 10. Reports

Minimum reports:
- General Ledger and account ledger.
- Trial Balance.
- Profit and Loss.
- Balance Sheet.
- Cash/Bank Book.
- Day Book / Journal Register.
- Receivables and ageing.
- Payables and ageing.
- Customer and supplier statements.
- Inventory valuation and stock-to-GL reconciliation.
- Branch/cost-centre profitability.
- Tax summaries where applicable.
- Period close and audit trail reports.

Each report must define date basis, accounting basis, dimensions, inclusion rules and drill-down to source vouchers. Reports must be reproducible for a closed period.

## 11. Acceptance scenarios

1. Post a balanced journal and reject an unbalanced journal.
2. Prevent direct mutation of a posted journal.
3. Reverse a posted entry with a linked reversal journal.
4. Post a POS sale and reconcile receivable/cash, revenue, tax and stock effects under configured policy.
5. Post a subscription invoice and later receipt with partial allocation.
6. Record farmer settlement and payment separately.
7. Reconcile customer/supplier subledgers to control accounts.
8. Prevent posting to a closed period without authorized process.
9. Retry an operational event without duplicate journal creation.
10. Preserve source IDs and audit references through correction.
11. Produce a trial balance whose debits and credits agree.
12. Export accounting records without losing voucher identity, dates, ledger mapping, dimensions or debit/credit amounts.

## 12. Open decisions

- Exact legal books scope when a tenant has multiple branches or entities.
- Starter CoA templates and account group taxonomy.
- Accounting basis and inventory valuation policy.
- Voucher numbering and approval controls.
- GST applicability and tax posting rules.
- Financial year and period close policy.
- Cost-centre/branch dimension model.
- Opening balance and historical migration policy.
- Which reports are in MVP versus later releases.
- Tally version compatibility and supported import/export profile.
