# Non-Functional Requirements Specification

**Document ID:** NFR-001  
**Status:** Proposed baseline — product-owner review required  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, FRS-001, ERS-001, Business Rules Catalogue, State Transition Matrix, Exception/Reversal/Recovery Matrix, and Open Decisions Register.  
**Purpose:** Define quality attributes and verifiable non-functional requirements without prematurely selecting implementation technologies or inventing service-level targets.

## 1. Scope and interpretation

This specification applies to the complete SaaS platform, tenant workspaces, APIs, web/mobile clients, background processing, integrations, reporting, operational tooling, and data lifecycle.

Phasing may prioritize delivery but must not silently remove a quality requirement or reduce the approved product scope. The currently constrained development VPS is not a production capacity target. Production architecture must be evaluated against expected workloads, business impact, security and operating requirements.

### 1.1 Normative language
- **MUST:** mandatory requirement.
- **SHOULD:** expected requirement; deviation requires documented rationale.
- **MAY:** optional capability.
- **TBD/Open:** target or policy not approved; do not invent a value.

### 1.2 Target status
Unless explicitly identified as user-confirmed or an existing invariant, numeric targets in this document are intentionally not assigned. ODR-027 and ODR-028 remain authoritative for scale and availability/recovery decisions. A target becomes binding only after evidence, impact assessment and explicit approval.

## 2. Quality requirement catalogue

| ID | Quality area | Requirement | Verification evidence |
|---|---|---|---|
| NFR-SEC-001 | Security | All protected operations MUST authenticate the actor or service identity and authorize the exact action, resource, tenant and permitted scope on the server. | Negative API and service tests |
| NFR-SEC-002 | Tenant isolation | No request, job, export, cache entry, search result, report, attachment or integration callback may disclose or mutate another tenant's data. | Automated cross-tenant isolation suite |
| NFR-SEC-003 | Branch isolation | Branch-scoped access MUST be enforced independently from tenant access; cross-branch visibility and mutation are separately permissioned. | Cross-branch read/write matrix |
| NFR-SEC-004 | Least privilege | Default grants MUST be minimal; role assignment, delegation, platform support access and sensitive-field access MUST be bounded and auditable. | Permission matrix and privilege-escalation tests |
| NFR-SEC-005 | Secret handling | Passwords, tokens, OTPs, payment PINs, private keys and integration secrets MUST not be exposed in logs, URLs, error messages or ordinary audit payloads. | Secret scanning and log inspection |
| NFR-SEC-006 | Session security | Sessions/devices MUST support revocation, expiry and documented re-authentication behavior. | Session lifecycle and revocation tests |
| NFR-SEC-007 | Data protection | Sensitive data MUST be protected in transit and at rest using controls selected during security/technology evaluation. | Configuration review and security tests |
| NFR-SEC-008 | Secure operations | Security-sensitive configuration, support access, exports, permission changes and recovery actions MUST be audited. | Audit completeness tests |
| NFR-SEC-009 | Abuse resistance | Authentication, recovery, invitation, webhook and expensive API operations MUST have abuse controls, bounded rate/concurrency and actionable anomaly monitoring. | Abuse simulation and rate-limit tests |
| NFR-SEC-010 | Secure input handling | Untrusted input MUST be schema-validated and protected against injection, unsafe deserialization, mass assignment and unsafe output rendering. | Security test suite and code review |
| NFR-SEC-011 | File security | Uploaded files MUST be validated by content, bounded by size/resource limits, quarantined/scanned as appropriate and served without unsafe active-content execution. | Malicious-file and safe-download tests |
| NFR-SEC-012 | Network request safety | Server-side URL fetching and outbound integration requests MUST prevent SSRF and unauthorized access to private, loopback, link-local and metadata destinations. | SSRF and egress-policy tests |
| NFR-SEC-013 | Integration authenticity | Webhooks/callbacks MUST verify authenticity and integrity, enforce replay protection and bind to a tenant-specific connection. | Signature, replay and tenant-binding tests |
| NFR-SEC-014 | Secret lifecycle | Production secrets MUST be centrally protected, least-privileged, rotatable and revocable; secret values MUST not appear in source, logs or client artifacts. | Secret scanning and rotation drill |
| NFR-SEC-015 | Software supply chain | Dependencies, build pipelines and release credentials MUST be inventoried, scanned and access-controlled; critical vulnerabilities require defined remediation. | Dependency scan and pipeline review |
| NFR-SEC-016 | Incident response | Security incidents MUST have documented detection, containment, evidence preservation, recovery and post-incident review procedures. | Incident tabletop and runbook exercise |
| NFR-SEC-017 | Audit protection | Security/audit evidence MUST be access-restricted, tamper-evident where feasible, retained under policy and redacted of secrets. | Audit access and integrity tests |
| NFR-SEC-018 | Privileged access | Platform and support access to tenant data MUST require explicit scope, purpose, expiry/revocation and attributable audit; no implicit global bypass is permitted. | Support-access abuse and expiry tests |
| NFR-REL-001 | Reliability | A committed business operation MUST have a durable, unambiguous outcome; clients must not be told it failed merely because a later notification failed. | Fault-injection and workflow tests |
| NFR-REL-002 | Idempotency | Retryable material commands MUST prevent duplicate business effects using stable operation identity and persisted outcomes. | Replay/retry tests |
| NFR-REL-003 | Recovery | Recoverable failures MUST expose status, error classification, retryability and a safe recovery path. | Exception matrix execution |
| NFR-REL-004 | External dependencies | Provider outages MUST be isolated from unrelated core business transactions; unknown external outcomes require reconciliation before resend. | Provider outage and partial-success tests |
| NFR-REL-005 | State integrity | Invalid state transitions and unauthorized reversals MUST be rejected; posted stock and financial effects require linked compensating actions. | State-transition and reversal tests |
| NFR-DATA-001 | Consistency | Tenant ownership, branch attribution, unique business effects and critical relationships MUST be enforced by application and persistence controls appropriate to the selected technology. | Constraint and integration tests |
| NFR-DATA-002 | Concurrency | Simultaneous requests/jobs MUST NOT cause lost updates, duplicate schedules, over-allocation, double posting or silent stock/balance corruption. | Deterministic concurrency tests |
| NFR-DATA-003 | Auditability | Material actions MUST preserve actor/service, tenant/scope, timestamp, source, reason and relevant before/after or event references. | Audit trail verification |
| NFR-DATA-004 | Historical integrity | Finalized transactions MUST retain the versions/snapshots required to explain historical quantities, rates, prices, tax, policy and accounting outcomes. | Historical replay and master-data change tests |
| NFR-DATA-005 | Precision | Quantity, currency, tax and measurement precision/rounding MUST be explicit per domain and consistent across clients and integrations. | Boundary and rounding test vectors; ODR-008/016/017 |
| NFR-PERF-001 | Performance | User-facing operations MUST have defined response-time budgets by operation class and workload; budgets require workload evidence and approval. | Load tests and percentile reports |
| NFR-PERF-002 | Throughput | API, schedule generation, billing, delivery planning, imports and reporting MUST be tested at approved expected and peak workload profiles. | Reproducible load test |
| NFR-PERF-003 | Resource efficiency | Expensive reports, imports, exports and bulk integrations SHOULD use bounded asynchronous processing where synchronous completion is not appropriate. | Job architecture and load tests |
| NFR-PERF-004 | Scalability | Capacity MUST be evaluated across tenants, branches, users, records, API traffic, concurrent transactions, background jobs and reporting workloads. | Capacity model and scale test |
| NFR-AVAIL-001 | Availability | Availability objectives MUST be defined by service and business impact before production service commitments are made. | Approved SLO and monitoring evidence |
| NFR-AVAIL-002 | Degraded operation | Failure of a non-critical integration or analytics workload MUST NOT unnecessarily prevent core sales, delivery, procurement or accounting operations. | Dependency failure tests |
| NFR-AVAIL-003 | Maintenance | Maintenance, migration and deployment procedures MUST define user impact, compatibility, rollback and recovery behavior. | Release rehearsal |
| NFR-OBS-001 | Observability | Services and jobs MUST emit structured, correlated logs, metrics and traces sufficient to diagnose request and workflow outcomes. | Trace/correlation walkthrough |
| NFR-OBS-002 | Alerting | Alerts MUST be actionable, severity-classified and linked to a runbook or owner. | Alert simulation and runbook review |
| NFR-OBS-003 | Business monitoring | Operational queues MUST expose aged/failed schedules, deliveries, reconciliations, jobs and external sync items with tenant-safe access. | Operations dashboard acceptance |
| NFR-MNT-001 | Maintainability | Business rules MUST be separated from client presentation and external-provider implementations. | Architecture and change-impact review |
| NFR-MNT-002 | Modularity | Domain boundaries and contracts MUST permit controlled evolution without duplicating authoritative business logic. | Dependency and contract review |
| NFR-MNT-003 | Change safety | Material changes MUST include impact analysis across requirements, workflows, data, permissions, APIs, tests, reports and integrations. | Change record review |
| NFR-COMP-001 | API compatibility | API versions and deprecation policy MUST protect supported web/mobile clients and external integrations from unannounced breaking changes. | Contract and compatibility tests |
| NFR-COMP-002 | Client parity | Web and mobile clients MUST receive consistent business validation and authorization outcomes for equivalent operations. | Cross-client contract tests |
| NFR-COMP-003 | Integration compatibility | Tally and other adapters MUST version their contracts, mappings and acknowledgements; external schema changes must not silently alter internal records. | Adapter contract tests |
| NFR-LOC-001 | Localization | Unicode names/data and Marathi, Hindi and additional Indian language resources MUST round-trip without corruption. | Localization and Unicode tests |
| NFR-LOC-002 | Regional conventions | INR, Indian number/date presentation and IST business-time defaults MUST be consistently applied; stored instants and business dates remain distinguishable. | Timezone and formatting tests |
| NFR-ACC-001 | Accessibility | Critical workflows SHOULD be usable with keyboard navigation, clear labels, readable contrast and assistive technologies. Detailed conformance target remains open. | Accessibility audit |
| NFR-UX-001 | Usability | Owner-operated businesses MUST be able to perform essential workflows without creating employee accounts or navigating unnecessary enterprise-only steps. | Owner-only usability scenarios |
| NFR-UX-002 | Offline operation | Offline-capable workflows MUST explicitly define local data, permitted actions, conflict behavior, expiry and reconciliation; scope remains subject to ODR-020. | Offline conflict and replay tests |
| NFR-BACK-001 | Backup | Backup scope, frequency, encryption, isolation, retention and integrity checks MUST be documented and tested. | Backup inventory and restore test |
| NFR-BACK-002 | Recovery | Recovery objectives (RPO/RTO), disaster scenarios, failover and restore priority MUST be approved based on business impact; see ODR-028. | Timed recovery exercise |
| NFR-BACK-003 | Restore integrity | Restores MUST verify database, file/object data, secrets/configuration and external integration consistency as applicable. | Full restore rehearsal |
| NFR-STO-001 | Object storage | Business documents and generated artifacts MUST use a provider-neutral storage abstraction; provider-specific behavior must be isolated in tested adapters. | Adapter contract and migration tests |
| NFR-STO-002 | Object integrity | Stored objects MUST have stable metadata, checksum verification, tenant ownership and auditable lifecycle state. | Upload/download/reconciliation tests |
| NFR-STO-003 | Tenant isolation | Object access, signed URLs, listing, copy, archive, restore and deletion MUST enforce tenant and applicable branch authorization server-side. | Cross-tenant object-access tests |
| NFR-STO-004 | Backup isolation | Backup credentials and deletion authority MUST be separated from ordinary application credentials; at least one recoverable copy MUST be outside the VPS failure/administrative boundary. | Credential review and isolated restore exercise |
| NFR-STO-005 | Backup encryption | Sensitive backup sets MUST be encrypted before external storage where independent key control is required; key recovery MUST be tested separately from ciphertext storage. | Backup encryption and key-recovery drill |
| NFR-STO-006 | Retention | Object lifecycle automation MUST follow an approved data-class retention policy and MUST respect legal holds and immutable retention controls. | Retention simulation and deletion audit |
| NFR-STO-007 | Quota resilience | Provider free-tier quotas MUST NOT be treated as guaranteed capacity; quota exhaustion MUST be monitored and MUST NOT silently discard critical business records or backups. | Quota exhaustion and paid-tier transition test |
| NFR-STO-008 | Restore verification | Backup success MUST be distinguished from verified recoverability; scheduled restore exercises MUST verify database-to-object references and checksums. | Documented full restore rehearsal |
| NFR-PRIV-001 | Data lifecycle | Collection, access, export, archival, retention, legal hold and deletion MUST follow approved legal/contractual policies; see ODR-026. | Data inventory and lifecycle tests |
| NFR-PRIV-002 | Data minimization | Only data needed for documented business, security, legal or operational purposes SHOULD be collected and retained. | Data inventory review |
| NFR-TEST-001 | Testability | Critical business rules MUST be testable independently of UI and external providers. | Automated domain/service tests |
| NFR-TEST-002 | Risk coverage | Tenant isolation, authorization, money, stock, delivery, concurrency, idempotency, reversals and integrations MUST receive risk-based automated tests. | Coverage and test evidence |
| NFR-TEST-003 | Reproducibility | Performance, concurrency, restore and integration tests MUST record environment, dataset profile, versions and execution results. | Reproducible test report |

## 3. Workload and capacity model

Capacity targets must be based on explicit workload scenarios, not assumptions about the development VPS. The architecture evaluation must model at least:

- Tenant count and tenant-size distribution.
- Branches and operating locations per tenant.
- Users, active sessions, registered devices and role assignments.
- Customers, subscriptions, recurring schedule occurrences and daily delivery stops.
- POS transactions, procurement entries, stock movements and financial postings.
- Peak concurrent users and competing writes to the same business resources.
- Background job volume, retry backlog and provider rate limits.
- Report/export volume, date ranges, drill-down concurrency and forecast workloads.
- Attachment volume, audit growth, retention and archival.
- Tally desktop-agent count, sync batch sizes and intermittent connectivity.

Low, expected and high-growth scenarios, workload assumptions and headroom policy remain open under ODR-027. Do not treat these illustrative dimensions as approved numerical forecasts.

## 4. Measurement and service objectives

Before production readiness, define and approve:
- API latency by operation class, including percentile and timeout behavior.
- Throughput and concurrency for interactive and batch workloads.
- Availability objective and measurement window for each critical service.
- Error budget and maintenance/degradation policy where an SLO is adopted.
- Background queue delay, processing completion and retry-age objectives.
- Data freshness targets for operational dashboards and analytical reports.
- RPO, RTO, backup frequency, retention and restore verification.
- Offline duration and conflict-resolution guarantees.
- Security incident detection and response objectives.

No numeric value is approved by this document. The owner, evidence, business impact, cost and acceptance test for each target must be recorded before approval.

## 5. Required test environments and evidence

The quality strategy must distinguish local development, shared test/staging and production. Production-like tests must use controlled non-production data and secrets. The constrained development VPS may require smaller datasets or fewer concurrent workers, but such limits must not be mistaken for production capacity requirements.

For each quality requirement, retain:
- Requirement ID and applicable component/workflow.
- Test method, tool/version and environment.
- Dataset/workload profile and relevant configuration.
- Expected result and pass/fail threshold.
- Actual result, timestamp and evidence location.
- Known limitation, exception and remediation owner.

## 6. Open decisions and dependencies

| Decision | NFR impact |
|---|---|
| ODR-004/005 | Membership, role/scope model, delegation and authorization complexity |
| ODR-019/020 | Client strategy and offline guarantees |
| ODR-021 | Notification reliability, consent, provider fallback and retry policy |
| ODR-022/023/024 | Analytics freshness, forecast quality and workload |
| ODR-026 | Retention, archival, export, deletion and legal holds |
| ODR-027 | Capacity, performance, throughput and scale targets |
| ODR-028 | Availability, backup, RPO/RTO and disaster recovery |
| ODR-029/030 | Integration and technology choices |
| ODR-031 | Production topology, isolation tiers and resilience |
| ODR-032 | Language coverage and localization workflow |

## 7. Approval and exit criteria

This document is a proposed baseline, not an approved SLA or production service commitment. It is ready for detailed review when every requirement has an owner, verification method and traceability mapping. Production readiness additionally requires approved measurable targets for the applicable service tiers, passing evidence, operational runbooks and documented exceptions.
