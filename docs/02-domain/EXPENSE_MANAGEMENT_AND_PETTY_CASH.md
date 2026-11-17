# Expense Management and Petty Cash

**Status:** Working baseline — domain requirements

## 1. Objectives and principles
Provide a simple expense workflow for owner-operated dairies and controlled multi-user workflows for larger tenants. Do not require a dedicated finance employee, corporate card, payment gateway or GST registration. Expense capture, approval, payment and accounting posting are distinct events.

## 2. Expense categories and sources
Support configurable categories such as rent, electricity, water, refrigeration/cooling, fuel, vehicle maintenance, packaging, cleaning, repairs, telephone/data, professional fees, bank charges, staff welfare, travel, marketing and miscellaneous operating costs. Categories are tenant-configurable and mapped to ledger accounts through versioned posting rules.

Sources include direct vendor bill, cash purchase, bank/UPI payment, employee reimbursement claim, recurring expense, petty-cash voucher, advance settlement and approved journal adjustment. Record vendor/employee, branch/location, expense date, business date, due date where relevant, amount, tax details when applicable, payment method/account, reference, narration and evidence attachment.

## 3. Lifecycle
Draft → Submitted → Reviewed/Approved or Rejected → Recorded/Posted → Paid/Partially Paid → Reconciled/Closed. A paid expense can remain unreconciled until external evidence is matched. Rejected claims retain reason and history. Posted records are not silently edited or deleted; correct through reversal/credit/adjustment linked to the original.

## 4. Approval and policy
Configurable approval rules may consider amount thresholds, category, branch, claimant, budget availability and risk. The creator should not approve their own claim when separation of duties is enabled/required by tenant policy. Very small tenants may use owner approval or an explicitly configured no-approval workflow, while audit history remains mandatory.

## 5. Petty cash
Support one or more cash floats/custodians per branch or location. Record opening float, cash-in/top-up, expense voucher, cash return, transfer between custodians and physical cash count. Expected balance is derived from posted movements. Count variance requires reason and approval; never overwrite cash balance.

## 6. Advances and reimbursements
Support employee/vendor advances, claim submission, receipt attachment, settlement against advance, return of unused amount, additional reimbursement and outstanding ageing. Prevent settlement above the available advance/claim amount. Track personal out-of-pocket claims separately from business cash disbursements.

## 7. Accounting and controls
Each approved expense creates a posting instruction using a versioned mapping. Typical debit expense/asset/tax input and credit cash/bank/payable/employee payable are examples only; actual treatment depends on nature, capitalization policy, tax applicability and accounting review. Prevent duplicate postings with source-event idempotency. Preserve invoice/reference, supplier, branch/cost centre and tax evidence.

## 8. Reports
Expense register by category/vendor/branch/employee; recurring expense schedule; approval ageing; petty-cash book and variance; employee claim and advance ageing; unpaid expense/payable; budget-versus-actual; tax-evidence exceptions; ledger drill-down.

## 9. Acceptance scenarios
1. Record a cash expense without vendor GST registration assumptions.
2. Submit and approve a staff reimbursement with evidence.
3. Reject a claim with reason and retain audit history.
4. Settle an advance partly and return unused funds.
5. Reconcile petty cash count and approve variance.
6. Prevent duplicate posting after a retry.
7. Reverse a posted expense without deleting original.
8. Restrict approvals and branch visibility by role.
9. Generate category/branch reports that drill down to source vouchers.

## 10. Open decisions
Expense approval thresholds; evidence requirements by category; recurring expense generation; petty-cash float policy; capitalization/fixed-asset boundary; tax evidence validation; retention and statutory requirements.