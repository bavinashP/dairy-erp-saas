# Cash, Bank Accounts and Financial Reconciliation

**Status:** Recommended financial operations baseline — evidence-first payment and reconciliation controls selected; provider formats remain evidence-gated

## 1. Operating assumptions
A dairy may accept payments into a personal bank account, business current/savings account, UPI QR linked to either, or cash drawer. Payment gateway integration is optional. The system must record the actual receiving destination without forcing every tenant to have a corporate bank account or payment aggregator.

## 2. Account and custody registry
Maintain tenant-scoped financial account records: cash drawer/float, bank account, UPI receiving identity/QR reference, gateway settlement account and other supported clearing accounts. Store only necessary details; mask account identifiers in normal UI. Separate display label, account holder, bank/branch/IFSC where needed, masked account number, UPI VPA/QR reference, branch/custodian, active dates and verification status. Never store internet banking credentials, UPI PINs, OTPs or card PINs.

Personal versus business account classification is explicit metadata, not a legal conclusion. Provide clear warning that recording a personal account does not determine tax or legal treatment.

## 3. Cash management
Support opening float, cash receipt, cash payment, cash transfer, shift handover, cash count and closing. Every movement references its source receipt/expense/POS/refund or approved transfer. Drawer expected balance is calculated from movements. Short/excess cash is recorded as a variance with reason, actor and approval. Do not permit direct balance edits.

## 4. Bank and UPI transaction evidence
Record bank statement lines or manually entered external transaction evidence with transaction date, value date if supplied, description, debit/credit amount, reference/UTR, account, source file and import batch. UPI reference entered by a user remains unverified until matched to reliable statement/provider evidence. Distinguish payment recorded, externally confirmed, statement-imported and reconciled.

## 5. Statement import
Design an adapter-based import pipeline for supported bank statement formats (CSV, XLSX, PDF only where reliable extraction is possible) and provider settlement files. Preserve original file, checksum, import profile/version, source row number, parsed values and validation outcome. Do not assume one universal Indian bank format. PDF extraction may require manual review. Never silently discard unrecognized rows.

## 6. Reconciliation workflow
Select account and period → import/enter statement → normalize rows → suggest matches → user confirms or rejects → classify unmatched items → approve adjustments if required → close reconciliation. Matching may consider amount, date range, UTR/reference, counterparty, narration and account. Exact matching rules and tolerances are configurable and auditable.

Support one-to-one, one-to-many and many-to-one matches where business evidence supports them (e.g. settlement batches and grouped deposits). Distinguish bank charges, interest, refunds, reversals, cash deposits, transfers and unidentified items. Reconciliation links evidence; it must not rewrite source receipts/payments.

## 7. Transfers and settlement clearing
Transfers between own accounts require linked debit and credit sides and must not be treated as income/expense. Gateway settlement gross amount, fees, tax on fees and net bank credit must be separately represented when a gateway is used. UPI QR paid directly into a bank account must not be modelled as a gateway settlement merely because UPI was used.

## 8. Accounting integrity
Reconciled statement lines and source transactions retain stable IDs. Duplicate imports are detected using file checksum plus row-level/account/date/amount/reference fingerprints, with manual exception handling for legitimate repeated amounts. Adjustments post through approved accounting rules. Reopen a closed reconciliation only through permissioned workflow with reason and audit.

## 9. Reports
Cash book, bank book, account-wise statement, unreconciled items, UPI reference exceptions, cash count variance, transfer register, gateway settlement reconciliation, bank charges, reconciliation completion and ageing.

## 10. Acceptance scenarios
1. Record receipt into a personal UPI-linked bank account without gateway.
2. Import bank statement and match by UTR and amount.
3. Leave a user-entered UPI reference unverified until evidence exists.
4. Match multiple receipts to one deposit with audit trail.
5. Record own-account transfer without inflating income/expense.
6. Reconcile gateway gross/fee/net settlement separately.
7. Detect duplicate statement import.
8. Record and approve cash shortage.
9. Reopen closed reconciliation only with authorization.

## 11. Configuration and evidence dependencies
- Supported statement formats, bank feeds and import frequency are adapter/provider choices verified through sample-file PoCs; manual file import remains supported.
- Match confidence and auto-match thresholds are configured per account/source and must retain evidence and exceptions. Weak or ambiguous matches require review.
- Account ownership/verification status is separate from account registration; do not infer ownership from a label or uploaded screenshot.
- Cash count and closing policies are configurable by cash location/custodian; physical count differences require separate review/approval.
- UPI reference, manually entered UTR or screenshot is not proof of bank settlement. Use provider/bank evidence and reconciliation status under ODR-015.
- Personal-account disclosure requires data-minimization, explicit access and later legal/privacy review. No account details should be exposed to users without a business need/permission.
- External bank-feed integrations remain optional; core receipt recording and statement import must work without a provider API.