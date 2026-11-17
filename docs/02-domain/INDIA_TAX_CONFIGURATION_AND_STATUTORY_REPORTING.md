# India Tax Configuration and Statutory Reporting

**Status:** Working baseline — requirements subject to current professional/legal verification

## 1. Product principles
GST registration must not be mandatory for every tenant. Tax treatment depends on legal person, registration status, supply classification, product/HSN or service classification where applicable, place/time of supply, exemptions/notifications, transaction type and current law. The software must not assume all milk/dairy products have one uniform tax treatment.

This document defines software capabilities, not tax advice. All rates, exemptions, thresholds, return requirements, invoice rules and due dates must be verified against current official sources and reviewed by a qualified Indian tax professional before release.

## 2. Tax profile
Maintain effective-dated tenant tax profile: registration status (not registered, regular, composition or other supported status subject to validation), GSTIN where applicable, registered legal/trade name, registration state, effective dates, filing frequency metadata and branch/place-of-business registrations where relevant. Do not force a GSTIN field for unregistered businesses. Store verification source/time/status and restrict changes.

## 3. Product and transaction tax classification
Maintain versioned tax classification per product/service and transaction context, including HSN/SAC where required, applicable rate components, exemption/zero-rated/non-taxable/other classification as validated, place-of-supply logic and reason/source. Never hardcode a rate into product master without effective date and evidence. Historical invoices retain the exact applied classification and calculation snapshot.

## 4. Invoice and credit/debit document controls
Document templates and fields must be conditional on tenant registration and transaction applicability. Support configurable invoice numbering series, date, supplier/recipient details, tax breakdown and required references when applicable. Separate commercial receipt, internal statement, bill of supply, tax invoice, credit note and debit note. Cancellation/amendment must follow a controlled linked workflow; never silently rewrite issued documents.

## 5. Tax calculation engine
Use deterministic decimal arithmetic and explicit rounding policy. Preserve taxable value, rate, tax components, cess/other applicable components, discounts, freight/charges and line/document rounding separately. Handle intra-state/inter-state classification through reviewed place-of-supply rules. Tax engine version and input facts must be retained for reproducibility.

## 6. Input tax and supplier evidence
Capture supplier tax identity/status, invoice number/date, tax amounts, eligible/ineligible input tax metadata and evidence. Do not automatically treat every supplier tax amount as recoverable input credit. Eligibility, blocked credits, reversals and matching rules require verified legal/accounting policy.

## 7. Return and reconciliation readiness
Design data extraction and reconciliation views for outward supplies, inward supplies, credit/debit notes, tax liability, eligible input tax, payments and ledger balances. Provide exportable registers and reconciliation exceptions. Do not claim direct GST portal filing or API integration unless a supported authorized integration is separately implemented and verified.

## 8. Statutory rule registry
A versioned registry should capture jurisdiction, authority, source URL/document, published/effective dates, applicability conditions, formula/rate/threshold, calculation precision, filing output mapping, reviewer, verification date and supersession history. Changes must not retroactively alter finalized transactions.

## 9. Access, audit and retention
Restrict GSTIN, tax documents and statutory exports. Log profile changes, tax classification changes, invoice issuance/cancellation, tax calculation and export. Retention/deletion must follow reviewed statutory and privacy requirements; do not invent one universal retention period.

## 10. Reports
Tax classification register; sales/purchase tax summary; invoice/credit/debit register; input-tax evidence and eligibility exceptions; tax liability by period; branch-wise tax data where legally relevant; reconciliation between tax registers and GL; missing/invalid supplier evidence.

## 11. Acceptance scenarios
1. Create a tenant without GST registration and complete permitted non-GST workflows.
2. Configure a GST-registered tenant with effective-dated profile.
3. Apply different reviewed product classifications and preserve snapshots.
4. Reject posting when required tax facts are missing or contradictory.
5. Generate correct document type based on reviewed policy.
6. Issue credit note without editing original invoice.
7. Export period tax registers and reconcile totals to GL.
8. Prevent statutory rule update from changing closed historical documents.
9. Show an explicit unverified status when legal applicability is not established.

## 12. Official research sources to maintain
- GST portal: https://www.gst.gov.in/
- CBIC GST: https://cbic-gst.gov.in/
- GST Council: https://gstcouncil.gov.in/
- Relevant notifications, circulars, rules and Acts published by competent authorities.

Every implementation release must refresh the source register and obtain qualified review for affected tax rules.