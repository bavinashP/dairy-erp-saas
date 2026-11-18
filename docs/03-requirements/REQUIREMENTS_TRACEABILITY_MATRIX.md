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
| FRS-GEN-001..015 | API-first rules, tenant isolation, audit, idempotency, concurrency, localization, owner-only operations | Project Vision; Actor and Access Model; Domain Dependency Map | Tenant, TenantMembership, AuditEvent, TenantConfiguration, Attachment | AT-GEN-001..015 | ODR-004, 020, 026, 027, 028, 030..032 |
| FRS-PLT-001..007 | Platform administration, tenant lifecycle, plans, entitlements, support access, offboarding | Project Vision; SaaS platform sections in Domain Landscape | PlatformPrincipal, Tenant, TenantSubscription, Plan, FeatureEntitlement, TenantEntitlementOverride, TenantBusinessProfile | AT-PLT-001..007 | ODR-001, 003, 025..028, 031 |
| FRS-IAM-001..008 | Identity, memberships, role/permission scopes, sessions and no customer login | Actor and Access Model | Person, UserAccount, TenantMembership, PersonnelRecord, Role, Permission, RoleAssignment, DeviceRegistration, UserSession | AT-IAM-001..008 | ODR-004, 005, 018, 019, 020 |
| FRS-ORG-001..005 | Branches, operating locations, capability configuration, transfers and consolidated reports | Domain Landscape; Module Dependency Map | Tenant, Branch, OperatingLocation, ServiceArea, StockLocation | AT-ORG-001..005 | ODR-002, 003, 012 |
| FRS-CUS-001..002 | Customer master, contacts and addresses without customer login | End-to-End Workflows; Canonical Glossary | Party, PartyRole, CustomerAccount, PartyContact, PartyAddress, CustomerServiceAddress | AT-CUS-001..002 | ODR-002, 004, 026 |
| FRS-SUB-001..006 | Multi-line recurring supply, recurrence, exceptions, effective dates and generated occurrences | Subscription Lifecycle and Recurrence Rules | CustomerSubscription, SubscriptionLine, RecurrenceRule, SubscriptionScheduleVersion, ScheduleException, ScheduleOccurrence | AT-SUB-001..012 | ODR-006..008, 013, 014, 020, 021 |
| FRS-ORD-001; FRS-SAL-001..004 | Ad-hoc orders, POS, receipts, returns, voids, pricing and approvals | End-to-End Workflows; Billing/Receivables/Payments | CustomerOrder, OrderLine, SalesTransaction, SalesLine, POSRegister, POSSession, Tender/PaymentCapture | AT-SAL-001..010 | ODR-002, 008, 013..017 |
| FRS-PRC-001..008 | Supplier/farmer, packaged procurement, raw milk collection, quality and settlements | Procurement Lifecycle; Milk Collection, Quality and Farmer Settlement | Party, SupplierAccount, FarmerProfile, PurchaseOrder, GoodsReceipt, MilkCollectionSession, MilkCollectionEntry, QualityResult, ProcurementRateVersion, FarmerSettlement | AT-PRC-001..014 | ODR-001, 002, 008..012, 016, 017 |
| FRS-INV-001..006; FRS-PRD-001..003 | Product catalogue, UOM, stock ledger, lot, processing and traceability | Inventory Stock Ledger; Batch/Lot Traceability; Dairy Processing and Packaging | Product, ProductVariant, SKU, UnitOfMeasure, UnitConversion, StockLocation, StockLot, StockMovement, StockReservation, ProductionBatch, TraceabilityLink | AT-INV-001..015 | ODR-008, 011, 012, 016, 017 |
| FRS-CST-001..010 | Shared-site custody, handover assurance, perishable evidence, shared resource use and incident accountability | Shared-Site Accountability and Custody Management; Inventory Stock Ledger; Batch/Lot Traceability | CustodySubject, CustodyAssignment, CustodyEvent, CustodyHandover, CustodyEvidence, CustodyIncident, SharedResourceUse, StockMovement, StockLocation, Asset | AT-CST-001..020 | ODR-002 section 16.36; ODR-005 permissions |
| FRS-DLV-001..007 | Demand consolidation, routes, delivery runs, attempts, offline and corrections | Delivery Lifecycle and Exception Rules | ScheduleOccurrence, CustomerOrder, DeliveryRun, DeliveryStop, DeliveryAttempt, Fulfilment, FulfilmentLine, DeliveryEvidence | AT-DLV-001..015 | ODR-002, 004, 006, 007, 008, 019..022 |
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
- **Requirement-to-test mapping:** acceptance suite identifiers proposed; executable test scenarios and expected results remain to be authored.
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
