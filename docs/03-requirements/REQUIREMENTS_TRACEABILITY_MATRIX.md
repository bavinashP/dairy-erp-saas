# Requirements Traceability Matrix (RTM)

**Document ID:** RTM-001  
**Status:** Working traceability baseline  
**Authority:** FRS-001, ERS-001, domain specifications and Open Decisions Register.  
**Purpose:** Maintain bidirectional traceability from requirements to domain workflows, logical entities and verifiable acceptance evidence.

## 1. Traceability rules

1. FRS requirement IDs are the stable requirement keys; do not copy requirement wording into this matrix as a second authority.
2. A mapping to an entity is a candidate logical coverage mapping, not approval of a physical table or schema.
3. A test reference below is a proposed acceptance-test identifier. Detailed executable test cases are a later deliverable.
4. A domain document's candidate workflow or state is not approved merely because it appears in this matrix.
5. Open decisions remain open until explicitly approved and recorded in ODR.
6. Every requirement must eventually map to at least one acceptance test. Every implemented endpoint/event and data constraint must map back to requirements.
7. Changes to FRS/ERS must trigger an RTM impact review.

## 2. Coverage matrix

| Requirement range | Capability / scope | Authoritative domain source | Logical ERS coverage | Proposed acceptance suite | Open decisions |
|---|---|---|---|---|---|
| FRS-GEN-001..017 | API-first rules, tenant isolation, audit, idempotency, concurrency, localization, owner-only operations and product-wide configuration/extension compatibility | Project Vision; Actor and Access Model; Domain Dependency Map; Product Configurability, Customization, Extensibility and Scale Policy | Tenant, TenantMembership, AuditEvent, TenantConfiguration, Attachment, PermissionCatalogueEntry, ExtensionPermissionRegistration | AT-GEN-001..017 | ODR-004, 020, 026, 027, 028, 030..032, 035 |
| FRS-PLT-001..007 | Platform administration, tenant lifecycle, plans, entitlements, support access, offboarding | Project Vision; SaaS platform sections in Domain Landscape | PlatformPrincipal, Tenant, TenantSubscription, Plan, FeatureEntitlement, TenantEntitlementOverride, TenantBusinessProfile | AT-PLT-001..007 | ODR-001, 003, 025..028, 031 |
| FRS-IAM-001..018 | Identity, Tenant memberships, permission bundles/direct grants, scoped cross-Branch stock-task grants, immutable grant versions, approvals, revocation and task reconciliation | Actor and Access Model; Permission Catalogue and Assignment Governance; Shared-Site Accountability and Custody Management | Person, UserAccount, TenantMembership, PersonnelRecord, PermissionCatalogueEntry, PermissionBundle, PermissionBundleVersion, RoleAssignment, PermissionAssignment, AccessGroup, GroupMembership, ExtensionPermissionRegistration, SupportAccessGrant, StockTaskAccessGrant, StockTaskAccessGrantVersion, ApprovalRequest, ApprovalAction, StockTask, StockTaskHold, StockTaskReconciliation, DeviceRegistration, UserSession | AT-IAM-001..008; AT-GNT-001..018 | ODR-002 sections 16.1–16.7 and 16.36–16.38; ODR-004, ODR-005, ODR-018..020 |
| FRS-IAM-019..026 | Canonical permission key registry, Tenant-configurable bundles/assignments, sensitive-field/export/query-surface separation, extension permission registration, platform/support boundary, effective-dated revocation and write-race protection | Permission Catalogue and Assignment Governance; Actor and Access Model; Authentication/Authorization Architecture; Cross-Branch Permission Matrix | PermissionCatalogueEntry, PermissionBundle, PermissionBundleVersion, RoleAssignment, PermissionAssignment, AccessGroup, GroupMembership, ExtensionPermissionRegistration, SupportAccessGrant, AuditEvent | AT-IAM-009..020 | ODR-005 section 16.39; ODR-002 section 16.7; ODR-035 / ARC-PV-001 |
| FRS-IAM-027..036 | Domain permission/approval separation; versioned Tenant approval policy lifecycle; weakening-change independence; immutable combined policy evaluation snapshots; stricter/less-restrictive in-flight re-evaluation; scope conflict handling and proportionate starter profiles | Domain Permission and Approval Control Matrix; Permission Catalogue and Assignment Governance; Authentication/Authorization Architecture; State Transition Matrix | ApprovalPolicyProfile, ApprovalPolicyVersion, ApprovalPolicyRule, ApprovalRequirement, ApprovalLimit, ApprovalPolicyEvaluationSnapshot, ApprovalPolicyMatch, ApprovalRequest, ApprovalAction, PermissionCatalogueEntry, PermissionAssignment | AT-CTRL-001..026 | ODR-005 sections 16.41–16.42; ODR-002 sections 16.3–16.7; ODR-035 / ARC-PV-001 |
| FRS-IAM-037 | Conceptual resource/action/scope/lifecycle-to-permission mapping and pre-implementation API/command/event/job/device/integration key coverage | Domain Permission and Resource Mapping Register; Permission Catalogue and Assignment Governance; FRS/ERS and owning domain specifications | PermissionCatalogueEntry, conceptual module resources, PermissionAssignment, ApprovalPolicyEvaluationSnapshot, AuditEvent | AT-IAM-021..025 | ODR-005 sections 16.39–16.42; ODR-002 section 16.7; ODR-035 / ARC-PV-001 |
| FRS-IAM-038..039 | Sensitive-field classification and exact sensitive-view/download key mapping; parent-resource, query, attachment, report, export and download field/row/purpose enforcement | Domain Permission and Resource Mapping Register section 4; Permission Catalogue and Assignment Governance; privacy/security and owning domain specifications | PermissionCatalogueEntry, sensitive field/data class, PartyContact/Address, CustomerServiceAddress, Evidence, WorkforceDocument, PayrollLine/Payslip, BankAccount, AuditEvent, UserSession, ReportRun, ExportJob, Attachment | AT-IAM-026..028 | ODR-005 sections 16.39 and 16.43; ODR-026; ODR-034 / ARC-PV-001 |
| FRS-IAM-040 | Exception/reversal/recovery command-to-resource and canonical action-key mapping for ERR-001..064; no catch-all recovery authority | Domain Permission and Resource Mapping Register section 5.2.2; Exception/Reversal/Recovery Matrix; owning domain workflows | ExceptionRecord, ApprovalRequest/Action, domain resource namespaces, linked StockMovement, JournalEntry, CustodyEvent, Correction/Reversal, AuditEvent | AT-IAM-029..030 | ODR-005 section 16.45; ODR-002, 012 and all affected domain ODRs |
| FRS-ORG-001..005 | Branches, operating locations, capability configuration, transfers and consolidated reports | Domain Landscape; Module Dependency Map | Tenant, Branch, OperatingLocation, ServiceArea, StockLocation | AT-ORG-001..005 | ODR-002, 003, 012 |
| FRS-CUS-001..002 | Customer master, contacts and addresses without customer login | End-to-End Workflows; Canonical Glossary | Party, PartyRole, CustomerAccount, PartyContact, PartyAddress, CustomerServiceAddress | AT-CUS-001..002 | ODR-002, 004, 026 |
| FRS-SUB-001..006 | Multi-line recurring supply, recurrence, exceptions, effective dates and generated occurrences | Subscription Lifecycle and Recurrence Rules | CustomerSubscription, SubscriptionLine, RecurrenceRule, SubscriptionScheduleVersion, ScheduleException, ScheduleOccurrence | AT-SUB-001..012 | ODR-006..008, 013, 014, 020, 021 |
| FRS-ORD-001; FRS-SAL-001..004 | Ad-hoc orders, POS, receipts, returns, voids, pricing and approvals | End-to-End Workflows; Billing/Receivables/Payments | CustomerOrder, OrderLine, SalesTransaction, SalesLine, POSRegister, POSSession, Tender/PaymentCapture | AT-SAL-001..010 | ODR-002, 008, 013..017 |
| FRS-PRC-001..008 | Supplier/farmer, packaged procurement, raw milk collection, quality and settlements | Procurement Lifecycle; Milk Collection, Quality and Farmer Settlement | Party, SupplierAccount, FarmerProfile, PurchaseOrder, GoodsReceipt, MilkCollectionSession, MilkCollectionEntry, QualityResult, ProcurementRateVersion, FarmerSettlement | AT-PRC-001..014 | ODR-001, 002, 008..012, 016, 017 |
| FRS-INV-001..007; FRS-PRD-001..003 | Product catalogue, UOM, stock ledger, internal transfers, Branch stock-attribution transfer, lot, processing and traceability | Inventory Stock Ledger; Batch/Lot Traceability; Dairy Processing and Packaging; ODR-012 section 16.44 | Product, ProductVariant, SKU, UnitOfMeasure, UnitConversion, StockLocation, StockLot, StockMovement, StockReservation, StockAttributionTransfer, StockAttributionTransferLine, ProductionBatch, TraceabilityLink | AT-INV-001..023 | ODR-008, 011, 012 section 16.44, 016, 017 |
| FRS-UOM-001..012 | Canonical dimensions/UOM, SKU base unit, contextual conversions, pack definitions, source precision, rounding and Tally loss controls | Quantity, UOM Precision and Money Rounding Analysis (DBR-004); Inventory Stock Ledger; Milk Collection/Quality; Processing/Packaging; Billing; Accounting; Tally Field and Voucher Mapping | UOMDimension, UnitOfMeasure, UnitConversionVersion, SKUBaseUOMAssignment, PackDefinitionVersion, PrecisionProfileVersion, RoundingPolicyVersion, MeasurementEvidence, SKU, StockMovement, Invoice, JournalEntry, ExternalMasterMapping | AT-UOM-001..020 | ODR-008 section 16.40; ODR-009..017; ODR-029 |
| FRS-CST-001..010 | Shared-site custody, handover assurance, perishable evidence, shared resource use and incident accountability | Shared-Site Accountability and Custody Management; Inventory Stock Ledger; Batch/Lot Traceability | CustodySubject, CustodyAssignment, CustodyEvent, CustodyHandover, CustodyEvidence, CustodyIncident, SharedResourceUse, StockMovement, StockLocation, Asset | AT-CST-001..012 | ODR-002 section 16.36; ODR-005 permissions |
| FRS-DLV-001..018 | Demand consolidation, Branch-owned routes, delivery plans/runs, personnel and transport assignments, dispatch readiness, custody, perishable handling, offline and corrections | Delivery Lifecycle and Exception Rules; Shared-Site Accountability and Custody Management; End-to-End Business Workflows | DeliveryPlan, RouteTemplate/ManualRoute, DeliveryRun, DeliveryRunPersonnelAssignment, DeliveryRunTransportAssignment, RunExecutionEvent, DeliveryStop, DeliveryAttempt, Fulfilment, FulfilmentLine, DeliveryEvidence, CustodyHandover, StockMovement, Asset | AT-DLV-001..030 | ODR-002 sections 16.36–16.37; ODR-004, 006–008, 019–022, 027–029 |
| FRS-BIL-001..004; FRS-PAY-001..005 | Billing, statements, invoices, UPI/cash/bank capture, allocation and reconciliation | Billing, Receivables, Payments and Reconciliation | BillingRun, BillableItem, Invoice, InvoiceLine, CustomerStatement, Receipt, ReceiptAllocation, Refund, PaymentAccount, BankStatementLine, ReconciliationMatch | AT-BIL-001..014 | ODR-013..016 |
| FRS-ACC-001..007; FRS-EXP-001..002; FRS-BNK-001..003 | Double-entry, posting, expenses, cash/bank, close and reconciliation | Accounting Double-Entry and Financial Control; Expense; Cash/Bank | AccountingEntity, FinancialYear, AccountingPeriod, LedgerAccount, JournalEntry, JournalLine, PostingRuleVersion, ReversalLink, Expense, CashMovement, BankStatementImport | AT-ACC-001..018 | ODR-003, 016, 017, 024, 026 |
| FRS-TLY-001..006 | Desktop sync, mappings, voucher exchange, idempotency and reconciliation | Tally Desktop Synchronization; Tally Field and Voucher Mapping | ExternalAccountingCompany, ExternalMasterMapping, ExternalVoucherMapping, SyncDevice, SyncBatch, SyncBatchItem, SyncConflict | AT-TLY-001..012 | ODR-017, 020, 029, 030, 031 |
| FRS-HR-001..006; FRS-AST-001 | Optional workforce, attendance, payroll and asset maintenance | Payroll and Employee Financial Transactions; Domain Landscape | PersonnelRecord, Engagement, ShiftAssignment, AttendanceRecord, LeaveRequest, PayrollRun, PayrollLine, Payslip, Asset, WorkOrder | AT-HR-001..012 | ODR-018, 026 |
| FRS-TAX-001..006 | GST optionality, verified tax configuration, documents and exports | India Tax Configuration and Statutory Reporting; India regulatory research register | TenantBusinessProfile, TaxClassification, Invoice, InvoiceLine, StatutoryRuleVersion, ComplianceEvidence | AT-TAX-001..010 | ODR-001, 003, 016, 026 |
| FRS-ANA-001..009 | KPI, dashboards, drill-down, actual/budget/forecast, scenarios and asynchronous reports | Budgeting, Financial Analytics and Projections | KPI definition/version, ReportDefinition, ReportRun, AnalyticalFact, Dimension, Budget, BudgetVersion, ForecastRun, ForecastValue, Scenario, ScenarioResult | AT-ANA-001..014 | ODR-022..024, 027, 028 |
| FRS-WFL-001..003; FRS-INT-001..003 | Approvals, notifications, external integrations, retry and audit | Actor and Access Model; domain workflow specifications | ApprovalRequest, ApprovalAction, Notification, DeliveryAttempt, IntegrationConnection, IntegrationJob, AuditEvent | AT-WFL-001..010 | ODR-005, 019..021, 029, 032 |

## 3. Coverage status

- **Requirement-to-domain mapping:** initial mapping complete at capability level.
- **Requirement-to-entity mapping:** logical candidate mapping complete at capability level; ERS detailed entity coverage must be checked requirement-by-requirement during physical design.
- **Requirement-to-test mapping:** stable acceptance identifiers and documented scenario definitions are present in the Quality Strategy and owning control/domain documents; executable automated test harnesses, test data, run evidence and field-pilot evidence remain to be produced after implementation contracts and test environments exist.
- **Requirement-to-API mapping:** intentionally deferred until API contract design.
- **Requirement-to-deployment/component mapping:** intentionally deferred until architecture decisions.
- **Legal/tax requirement evidence:** must be linked to current official sources before any compliance claim.

## 4. Change impact procedure

For each requirement change:
1. Record changed requirement ID, source, status, rationale and effective revision.
2. Identify affected domain documents, ODR items, ERS concepts, acceptance tests, APIs, permissions, reports and integration mappings.
3. Assess tenant/branch isolation, concurrency, idempotency, audit, data migration and backward compatibility.
4. Update the authoritative source document first.
5. Update dependent documents and this matrix.
6. Re-run cross-reference and coverage checks.
7. Record reviewer, date and verification result.

## 5. Exit criteria for detailed RTM

- Every FRS ID has a specific test case with positive, negative, boundary and concurrency coverage where relevant.
- High-risk requirements have security/tenant-isolation tests.
- Financial, stock and delivery requirements have retry/reversal tests.
- Each API operation and background job maps to a requirement and permission.
- Each persistent entity and important constraint maps to one or more requirements.
- No open decision is represented as approved behavior without explicit decision evidence.
