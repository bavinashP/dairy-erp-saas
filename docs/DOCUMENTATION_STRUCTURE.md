# Documentation Structure and Governance

**Status:** Maintained documentation index and governance baseline  
**Purpose:** Define the authoritative documentation system, evidence standards, and review gates.

## 1. Principles
1. Maintain one authoritative location for each requirement or decision.
2. Link to authoritative content rather than duplicating it.
3. Distinguish approved requirements, proposals, assumptions, constraints, risks, and open decisions.
4. Do not infer legal, business, security, or architecture decisions from incomplete information.
5. Preserve the comprehensive product scope across delivery phases.
6. Record rationale, alternatives, consequences, dependencies, and verification for material decisions.
7. Do not select technology merely because it is familiar or convenient for the development VPS.
8. Treat legal and regulatory statements as evidence-backed and date-sensitive.
9. Separate business rules from presentation and client-specific behavior.
10. Review cross-document consistency after foundational changes.
11. Classify every material rule as fixed, configurable, customizable, extensible or externally dependent before exposing it as a product setting.
12. Prefer configuration and governed extension contracts over customer-specific code forks.
13. Treat tenant plan entitlement, capability activation, configuration, authorization and extension grants as separate controls.

## 2. Status vocabulary
| Status | Meaning |
|---|---|
| Approved | Explicitly accepted requirement or decision |
| Proposed | Recommendation awaiting approval |
| Open | Unresolved question or decision |
| Assumption | Temporary premise requiring validation |
| Constraint | Explicit project boundary |
| Deferred | Intentionally scheduled later; not removed from scope |
| Superseded | Replaced by a newer decision; retain traceability |
| Verified | Checked against evidence or an agreed acceptance method |

For decision-specific classifications, use the authoritative classification protocol in [Open Decisions Register](01-product/OPEN_DECISIONS_REGISTER.md). Do not use a bare generic `Open` status where the decision classification, missing evidence/approval, owner and trigger can be stated explicitly.

## 3. Evidence labels
| Label | Meaning |
|---|---|
| User-confirmed | Explicitly stated by the product owner |
| Official-source | Supported by an authoritative government, standards, or vendor source |
| Industry-evidence | Supported by credible industry research or practice |
| Analysis | Reasoned from established inputs |
| Unverified | Requires investigation |

Claims about law, tax, compliance, or provider capability must record source, applicable date, jurisdiction, and verification status.

## 4. Documentation catalogue

### 00 — Governance
- [Continuation State Audit — 2026-11-17](CONTINUATION_STATE_AUDIT_2026-11-17.md)
Project governance, glossary, requirements traceability, change control and risk/assumption management are governed through the ODR, RTM, ADR and risk evidence sections; dedicated register refinements remain in progress.

### 01 — Product
- [Project Vision and Scope](01-product/PROJECT_VISION_AND_SCOPE.md)
- [Open Decisions Register](01-product/OPEN_DECISIONS_REGISTER.md)
- Goals and success metrics, personas, business models, roadmap (planned).

### 02 — Business domain
- [Domain Landscape and Capability Model](02-domain/DOMAIN_LANDSCAPE_AND_CAPABILITY_MODEL.md)
- [End-to-End Business Workflows](02-domain/END_TO_END_BUSINESS_WORKFLOWS.md)
- [Actor and Access Model](02-domain/ACTOR_AND_ACCESS_MODEL.md)
- [Core Domain Entity Catalogue](02-domain/CORE_DOMAIN_ENTITY_CATALOGUE.md)
- [Canonical Business Glossary](02-domain/CANONICAL_BUSINESS_GLOSSARY.md)
- [Module Dependency Map](02-domain/MODULE_DEPENDENCY_MAP.md)
- [Shared-Site Accountability and Custody Management](02-domain/SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md)
- [Subscription Lifecycle and Recurrence Rules](02-domain/SUBSCRIPTION_LIFECYCLE_AND_RECURRENCE_RULES.md)
- [Delivery Lifecycle and Exception Rules](02-domain/DELIVERY_LIFECYCLE_AND_EXCEPTION_RULES.md)
- [Procurement Lifecycle and Controls](02-domain/PROCUREMENT_LIFECYCLE_AND_CONTROLS.md)
- [Milk Collection, Quality and Farmer Settlement](02-domain/MILK_COLLECTION_QUALITY_AND_FARMER_SETTLEMENT.md)
- [Inventory, Stock Ledger and Reconciliation](02-domain/INVENTORY_STOCK_LEDGER_AND_RECONCILIATION.md)
- [Batch, Lot, Expiry and Traceability](02-domain/BATCH_LOT_EXPIRY_AND_TRACEABILITY.md)
- [Dairy Processing, Packaging and Yield Management](02-domain/DAIRY_PROCESSING_PACKAGING_AND_YIELD.md)
- [Billing, Receivables, Payments and Reconciliation](02-domain/BILLING_RECEIVABLES_PAYMENTS_AND_RECONCILIATION.md)
- [Accounting, Double-Entry Bookkeeping and Financial Controls](02-domain/ACCOUNTING_DOUBLE_ENTRY_AND_FINANCIAL_CONTROL.md)
- [Tally Desktop Synchronization and Data Exchange Architecture](02-domain/TALLY_DESKTOP_SYNC_AND_DATA_EXCHANGE_ARCHITECTURE.md)
- [Expense Management and Petty Cash](02-domain/EXPENSE_MANAGEMENT_AND_PETTY_CASH.md)
- [Cash, Bank Accounts and Financial Reconciliation](02-domain/CASH_BANK_MANAGEMENT_AND_RECONCILIATION.md)
- [Payroll and Employee Financial Transactions](02-domain/PAYROLL_AND_EMPLOYEE_FINANCIAL_TRANSACTIONS.md)
- [India Tax Configuration and Statutory Reporting](02-domain/INDIA_TAX_CONFIGURATION_AND_STATUTORY_REPORTING.md)
- [Budgeting, Financial Analytics and Projections](02-domain/BUDGETING_FINANCIAL_ANALYTICS_AND_PROJECTIONS.md)
- [Tally Field and Voucher Mapping Specification](02-domain/TALLY_FIELD_AND_VOUCHER_MAPPING_SPECIFICATION.md)

### 03 — Requirements
- [Functional Requirements Specification](03-requirements/FUNCTIONAL_REQUIREMENTS_SPECIFICATION.md)
- [Entity Relationship Specification](03-requirements/ENTITY_RELATIONSHIP_SPECIFICATION.md)
- [ERS Readiness and Database Design Gap Assessment](03-requirements/ERS_READINESS_AND_DATABASE_DESIGN_GAP_ASSESSMENT.md)
- [Tenant, Branch, Legal Entity and Accounting Boundary Analysis](03-requirements/TENANT_BRANCH_LEGAL_ENTITY_AND_ACCOUNTING_BOUNDARY_ANALYSIS.md)
- [Party, Customer, Supplier and Farmer Domain Analysis](03-requirements/PARTY_CUSTOMER_SUPPLIER_AND_FARMER_DOMAIN_ANALYSIS.md)
- [Quantity, UOM Precision and Money Rounding Analysis](03-requirements/QUANTITY_UOM_PRECISION_AND_MONEY_ROUNDING_ANALYSIS.md)
- [Critical Workflow Transaction and Posting Boundary Analysis](03-requirements/CRITICAL_WORKFLOW_TRANSACTION_AND_POSTING_BOUNDARY_ANALYSIS.md)
- [Requirements Traceability Matrix](03-requirements/REQUIREMENTS_TRACEABILITY_MATRIX.md)
- [Business Rules Catalogue](03-requirements/BUSINESS_RULES_CATALOGUE.md)
- [State Transition Matrix](03-requirements/STATE_TRANSITION_MATRIX.md)
- [Exception, Reversal and Recovery Matrix](03-requirements/EXCEPTION_REVERSAL_AND_RECOVERY_MATRIX.md)
- [Non-Functional Requirements Specification](03-requirements/NON_FUNCTIONAL_REQUIREMENTS_SPECIFICATION.md)
- Detailed executable test cases and API traceability remain planned.

### 04 — Architecture
- [SaaS Multi-Tenancy and Data Isolation Architecture](04-architecture/SAAS_MULTI_TENANCY_AND_DATA_ISOLATION_ARCHITECTURE.md)
- [System Architecture Overview and Integration Map](04-architecture/SYSTEM_ARCHITECTURE_OVERVIEW_AND_INTEGRATION_MAP.md)
- [Product Configurability, Customization, Extensibility and Scale Policy](04-architecture/PRODUCT_CONFIGURABILITY_CUSTOMIZATION_EXTENSIBILITY_AND_SCALE_POLICY.md)
- [Authentication, Authorization and Access Control Architecture](04-architecture/AUTHENTICATION_AUTHORIZATION_AND_ACCESS_CONTROL_ARCHITECTURE.md)
- [Permission Catalogue and Assignment Governance](04-architecture/PERMISSION_CATALOGUE_AND_ASSIGNMENT_GOVERNANCE.md)
- [Domain Permission and Approval Control Matrix](04-architecture/DOMAIN_PERMISSION_AND_APPROVAL_CONTROL_MATRIX.md)
- [Domain Permission and Resource Mapping Register](04-architecture/DOMAIN_PERMISSION_AND_RESOURCE_MAPPING_REGISTER.md)
- [Cross-Branch Permission Matrix](04-architecture/CROSS_BRANCH_PERMISSION_MATRIX.md)
- [API Architecture, Versioning and Client Compatibility](04-architecture/API_ARCHITECTURE_VERSIONING_AND_CLIENT_COMPATIBILITY.md)
- [Mobile Client and Offline Operation Architecture](04-architecture/MOBILE_CLIENT_AND_OFFLINE_OPERATION_ARCHITECTURE.md)
- [Concurrency, Transaction Integrity and Idempotency](04-architecture/CONCURRENCY_TRANSACTION_INTEGRITY_AND_IDEMPOTENCY.md)
- [Event-Driven Processing, Background Jobs and Transactional Outbox](04-architecture/EVENT_DRIVEN_PROCESSING_BACKGROUND_JOBS_AND_OUTBOX.md)
- [Object Storage, File Archival and Backup Architecture](04-architecture/OBJECT_STORAGE_FILE_ARCHIVAL_AND_BACKUP_ARCHITECTURE.md)
- [Backup, Restore and Disaster Recovery Architecture](04-architecture/BACKUP_RESTORE_AND_DISASTER_RECOVERY_ARCHITECTURE.md)
- [Scalability, Performance and Capacity Architecture](04-architecture/SCALABILITY_PERFORMANCE_AND_CAPACITY_ARCHITECTURE.md)
- [Analytics, Business Intelligence and Forecasting Architecture](04-architecture/ANALYTICS_BI_AND_FORECASTING_ARCHITECTURE.md)
- [Production VPS Deployment and Infrastructure Architecture](04-architecture/PRODUCTION_VPS_DEPLOYMENT_AND_INFRASTRUCTURE_ARCHITECTURE.md)
- [Security Threat Model and Security Architecture](04-architecture/SECURITY_THREAT_MODEL_AND_SECURITY_ARCHITECTURE.md)
- [Audit Logging and Observability Architecture](04-architecture/AUDIT_LOGGING_AND_OBSERVABILITY_ARCHITECTURE.md)
- Physical database architecture and ERD remain deferred until ERS readiness, ODR-003/008/017 and technology PoC dependencies are satisfied; no DDL/schema is authorized in the current phase.

### 05 — India requirements
- [India Dairy Regulatory Research Register](05-india/INDIA_DAIRY_REGULATORY_RESEARCH_REGISTER.md)
- GST applicability and invoicing; payments and reconciliation; address/PIN/mobile handling; privacy and retention (detailed research and requirements planned).

### 06 — Security and operations
- [Security Threat Model and Security Architecture](04-architecture/SECURITY_THREAT_MODEL_AND_SECURITY_ARCHITECTURE.md)
- [Audit Logging and Observability Architecture](04-architecture/AUDIT_LOGGING_AND_OBSERVABILITY_ARCHITECTURE.md)
- [Production Operations, Incident Response and Support Runbook](06-security-operations/PRODUCTION_OPERATIONS_INCIDENT_RESPONSE_AND_SUPPORT_RUNBOOK.md)
Tenant-isolation verification, named responders, support grants and production-specific provider details require environment-specific evidence and human assignment; backup/restore/DR baseline is specified in ARC-DR-001.

### 07 — Quality engineering
- [Quality Engineering and Acceptance Test Strategy](07-quality/QUALITY_ENGINEERING_AND_ACCEPTANCE_TEST_STRATEGY.md)
Detailed executable test cases, test data fixtures and automated coverage remain to be produced from the FRS/RTM during the later implementation phase.

### 08 — Technology evaluation
- [Technology Evaluation and Provisional Technology Baseline](08-technology/TECHNOLOGY_EVALUATION_AND_PROVISIONAL_BASELINE.md)
Proof-of-concept findings, final ADRs and approved technology baseline remain gated by reproducible evidence and explicit review.

### 09 — Delivery and maintenance
- [Development, Release and Change Governance](09-delivery/DEVELOPMENT_RELEASE_AND_CHANGE_GOVERNANCE.md)
Detailed environment-specific setup, branch/CI configuration, deployment scripts, provider operations and customer support assignments are finalized after ODR-030/031 and named operational owners.

## 5. Review gates

- **Variability gate:** every proposed setting, customization or extension has an owner, declared scope, validation, permissions, audit, compatibility, migration/rollback and support lifecycle.
- **Gate A — Foundation:** vision, scope, terminology, actors, business models, constraints, open decisions.
- **Gate B — Domain completeness:** workflows, exceptions, lifecycle states, hand-offs, dependencies.
- **Gate C — Requirements completeness:** testability, traceability, non-duplication, status.
- **Gate D — Architecture readiness:** tenancy, authorization, data boundaries, integrity, concurrency, integrations, analytics, operations.
- **Gate E — Technology selection:** requirements-led comparison and proof of concept; no premature selection.
- **Gate F — Development readiness:** approved architecture, acceptance criteria, tests, security, deployment, and decision-impact review.

## 6. Change control
For each material change: identify source and evidence; list affected documents/modules/APIs/workflows/data/tests; assess compatibility, migration, security, operations and legal impact; update the authoritative document; cross-check consistency; record verification and follow-ups.

## 7. Execution order
1. Product vision and scope.
2. Business domain and operating-model analysis.
3. Actors, permission-first authorization and scoped access (roles are optional bundles, not authority classes).
4. End-to-end workflows and exceptions.
5. Module catalogue and dependency map.
6. Functional and non-functional requirements.
7. India-specific business and regulatory requirements.
8. SaaS tenancy, branch, subscription and entitlement architecture.
9. Security, data integrity, concurrency, audit and resilience.
10. Reporting, BI, forecasting, budgeting and projections.
11. Product variability, configuration governance, customization and extension contracts.
12. Architecture alternatives and technology evaluation.
13. Final architecture decisions and development-readiness review.

This is a documentation sequence, not a reduction of product scope.
