# Tally Desktop Synchronization and Data Exchange Architecture

**Status:** Proposed architecture baseline — requires product-owner approval and version-specific proof of concept  
**Scope:** Prepare Dairy ERP SaaS accounting and data contracts for a future separately installed desktop synchronization application that exchanges data with TallyPrime.

## 1. Product direction

The SaaS portal remains the authoritative operational system and its own accounting ledger. Tally is an optional external accounting destination/source for customers and auditors who use it. A separate desktop synchronization application may run on the customer's computer/network where TallyPrime is installed.

The SaaS backend must not require a direct inbound connection from the public internet to a customer's Tally machine. The desktop agent should initiate authenticated outbound communication to the SaaS service and communicate locally with an explicitly selected Tally company/instance.

## 2. Official Tally integration capabilities researched

The following statements are based on Tally's official Help documentation reviewed for this design:
- TallyPrime supports XML over HTTP integration; the Tally HTTP server is commonly configured on port 9000. The external application sends XML requests to the configured Tally endpoint. See https://help.tallysolutions.com/xml-integration/ and https://help.tallysolutions.com/pre-requisites-for-integrations/
- TallyPrime Release 7.0 documentation describes native JSON integration in addition to XML. JSON integration is version-sensitive and may require the relevant Tally configuration/TDL capabilities. See https://help.tallysolutions.com/tally-prime-integration-using-json-1/
- TallyPrime import workflows support masters and transactions; official guidance recommends importing masters before transactions when referenced masters are not already present. See https://help.tallysolutions.com/import-data-from-xml-or-json/
- TallyPrime can export masters, vouchers, reports and other data in multiple formats including XML, JSON and Excel, depending on release/features. See https://help.tallysolutions.com/export-data-in-tally/
- ODBC and TDL-based integration capabilities are also documented by Tally. Do not assume ODBC is a safe or complete write API for our synchronization design. See https://help.tallysolutions.com/integration-methods-and-technologies/

These vendor capabilities are not a guarantee that every Tally edition/release supports identical behavior. Compatibility must be tested against the customer's installed release and company configuration.

## 3. Synchronization topology

Conceptual flow:

Dairy ERP SaaS API ⇄ authenticated outbound desktop agent ⇄ local TallyPrime HTTP/XML or supported JSON interface

The agent:
- Is separately installable and upgradeable.
- Stores a device identity and tenant-scoped pairing credential securely.
- Lets the authorized user select the local Tally instance and exact company.
- Verifies active company identity before every import/export batch.
- Uses local loopback/LAN access only as explicitly configured; never exposes Tally's HTTP port publicly by default.
- Downloads signed/authorized export jobs from SaaS.
- Validates, previews and maps data before import.
- Reads Tally responses and produces a detailed result/exception report.
- Uploads export results, import acknowledgements and logs to SaaS.
- Supports pause, retry, resume, cancellation before posting and safe recovery.

## 4. Canonical accounting model is vendor-neutral

The portal must store its own stable internal:
- Ledger/account IDs and account hierarchy.
- Journal/voucher IDs and types.
- Voucher dates, business dates and posting dates.
- Debit/credit journal lines and amounts.
- Party/subledger identities.
- Branch/cost-centre/other dimensions.
- Tax classifications and source references.
- Attachments/evidence references.
- Reversal links and audit history.

Tally-specific names, GUIDs, master identifiers, voucher types and group mappings belong in an external mapping layer, never as the primary identity of the SaaS accounting model.

## 5. Mapping registry

Maintain tenant-scoped, versioned mappings for:
- SaaS accounting entity ↔ Tally company identity.
- SaaS ledger/account ↔ Tally ledger name/identifier/group.
- SaaS voucher type ↔ Tally voucher type.
- SaaS cost centre/branch ↔ Tally cost centre or other supported dimension.
- SaaS tax code/classification ↔ Tally tax ledger/configuration.
- SaaS stock item/unit/location/lot ↔ Tally stock master/location/batch where required.
- SaaS party/customer/supplier ↔ Tally party ledger.
- SaaS document/voucher number ↔ Tally voucher number/GUID/reference.

Mappings must retain external IDs, source system, company scope, effective dates, mapping status, last verification and conflict resolution history. Name-only matching is unsafe; names can change and may not be unique.

## 6. Import/export modes

Support independently selectable modes:
1. SaaS → Tally: export approved accounting vouchers and required masters.
2. Tally → SaaS: import approved masters/opening balances/vouchers/reports only under an explicit ownership and conflict policy.
3. Audit package export: produce period-scoped vouchers, ledgers, trial balance and supporting references for auditor/CA review.
4. Reconciliation: compare totals and record mismatches without silently overwriting either system.

Do not enable unrestricted bidirectional synchronization by default. For each data class define its system of record and permitted direction. Avoid syncing the same operational sale into Tally both as detailed invoice and again as summarized daily voucher.

## 7. Batch protocol and idempotency

Every sync batch must have:
- Globally unique batch ID and tenant/device/company scope.
- Direction, data class and schema/adapter version.
- Created, approved, started and completed timestamps.
- Payload manifest, record count and cryptographic checksum.
- Source IDs and stable external references for each record.
- Idempotency key per exported/imported record.
- Dry-run/validation outcome and user approval where configured.
- Per-record result: created, matched, skipped, rejected, updated under explicit policy.
- Tally response/error details with sensitive data redacted.
- Retry and recovery state.

A retry must not create duplicate vouchers. Do not assume voucher number alone is a safe idempotency key. Use stable source reference and external GUID/mapping where supported; verify actual behavior in the target Tally release.

## 8. Master-before-voucher and dependency ordering

Where a voucher references masters not present in Tally, the adapter should:
1. Validate required company and feature configuration.
2. Resolve existing mappings.
3. Create or update approved masters if permitted.
4. Confirm master availability.
5. Import vouchers in dependency-safe order.
6. Read back or query imported results where supported.
7. Reconcile counts and debit/credit totals.

A failed master mapping must block dependent vouchers or place them in an exception queue; do not silently substitute a generic ledger.

## 9. Conflict and ownership policy

Every synchronized data class must declare:
- System of record.
- Direction allowed.
- Create/update/delete policy.
- Conflict detection method.
- Human approval requirement.
- Reversal/correction behavior.

Posted accounting entries are not automatically overwritten. Corrections should use reversal/adjustment workflows. If Tally and SaaS both contain posted entries, the adapter must detect conflicts and request explicit resolution rather than guessing which is correct.

## 10. Security

- Pair device to tenant using short-lived, revocable credentials.
- Store secrets in OS-protected credential storage where available.
- Encrypt transport with TLS and authenticate every agent request.
- Apply tenant and user permissions to export jobs and data classes.
- Bind local Tally communication to loopback or explicitly trusted LAN; do not expose port 9000 to the internet.
- Verify selected company identity to prevent accidental posting to the wrong company.
- Require confirmation for first connection, company changes, bulk imports and high-impact operations.
- Support device revocation, audit logs, software signing and secure updates.
- Minimize local payload retention and securely remove temporary files according to policy.

## 11. Audit and observability

Keep an immutable synchronization audit trail containing:
- Initiating user and paired device.
- Tally release/adapter version and company identity.
- Direction, batch ID and payload checksum.
- Record counts and debit/credit totals before and after transfer.
- Validation, import/export result and exception details.
- Mapping changes and approvals.
- Retry, cancellation and recovery events.

Provide a downloadable reconciliation report. Logs must avoid credentials, full bank details and unnecessary personal information.

## 12. Compatibility and proof-of-concept matrix

Before committing to a supported compatibility statement, test:
- TallyPrime versions/releases used by target customers, including legacy XML-only scenarios.
- XML file import/export and HTTP integration.
- JSON integration only where the installed release supports it.
- Masters, ledgers, groups, cost centres, voucher types and vouchers.
- Sales, purchase, receipt, payment, journal, credit/debit note and opening balances.
- Inventory/stock items, units, locations and batch fields where applicable.
- Duplicate/retry behavior and import exception reporting.
- Unicode/Indian-language names and special characters.
- Large batch performance, interrupted transfer and recovery.
- Wrong-company protection and company feature mismatch.
- Round-trip totals, trial balance and source-to-voucher traceability.

## 13. Acceptance scenarios

1. Pair a desktop agent with the correct tenant and revoke it.
2. Select one Tally company and verify identity before transfer.
3. Export a balanced SaaS journal as a Tally voucher.
4. Create required ledger masters before dependent vouchers.
5. Reject an unmapped ledger rather than silently substituting another.
6. Retry an interrupted batch without duplicate vouchers.
7. Import Tally vouchers with source identifiers and mapping evidence.
8. Detect conflicting edits in both systems.
9. Compare voucher count, debit/credit totals and trial balance after transfer.
10. Export a period audit package with source-to-voucher traceability.
11. Operate with XML-only Tally releases and enable JSON only after capability detection.
12. Prevent public exposure of local Tally HTTP service.

## 14. Open decisions

- Initial supported TallyPrime release range and legacy Tally support.
- Whether Tally → SaaS import is in first synchronization release or later.
- Which system owns customer/supplier/product masters when both systems are used.
- Detailed versus summarized sales voucher export.
- Branch mapping to Tally cost centres versus separate Tally companies.
- Whether a custom TDL package is required for advanced mappings/acknowledgements.
- Desktop agent operating systems, packaging and update mechanism.
- Audit package contents and CA/auditor workflow.
- Supported tax and inventory feature combinations.
- Vendor licensing/redistribution constraints and integration certification requirements.
