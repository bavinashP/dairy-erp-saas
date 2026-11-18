# Billing, Receivables, Payments and Reconciliation

**Status:** Recommended domain baseline — billable-source separation and payment evidence rules selected; commercial/legal details remain configurable/evidence-gated  
**Scope:** Customer billing for recurring dairy supply, counter/POS sales and other approved sales; payment capture without assuming a payment gateway.

## 1. Sales and billing channels

Support distinct source channels:
- Subscription/recurring supply.
- Counter/POS sale.
- One-time order or ad-hoc delivery.
- Approved manual charge or adjustment.

A source sale/fulfilment, invoice, customer statement, receipt and accounting journal are separate records with explicit relationships. Do not create duplicate revenue when a subscription occurrence is converted to a bill or when a statement summarizes already invoiced transactions.

## 2. Subscription billing

Support configurable billing cycles and cut-offs, including monthly billing in arrears where selected. The billing engine must consume authoritative fulfilled/billable quantities and approved price/tax versions.

Handle:
- Morning/evening and multiple shift supply.
- Partial, missed, failed and substituted delivery.
- Skip/pause/resume effective dates and cut-off outcomes.
- Product/quantity/rate changes mid-cycle.
- Credits, adjustments and disputes.
- Previous balance, receipts and unapplied advances.
- Bill preview, review, posting and customer statement.

Do not bill planned quantity as delivered quantity unless the tenant explicitly configures and approves a different commercial rule.

## 3. POS and counter billing

Support fast counter workflows for cash, UPI QR, direct bank transfer, credit/account sale where permitted, discounts and returns. A gateway must not be required to record an externally received UPI payment.

Provide configurable receipt/invoice output and a clear distinction between tax invoice, bill of supply, commercial receipt and internal statement according to verified applicability. Do not label every generated document a tax invoice.

## 4. Invoice and document lifecycle

Candidate lifecycle:
Draft → Reviewed/Approved where required → Issued/Posted → Partially Settled/Settled → Adjusted or Credited.

Issued financial documents must not be silently edited or deleted. Correct them through linked credit/debit notes, cancellation or adjustment according to the applicable document and legal policy. Document numbering, date, tax fields and retention require India-specific legal review.

## 5. Payment capture

A payment record may represent:
- Cash received.
- UPI QR payment to a personal or business bank account.
- Direct bank transfer/NEFT/IMPS/RTGS.
- Card or payment gateway settlement if separately enabled.
- Cheque or other tenant-configured method.

Capture amount, method, payer/reference where available, receiving account/cash location, transaction time, value date if known, evidence/UTR/reference, recorder and verification status.

Payment statuses should distinguish recorded/unverified, confirmed by evidence, reconciled, failed/reversed/refunded. A user-entered UPI reference is not equivalent to bank confirmation. Do not claim automatic confirmation unless an authorized provider/bank integration supplies it.

## 6. Allocation and customer account

Support allocation of one receipt across one or more invoices, partial payments, advance/on-account receipts, unapplied balances, refunds and reversals.

Allocation must be auditable and concurrency-safe. A receipt cannot be allocated beyond its unallocated amount. Customer outstanding is derived from posted charges, credits, receipts and allocations.

## 7. Reconciliation

Support manual and assisted matching against bank statements, cash closing and provider settlement files where available. Matching criteria may include amount, date, UTR/reference, payer and account.

Unmatched, duplicate, reversed, short, excess and unidentified transactions must enter exception queues. Reconciliation must not alter source payment evidence; it creates a matching/adjustment decision with actor and reason.

## 8. Supplier and farmer payment relationship

Supplier/farmer payable and settlement originate in procurement/collection workflows. Their payments use the shared payment and accounting foundation but remain distinct from customer receipts. Support advances, partial settlement, deductions, refunds and payment allocation.

## 9. Pricing, tax and rounding

Prices, discounts, charges, tax classification and rounding must be snapshotted at document creation/posting. Tax applicability depends on product, supply, registration and transaction facts; it must be determined through an effective-dated, reviewed tax policy. GST registration must not be mandatory for every tenant.

INR is the product's base accounting currency for this India-focused scope. Multi-currency support is not a current requirement.

## 10. Notifications and collections

Configurable reminders may include bill generated, due date approaching, overdue, receipt acknowledged and payment mismatch. Reminders must respect tenant communication settings and applicable consent/privacy rules. Do not expose another customer's balance or payment details.

## 11. Reports

- Daily counter sales and collection.
- Subscription billing register.
- Customer ledger and statement.
- Receivables ageing and overdue.
- Payment method and receiving-account summary.
- Unallocated advances and receipts.
- Refunds, credits and adjustments.
- UPI/bank reference reconciliation.
- Branch/channel/customer/product profitability inputs.
- Billing exceptions and delivery-to-billing variance.

## 12. Acceptance scenarios

1. Generate a monthly subscription bill from actual billable fulfilment.
2. Apply a mid-month rate change only from its effective date.
3. Skip a scheduled supply before cut-off and verify billing outcome.
4. Record a POS cash sale without customer account login.
5. Record UPI QR payment without gateway integration.
6. Record bank transfer with UTR and reconcile later.
7. Accept partial receipt and allocate across invoices.
8. Keep an advance unapplied until allocation.
9. Issue credit adjustment without editing original invoice.
10. Refund or reverse payment with linked source.
11. Prevent duplicate payment import/retry.
12. Reconcile bank statement and identify unmatched entries.
13. Restrict users to authorized branch/customer financial data.
14. Ensure receipt, invoice, statement and journal are not duplicated.

## 13. Configuration and evidence dependencies

- Billing cycle, period, cut-off and customer statement grouping are Tenant-configurable, versioned and effective-dated; source fulfilment and billable quantity remain authoritative.
- Credit limits, overdue restrictions, deposits and approval thresholds are Tenant policies with explicit permissions and audit. Unpaid status never silently becomes a write-off.
- Invoice/credit/debit/statutory document type and numbering depend on verified legal/tax profile; ODR-003/016 post-project review remains the authority for legal applicability.
- Manual UPI/bank entry and uploaded screenshots are evidence/claims only; they do not create provider/bank-verified status. Reconciliation and provider status follow ODR-015/section 16.18.
- Cash custody and shift closing are explicit custody/close workflows. A cash receipt is not a bank settlement event.
- Refund/reversal/dispute requires separate permissions, source linkage and maker-checker where policy demands.
- Payment gateways/bank imports are optional adapters; the core workflow works without them and provider selection requires capability/cost tests.
- Customer statements summarize source documents; they must not duplicate invoices or revenue. Advances are allocated explicitly and never exceed available balance.
