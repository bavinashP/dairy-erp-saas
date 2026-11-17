# Tally Field and Voucher Mapping Specification

**Status:** Proposed technical contract — validate through version-specific TallyPrime proof of concept

## 1. Mapping principles
SaaS IDs are permanent canonical identities; Tally identifiers are external references. Mapping scope includes tenant, SaaS accounting entity/books and exact Tally company identity. Do not match solely by display name. Prefer stable external identifiers/GUIDs where available and verified; otherwise require reviewed composite matching and explicit confirmation. Mapping is versioned, effective-dated, auditable and reversible through supersession. Voucher export must preserve debit/credit lines, dates, party, tax, dimensions, narration, source references and reversal relationships to the extent supported by the target release. Unsupported fields must be reported as unsupported/lossy; never silently discard them. Exact XML/JSON tags, required fields and semantics are adapter-version-specific and must be established by tested samples.

## 2. Company identity mapping
Store SaaS accounting entity ID, tenant ID, Tally company name, company GUID/identifier when exposed, financial year, base currency, feature/configuration fingerprint, Tally product/release/build, agent ID, verification timestamp and user confirmation. A company name alone is not sufficient protection against selecting the wrong company.

## 3. Master mapping matrix

| SaaS canonical master | Tally-side concept | Required mapping metadata | Validation |
|---|---|---|---|
| Ledger account | Ledger | External identity, name, parent group, nature, opening balance policy | Group/nature compatibility; no ambiguous duplicate |
| Account group | Group | External identity, parent group, classification | Parent exists; hierarchy valid |
| Customer account | Party ledger | Party identity, receivable group, bill-wise settings if used | Party and bill allocation behavior verified |
| Supplier/farmer account | Party ledger | Party identity, payable group, bill-wise settings if used | Payable and settlement mapping verified |
| Voucher type | Voucher Type | Tally voucher type, numbering policy, effective version | Voucher type exists and is enabled |
| Cost centre | Cost Centre | Name/identity, category/hierarchy | Feature enabled and allocation supported |
| Tax classification | Tax ledger/configuration | GST/tax ledger references, classification and effective date | Tax configuration matches company and period |
| Stock item | Stock Item | Item identity, unit, group, tax metadata | Unit and inventory configuration compatible |
| Unit of measure | Unit | Symbol, formal name, decimal precision | Conversion/precision verified |
| Stock location | Godown | External identity and hierarchy | Inventory feature and location enabled |
| Batch/lot | Batch allocation fields | Lot identifier, expiry/manufacturing metadata | Release-specific behavior tested |

## 4. Voucher mapping matrix

| SaaS source event | Candidate Tally voucher family | Mapping notes |
|---|---|---|
| Customer invoice / POS sale | Sales | Decide invoice-wise vs consolidated daily voucher; never export both for same revenue |
| Customer receipt | Receipt | Preserve customer ledger, invoice allocation and payment reference |
| Customer refund | Payment or supported adjustment | Test linkage and original receipt traceability |
| Supplier purchase | Purchase | Inventory vs accounting invoice mode must match chosen ownership policy |
| Supplier/farmer payment | Payment | Separate procurement calculation from actual payment |
| Operating expense paid | Payment | Expense ledger, cash/bank ledger and evidence reference |
| Expense payable accrued | Journal or supported purchase voucher | Avoid double posting when later paid |
| Customer credit note | Credit Note / configured voucher type | Original invoice reference and tax treatment required |
| Supplier debit/credit adjustment | Debit/Credit Note or configured type | Validate sign and ledger effects |
| Payroll journal | Journal | Aggregate or employee-level detail policy; protect sensitive data |
| Cash/bank transfer | Contra or configured voucher type | Must not affect income/expense |
| Depreciation/accrual/provision | Journal | Accounting policy and period control |
| Opening balances | Opening balance on masters or journal | Choose one method per migration; never duplicate |

Voucher family names are conceptual candidates, not guaranteed exact names or behavior in every company configuration/release.

## 5. Canonical voucher payload contract
Each outbound voucher should carry batch ID, record ID, idempotency key, tenant and accounting entity IDs, Tally company mapping ID, source module/type/record ID/version, voucher type/number/date/business date/posting date/financial period, narration, party account, optional invoice allocation, ordered lines, ledger account ID, debit and credit amounts, line narration, cost-centre/branch dimensions, tax references, source-line reference, reversal relationship and evidence references. Include schema version. Amounts use decimal strings at the integration boundary; never binary floating-point serialization. Each voucher must balance before export eligibility.

## 6. Direction and ownership matrix
For every master and transaction class, configure system of record and direction:
- SaaS-owned operational transactions: SaaS → Tally export.
- Tally-originated opening balances or legacy vouchers: Tally → SaaS only through explicit approved import/migration mode.
- Shared masters: explicit ownership per tenant and class; do not auto-overwrite.
- Tally-originated manual journal: import only when enabled, mapped and approved.
- Posted conflict: exception and human resolution; no last-write-wins.

## 7. Import and export sequence
Pair/authorize desktop agent and verify company; retrieve mapping profile and supported capabilities; validate accounting period, masters, ledger nature, tax and dimensions; preview master changes and voucher totals; obtain approval; import masters before dependent vouchers when required; process vouchers deterministically; capture response; read back/query where supported; compare counts, references and debit/credit totals; mark complete only after required verification.

## 8. Idempotency and duplicate controls
Stable idempotency key should derive from immutable SaaS source identity/version plus target company and export purpose. A changed source version after posting must create a correction/reversal workflow, not silently create a second normal voucher. Voucher number alone is not a reliable idempotency key. If Tally does not expose a reliable unique identifier for the chosen workflow, maintain a durable mapping registry and verify before retry.

## 9. Data loss and compatibility report
For each batch report mapped/unmapped masters; supported/transformed/omitted/rejected fields; voucher count and debit/credit totals; tax/dimension/stock detail preservation; duplicate candidates and conflicts; target Tally release, adapter version and company fingerprint; warnings requiring user/CA review. Material lossy transformation must require explicit acknowledgement or be blocked by policy.

## 10. Proof-of-concept test fixtures
Create synthetic fixtures for sales, receipts, purchases, farmer settlement, expenses, payroll journal, contra transfer, tax invoice, credit note, stock item, godown, batch/expiry, cost centre, opening balances and reversal. For each target release export fixture into a disposable company, read back and compare fields, compare ledger balances and trial balance, verify bill-wise allocation/tax/inventory/dimensions, retry identical batch, interrupt transfer, change company selection and test wrong-company block, test Marathi/Indian-language names and Unicode, and record exact release/build, TDL dependency, configuration and limitations.

## 11. Open decisions
Initial Tally release support matrix; XML/JSON selection policy; TDL requirement; detailed versus summarized POS/subscription vouchers; stock/inventory synchronization depth; cost-centre mapping; opening balance import; direction for Tally-originated entries; supported field loss policy; desktop agent OS and update strategy.