# Audit Logging and Observability Architecture

**Document ID:** ARC-OBS-001  
**Status:** Recommended audit/observability baseline selected; retention periods, SLOs and technology tooling remain evidence-gated  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, ARC-TEN-001, ARC-IAM-001, ARC-EVT-001, ARC-CONC-001, ARC-SEC-001, ARC-STO-001, NFR-001 and ODR-021/026/027/028/030/031/034.  
**Purpose:** Define how the platform records accountable business/security actions and diagnoses system behavior while preserving tenant isolation, privacy, data integrity and operational recoverability.

## 1. Scope and core distinction

The platform requires several related but distinct records. They MUST NOT be collapsed into one generic log:

1. **Business history:** Domain facts and state transitions needed to explain a dairy business record over time (for example invoice issued, payment allocated, stock adjusted, delivery fulfilled, journal reversed).
2. **Security audit trail:** Who attempted or performed a security-sensitive action, against which boundary/resource, with what authorization context and outcome.
3. **Operational logs:** Diagnostic events emitted by APIs, workers, integrations and infrastructure.
4. **Metrics:** Numeric measurements of service health, workload, latency, throughput, saturation and business-process backlog.
5. **Distributed traces:** Correlated spans showing the path of a request/job across service and provider boundaries.
6. **External-provider evidence:** Provider request/event identifiers, acknowledgements, reconciliation states and relevant timestamps.
7. **Audit exports/evidence packages:** Controlled, integrity-checked extracts for investigation, dispute, audit or authorized customer access.

Business history is not a substitute for security audit. Operational logs are not the authoritative record of a financial transaction. A trace is not proof that a business operation committed. The relational business store remains authoritative for domain state; security audit and operational telemetry support accountability and diagnosis.

## 2. Design principles

- Record material business/security actions with an attributable actor or service identity, tenant/security boundary, scope, target, timestamp, outcome and correlation reference.
- Persist audit evidence reliably; do not rely on best-effort application log output for required audit events.
- Where audit evidence is required for a committed business action, persist the business change and its audit/outbox intent in the same transaction boundary where feasible.
- Separate business history, security audit, operational diagnostics, metrics and traces by purpose, access policy and retention.
- Enforce tenant and branch isolation in audit queries, exports, dashboards and support tooling.
- Platform-wide security operations are separate from tenant-visible audit history.
- Minimize sensitive payloads. Prefer stable references and safe change summaries over copying entire business records.
- Never log passwords, OTPs, access/refresh tokens, payment PINs, private keys, full credentials or secret values.
- Logs and telemetry must not become a secondary uncontrolled personal-data repository.
- Use structured, versioned event schemas and consistent correlation/causation identifiers.
- Alerts must be actionable, severity-classified, owned and linked to a runbook.
- Monitoring failure must not silently corrupt or block core business transactions; required audit durability must be handled separately from optional telemetry.
- Retention and deletion must follow approved data-class policies, including legal holds and backup implications.

## 3. Event taxonomy and ownership

| Record type | Primary purpose | Authority/owner | Example |
|---|---|---|---|
| Business history | Explain domain state and historical decisions | Domain module and its authoritative store | Invoice issued, receipt allocated, stock transfer posted |
| Security audit | Establish accountability for security/material actions | Security/audit subsystem with domain event sources | Role changed, support grant used, export downloaded |
| Operational log | Diagnose code path and failures | Producing service/job | API validation failure, worker retry |
| Metric | Detect trends, saturation and SLO breaches | Telemetry pipeline | Request latency, queue age, DB pool saturation |
| Trace | Follow one request/workflow across boundaries | Trace instrumentation | API → outbox → worker → provider |
| Provider evidence | Reconcile external outcomes | Integration adapter | Webhook event ID, bank/Tally acknowledgement |
| Audit package | Provide authorized, verifiable evidence | Audit/export service | Tenant-period audit export |

Each record type must have a documented schema, source, access policy, retention class and integrity expectation. A business event may cause a security audit event, but neither should be assumed to be interchangeable.

## 4. Audit event envelope

A security audit event SHOULD contain:
- Globally unique event ID and schema version.
- Event type/category and severity/risk classification.
- Event occurrence timestamp and recorded timestamp; use a consistent UTC instant representation while preserving relevant business date/timezone separately.
- Actor type (human, service, platform operator, integration) and stable actor reference.
- Security boundary (platform or tenant), tenant ID when applicable, and branch/location scope when applicable.
- Action/permission evaluated and target resource type plus stable opaque reference.
- Outcome (success, denied, failed, pending, overridden) and safe reason code.
- Authorization context reference or policy version, not a full secret-bearing token/claim set.
- Source channel/client class where useful (web, mobile, POS, API, job, integration, support).
- Request, correlation, causation and idempotency references where applicable.
- Reason/comment/ticket reference for privileged or exceptional actions, with length and content controls.
- Before/after summary or changed-field names only where necessary and safe.
- Integrity metadata or sequence/version reference where supported.

Do not store full request/response bodies by default. Any exceptional payload capture must be explicitly justified, minimized, access-restricted and retention-controlled.

## 5. Events that require audit evidence

### 5.1 Identity and access
- Authentication success/failure patterns, account recovery, MFA/factor changes and credential reset.
- Session/device registration, refresh anomalies, revocation and privileged re-authentication.
- Tenant membership, branch assignment, role, permission, delegation and approval-limit changes.
- Support grant request, approval, activation, use, expiry, revocation and break-glass review.
- Emergency Override incident/request, activation mode (ordinary approval or restricted self-activation), Emergency Authority permission evidence, affected stock-owning Branch/location/stock/batch/task, exact scope/version, reason/evidence, activation/expiry/extension/revocation, every attempted and completed action, quantities/custody changes, retrospective reviewer/findings and reconciliation/corrective actions.
- Approval delegation creation and authorization evidence, delegator/delegate identities, Tenant/Branch/resource/action scope, limits, effective period, reason, revocation, automatic invalidation and expiry; each delegated decision must record the actual delegate, delegator, delegation ID/version, exact payload/version/hash, policy version, limits, outcome, timestamp and reason.
- Stock-task grant expiry notification scheduling, event type (activation, 24-hour reminder, 1-hour reminder, expiry, revocation, automatic invalidation or approved version change), grant/version and exact expiry, recipient identity and current permission basis, channel, delivery attempts/outcome, suppression reason and held-task reference. Notification delivery failure must never alter authorization state.
- Authorization denials for sensitive resources and repeated suspicious access attempts.

### 5.2 Business and financial controls
- Invoice issue/correction/cancellation, credit/debit note, refund and payment allocation/reversal.
- Receipt creation, manual payment recording, payment verification and reconciliation override.
- Journal creation/post/reversal, accounting-period close/reopen and Tally mapping/sync overrides.
- Procurement rate overrides, farmer settlement, stock adjustment/write-off/transfer discrepancy.
- Product price/discount/credit-limit changes and high-risk approval decisions.
- Payroll finalization and sensitive employee/bank-detail access where the module is enabled.

### 5.3 Data, export and administration
- Sensitive record views where risk/policy requires access logging.
- Bulk export, report generation/download, customer/tenant data export and deletion.
- Retention-policy, legal-hold, archive, restore and deletion actions.
- Integration connection/credential changes, webhook configuration and API key lifecycle.
- Tenant activation/suspension, plan/entitlement changes and privileged platform configuration.
- Audit log access, audit export, retention configuration and any attempted tampering.

Not every ordinary read needs a durable audit event. Define read-audit requirements by data sensitivity, abuse risk and legal/contractual need to avoid excessive collection.

### 5.4 Custody and shared-resource accountability

Where custody/resource tracking is enabled, durable business audit evidence must include:
- Custody assignment creation/change/expiry/revocation and the actor's effective permission/scope.
- Custody offer, dispatch, carrier acceptance, in-transit handover, receipt, partial acceptance, rejection, dispute, alternative resolution and closure.
- Subject identity, stock-owning Branch, source/destination Branch/location, actual custodian, handler, sender/receiver, event times, quantity/UOM/condition and policy/assurance-profile version.
- Sensor/manual/device measurements, device identity, calibration reference, seals/containers, scans, signatures, evidence attachments and verification state as applicable.
- Custody incident report, evidence, containment, review, disposition, linked stock/financial correction and outstanding claim/dispute status.
- Shared-resource booking, actual use, custodian, Branch/capability, meter/usage readings, cleaning, calibration, inspection, maintenance, downtime and approved cost-allocation decision.
- Grant/permission expiry or revocation while physical custody is outstanding, including access hold, handover/reassignment and reconciliation outcomes.

Custody events and StockMovement records are separate domain facts. Where both occur, they must be correlated without double-counting stock. Audit records must distinguish observed readings, user assertions, device evidence and reviewed conclusions. An incident allegation is not a finding of negligence or liability.

### 5.5 Delivery route, assignment and dispatch accountability

For every DeliveryPlan/DeliveryRun lifecycle, durably record where applicable:
- Branch, Route/ManualRoute, ServiceArea assignments, route/plan/manifest version, business date/shift and source demand allocations.
- Run creation/planning, stop allocation/split, accountable run assignee and each participant's operational duty, assignment/reassignment/revocation/expiry and current permission/workforce-policy basis.
- Vehicle/transport-resource selection, booking interval, capacity/readiness checks, maintenance/inspection/cleaning/temperature evidence and replacement/release.
- Pre-dispatch validation results, any independent approval, dispatch actor/permission, exact dispatched manifest/version, stock issue and linked custody handover identifiers.
- Start/departure, stop attempt/outcome, actual quantities, route deviation/reordering, delay, hold/resume, safety/product-handling incident, breakdown, vehicle/participant replacement and return.
- Offline/device command identity, source and server timestamps, idempotency/correlation references, conflict outcome and server-side authorization revalidation.
- Run close or close-with-exceptions, reconciled delivered/returned/undelivered quantities, cash/receipt, reusable containers, unresolved custody and corrective events.

Route, assignment and transport-resource changes after dispatch must be recorded as linked events; never overwrite the original manifest or assignment evidence. Audit must distinguish planned from actual route/vehicle/personnel and distinguish a user-entered assertion from independently verified evidence.


## 6. Reliability and transaction boundaries

- For a material business operation requiring audit, write the domain state and audit intent atomically in the same database transaction or through a proven durable outbox mechanism.
- If the audit sink is unavailable, required audit evidence must remain durably queued; the operation's behavior must be explicitly classified as fail-closed or safely deferred according to business risk.
- Do not claim a critical action is fully complete if its required audit record has not been durably accepted.
- Avoid remote log-provider calls inside a business database transaction.
- Audit event publication and downstream indexing are at-least-once unless proven otherwise; consumers must deduplicate by event ID.
- Maintain stable event IDs across retries and preserve original occurrence time separately from processing time.
- Monitor outbox/audit backlog, oldest event age, rejected schemas, duplicate rates and sink availability.
- Reconciliation must identify missing event sequences/references and allow controlled repair without fabricating historical actor actions.
- Never modify historical audit evidence to make a failed operation appear successful; append a correction/annotation event.

## 7. Integrity, immutability and access controls

- Security audit records SHOULD be append-only at the application interface. Corrections are new linked records, not destructive edits.
- Restrict update/delete permissions at storage and administrative layers; separate audit writers, readers and retention administrators.
- Consider tamper-evident hash chaining, signed periodic manifests or immutable storage for high-value evidence after evaluating operational complexity and key management.
- A hash chain alone is not sufficient if an attacker can rewrite the whole chain and its trusted anchor; protect checkpoints independently.
- Apply least privilege to audit search and export. Sensitive audit details may require separate permissions from ordinary business reports.
- Tenant users may see only tenant-owned events and fields permitted by their role/scope. Platform-wide detection data remains platform-only.
- Support staff must not gain audit access to a tenant merely because they can administer the platform; use explicit scoped grants.
- Record and alert on audit configuration changes, retention changes, export and administrative access.
- Protect audit backups independently and include audit recovery in disaster-recovery exercises.

## 8. Operational logging standards

All services and workers SHOULD emit structured records with:
- Timestamp, severity, service/component, environment and release/build identifier.
- Event name and stable error classification.
- Request/job/event/correlation IDs.
- Actor/service identity reference and tenant/scope only where necessary and safe.
- Duration, dependency name and outcome.
- Retry/attempt count and idempotency reference where applicable.
- Safe diagnostic details and remediation hint.

Logging requirements:
- Use allowlisted structured fields; avoid dumping arbitrary objects or request headers.
- Redact credentials, tokens, payment secrets and sensitive personal/financial values before serialization.
- Do not include full addresses, phone numbers, bank details, GSTIN or customer names unless a documented diagnostic need and access/retention controls justify it.
- Keep stack traces and internal details in restricted diagnostics; return safe error references to clients.
- Define severity consistently (debug/info/warn/error/critical) and avoid logging the same failure at every layer without added context.
- Bound log volume and prevent attacker-controlled values from causing log injection or unbounded storage.
- Keep development/test data and telemetry separate from production.

## 9. Metrics and service-level indicators

Measure at least the following categories; numeric thresholds remain open pending workload and business-impact analysis.

### 9.1 API and client-facing services
- Request count, latency percentiles, error rate and timeout rate by route/operation class.
- Authentication/authorization denials and rate-limit outcomes.
- Database query latency, pool saturation and connection failures.
- Cache hit/miss and invalidation behavior where applicable.

### 9.2 Background processing and integrations
- Queue depth, oldest pending age, job duration, retries, dead-letter count and stuck leases.
- Outbox publication lag and consumer lag.
- Provider latency, throttling, timeout, signature failure and unknown-outcome counts.
- Tally synchronization backlog, per-batch outcomes and reconciliation age.
- Import/export/report/forecast execution duration, failure and result freshness.

### 9.3 Data, storage and recovery
- Storage bytes/object count/request classes and per-tenant usage.
- Upload scan/quarantine failures, checksum mismatches and orphan/missing object counts.
- Backup completion, age, size anomaly, checksum status and restore-test outcome.
- Database replication/backup lag and restore readiness where applicable.
- Audit outbox depth, oldest uncommitted audit intent and audit sink delivery failures.

### 9.4 Business operations
- Subscription occurrence generation lag and duplicate prevention outcomes.
- Delivery planning/fulfilment backlog and exception age.
- Procurement/collection settlement backlog.
- Reconciliation queue age and unresolved differences.
- Financial posting/reversal exceptions and accounting close blockers.

Business metrics exposed to tenant dashboards must respect tenant/branch authorization. Platform-level aggregates require a separate privacy and access policy.

## 10. Distributed tracing and correlation

- Propagate trace context across API, outbox, queue, worker and external adapter boundaries using a supported standard selected during technology evaluation.
- Preserve correlation ID for a user-visible workflow even when it spans multiple asynchronous jobs.
- Use causation IDs to connect child events to the initiating command/event.
- Do not place personal data, secrets or complete business payloads in trace baggage.
- Avoid tenant IDs, user IDs, object IDs or raw URLs as unbounded metric labels; use controlled dimensions and protected trace attributes.
- Sampling may be used for routine high-volume traces, but security/audit events and critical failure evidence must not be lost because a trace was sampled out.
- Capture external provider request IDs and outcome classifications without leaking credentials.
- Define clock synchronization and timestamp interpretation for cross-system investigations.

## 11. Alerting, incident triage and runbooks

Alerts must have severity, condition, owner, notification path, diagnostic context, runbook and escalation criteria. Avoid alerts with no clear action.

Initial alert families:
- Critical: confirmed cross-tenant access anomaly, privileged account compromise, audit evidence tampering, backup deletion/compromise, key compromise, sustained core transaction failure.
- High: repeated account takeover signals, support access outside approved grant, unusual bulk export, severe queue backlog, missing/failed backup, checksum mismatch, critical provider integration failure.
- Medium: elevated API errors/latency, growing retry/dead-letter backlog, storage quota threshold, report freshness breach, repeated upload quarantine detections.
- Informational: deployment events, scheduled maintenance and policy changes.

Thresholds, windows and routing must be tuned with measured baselines. Alerts must avoid including sensitive data and should link to a protected investigation view. Runbooks must explain safe containment, evidence preservation, recovery and escalation.

## 12. Dashboards and access boundaries

Provide distinct dashboard classes:
- **Tenant operations:** business-process backlog and tenant-scoped operational health.
- **Tenant audit:** permitted business/security events for the active tenant and branch scope.
- **Platform operations:** service health, infrastructure capacity, provider dependencies and aggregate tenant-safe metrics.
- **Security operations:** identity threats, privileged access, audit integrity, vulnerability and incident signals.
- **Engineering diagnostics:** restricted traces/logs for approved troubleshooting.

A tenant dashboard must never reveal another tenant's identity, volume, errors or usage. Platform dashboards must minimize tenant-identifiable information unless an authorized operational need exists. Support access to tenant-specific diagnostics requires an explicit grant and must itself be audited.

## 13. Retention, privacy and deletion

Retention must be defined separately for:
- Business history and financial source records.
- Security audit events.
- Operational logs.
- Metrics and traces.
- Provider request/response metadata.
- Audit exports and incident evidence.
- Backups and immutable evidence copies.

ODR-026 remains authoritative for legal/contractual data lifecycle decisions. ODR-034 covers operational telemetry and audit governance choices. Do not apply one retention period to every data class.

Requirements:
- Record purpose, owner, classification, access group, retention rule and deletion/hold behavior for each telemetry class.
- Minimize personal data and use pseudonymous references where practical.
- Apply legal hold and incident preservation without silently changing the approved retention policy.
- Ensure deletion and tenant offboarding consider replicas, indexes, exports, backups and immutable copies.
- Restrict search/export access and log access to sensitive telemetry.
- Reassess retention against current Indian legal requirements and contractual commitments before production; this document does not provide a legal retention conclusion.

## 14. Failure and abuse scenarios to test

- Business transaction commits but audit/outbox write is missing or unavailable.
- Audit publisher crashes after sink accepts event but before acknowledgement is recorded.
- Duplicate or out-of-order audit event delivery.
- Audit sink outage or quota exhaustion.
- Malicious tenant attempts to query platform-wide or another tenant's audit events.
- Platform support operator searches a tenant without a valid grant.
- Sensitive fields appear in logs, traces, metrics labels or exception messages.
- High-cardinality attacker-controlled labels exhaust telemetry storage.
- Clock skew makes event ordering ambiguous.
- Alert routing fails or a critical alert has no owner/runbook.
- Retention job deletes evidence under legal hold.
- Audit export is altered or downloaded by an unauthorized actor.
- Log/trace provider account compromise or telemetry data breach.
- Backup restore omits audit records or breaks correlation references.

## 15. Governance and evidence dependencies

- **ODR-026:** data-class retention, legal holds, offboarding and deletion; exact statutory periods await post-project review.
- **ODR-027/028:** workload-based telemetry sizing, production SLO and recovery commitments; human approval is required before launch.
- **ODR-030/031:** logging/metrics/tracing/alerting/audit storage tooling and deployment failure-domain choices require PoC/provider evidence.
- **ODR-034:** the audit-vs-telemetry split, durable material-event audit, append-oriented protection and permission-controlled queries are selected in section 16.34 of the ODR; named alert owners/escalation and exact retention schedules remain production dependencies.

## 16. Exit criteria

Before development readiness, approve audit event categories and schema, define which actions require durable audit, map business/audit transaction boundaries, establish redaction and access policies, define telemetry correlation conventions, identify initial metrics/alerts and runbooks, and record unresolved retention/tooling decisions. Before production, verify tenant-safe audit queries, audit durability under failure, sensitive-data redaction, alert routing, access controls, backup/restore and incident investigation exercises.
