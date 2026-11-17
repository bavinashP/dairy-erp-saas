# Event-Driven Processing, Background Jobs and Transactional Outbox

**Document ID:** ARC-EVT-001  
**Status:** Proposed architecture baseline — broker and implementation choices open  
**Authority:** FRS-001, ERS-001, NFR-001, ARC-TEN-001, ARC-IAM-001, ARC-CONC-001, Accounting Double-Entry and Financial Controls, Tally Desktop Synchronization, and ODR-021/022/023/027/028/029/030/031.  
**Purpose:** Define reliable asynchronous processing for notifications, analytics, integrations, imports/exports and cross-domain workflows without losing, duplicating or mis-scoping business effects.

## 1. Scope

This document defines logical events, job execution, retries, outbox/inbox, orchestration and operational controls. It does not select a message broker, queue product, workflow engine, database, scheduler or deployment topology.

## 2. Core principles

1. The authoritative business transaction commits independently from non-critical downstream delivery unless the business invariant explicitly requires one atomic boundary.
2. A database commit and the intention to publish its event must not be separated by an unsafe dual-write gap.
3. Delivery is assumed to be at-least-once unless a specific end-to-end guarantee is proven; consumers MUST be idempotent.
4. Event ordering is guaranteed only where explicitly designed and tested for a defined aggregate/key.
5. Events carry identity and references, not unrestricted copies of sensitive tenant data.
6. Every event/job has tenant scope and explicit branch scope when applicable.
7. A retry must preserve operation identity and must not duplicate business effects.
8. External calls are outside local database transactions; unknown outcomes require reconciliation.
9. Failed/permanent messages remain visible and recoverable; no silent discard.
10. Analytics, notifications and integrations must not block POS, delivery, procurement or accounting unnecessarily.

## 3. Event categories

Distinguish:
- **Domain event:** A fact that has occurred in the domain, such as SubscriptionOccurrenceGenerated, DeliveryFulfilled, StockMovementPosted, ReceiptRecorded, JournalPosted.
- **Integration event:** A versioned contract published for an external consumer, with minimized payload and explicit compatibility.
- **Command/job:** A request to perform work, such as GenerateBillingRun, SendNotification, BuildReport, ExportTallyBatch.
- **Scheduled trigger:** A time-based request to evaluate work due under a tenant's business calendar/timezone.
- **Audit/security event:** A durable record of who attempted or performed an action; it is not interchangeable with an operational event.

A command requests work; an event states that something happened. Do not use event names to hide imperative commands.

## 4. Transactional outbox pattern

Where a committed transaction must reliably cause asynchronous work:
1. Validate and authorize the command.
2. In one local database transaction, persist the authoritative business state and an outbox record containing the event identity/type/version and minimal payload/reference.
3. Commit both or neither.
4. A publisher claims pending outbox records using concurrency-safe claiming/lease semantics.
5. Publisher sends the event to the selected transport.
6. Publisher records delivery/attempt state. If it crashes after send but before marking delivered, it may send again.
7. Consumers deduplicate by stable event ID and apply effects idempotently.
8. Failed events are retried with bounded backoff and eventually moved to a visible dead-letter/manual-resolution state according to policy.

Do not claim exactly-once delivery merely because an outbox exists. The pattern provides durable publication intent; duplicate delivery remains possible.

For low-risk internal actions that do not require durable asynchronous work, a simpler synchronous path may be used with documented rationale. Do not introduce an event broker for every function by default.

## 5. Consumer inbox and deduplication

Consumers that apply material effects SHOULD maintain an inbox/processed-message identity under the correct tenant/security boundary. A duplicate event must return/retain the prior processing outcome without repeating the effect.

Consumer processing should:
- Validate event schema/version and producer identity.
- Resolve and validate tenant and branch scope.
- Check consumer authorization/service capability.
- Deduplicate event ID and relevant domain business key.
- Apply state change and record processing result atomically where possible.
- Emit downstream outbox event if further work is required.
- Record retryable/permanent error classification.

Event ID alone may not prevent duplicate business commands created with different IDs; domain-level uniqueness is still required.

## 6. Event envelope and contract

A versioned envelope SHOULD include:
- Event ID and event type.
- Schema version.
- Occurred-at instant and relevant business date/timezone where applicable.
- Tenant ID and branch/location ID only when applicable.
- Aggregate/resource type and stable ID.
- Aggregate version/sequence where ordering matters.
- Source command/correlation/causation ID.
- Actor/service identity reference.
- Producer and trace/correlation metadata.
- Minimal domain payload or reference to an authorized fetch operation.

Never include passwords, tokens, OTPs, payment PINs, private keys or unnecessary personal/financial data. Event consumers must not trust payload fields as authorization evidence.

## 7. Tenant-safe job execution

Every job MUST declare:
- Tenant ID.
- Branch/location scope or explicit tenant-wide marker.
- Service identity and allowed capability.
- Source operation/event and idempotency identity.
- Input schema/version.
- Retry policy and deadline/expiry where appropriate.
- Correlation/causation IDs.
- Expected result and status model.

A worker must establish a fresh explicit execution context for each job and clear it on completion/failure. A worker process handling Tenant A must never reuse Tenant A context for Tenant B. Missing or invalid tenant context fails closed.

Jobs must revalidate tenant status, permission/policy version, resource ownership and whether the operation remains valid at execution time. Sensitive jobs may require authorization snapshot or re-approval rules; these must be explicitly documented.

## 8. Scheduling and recurring work

Scheduled processes include subscription occurrence generation, delivery planning, billing runs, farmer settlement, payroll periods, reminders, report refreshes, forecasts, backup checks and Tally sync polling/agent coordination.

Requirements:
- Scheduler triggers are not the business occurrence itself; a durable run record identifies intended work.
- Every scheduled run has tenant, business date, timezone/calendar, schedule/policy version and deterministic run identity.
- Re-running a missed scheduler window must safely catch up without duplicates.
- Overlapping scheduler instances must not create duplicate occurrences or settlements.
- Cut-off and late-arriving data policies must be explicit.
- Paused/suspended tenants and disabled capabilities must be handled by approved lifecycle policy.
- Long-running tasks must expose progress, heartbeat/lease and recoverable state.
- Do not rely solely on process-local timers for business-critical schedules.

## 9. Retries, backoff and dead-letter handling

Classify errors as:
- Transient: network interruption, temporary provider failure, throttling.
- Permanent: invalid mapping/schema, unsupported operation, authorization revoked, business rule rejection.
- Unknown outcome: remote side may have completed but acknowledgement was lost.
- Poison message: malformed or repeatedly failing event requiring isolation.
- Cancelled/expired: work no longer valid under its policy.

Retry transient failures with bounded exponential backoff and jitter, honoring provider retry hints where appropriate. Permanent errors go to actionable resolution without endless retry. Unknown outcomes must reconcile before resend. Retry limits and retention are configurable per job/provider class but bounded by platform safety policy.

Dead-letter records MUST preserve safe diagnostic metadata, original event identity, attempts, timestamps, error classification and remediation history. Sensitive payloads must be redacted or access-controlled.

## 10. Long-running operations and user visibility

Imports, exports, bulk changes, reports, forecasts and Tally batches should expose a durable operation resource with:
- Accepted/queued/running/succeeded/partially_succeeded/failed/cancelled/needs_review states as appropriate.
- Created/started/updated/completed timestamps.
- Requesting actor, tenant and permitted scope.
- Progress counters that do not expose unauthorized records.
- Result artifact or structured error summary.
- Retry/resume/cancel permissions and state rules.
- Expiry/retention policy for result files.
- Correlation/reference ID for support.

Cancellation is cooperative and only safe at documented checkpoints. Do not imply rollback of already committed sub-operations.

## 11. Domain-specific workflows

### 11.1 Accounting
A source operational event and its posting instruction/state must remain consistent. Use a local atomic transaction or durable outbox/reconciliation pattern. Journal posting is idempotent by source event and posting scope. Tally export is a separate integration process and must never be required for the SaaS journal to be considered posted.

### 11.2 Tally desktop synchronization
The desktop agent initiates authenticated outbound communication. Sync batches have stable batch and record identities, adapter/schema version, selected company identity, mappings, acknowledgements and per-record outcomes. A lost acknowledgement requires reconciliation before re-export. Do not expose Tally's local HTTP endpoint publicly.

### 11.3 Notifications
Notification failure must not undo a committed sale, delivery, invoice or receipt. Track consent, template version, recipient/channel, provider response, retry state and delivery outcome. Avoid duplicate customer messages on job replay.

### 11.4 Reporting and forecasts
Heavy reports and forecast runs use bounded asynchronous jobs. Capture source-data cut-off, definition/model version, tenant/branch scope, filters and freshness. Publish a completed result only if its source watermark/version is not older than a newer valid result. Analytics must not block transactional workflows.

### 11.5 Imports and exports
Validate files before processing, bind each job to tenant and actor scope, produce row-level results, protect generated artifacts and support safe retry without duplicate rows or vouchers.

## 12. Operational controls and observability

Monitor:
- Outbox age/depth and oldest pending event.
- Publisher/consumer throughput and failure rate.
- Retry counts, backoff age and dead-letter volume.
- Job queue age, execution duration, lease expiry and stuck jobs.
- Per-tenant workload/fairness and noisy-neighbour indicators.
- Provider latency, throttling and unknown outcomes.
- Event schema rejection and consumer lag.
- Tally batch reconciliation backlog.
- Report/forecast generation duration and data freshness.

Alerts must identify severity, owner and runbook. Operations dashboards and support tools must enforce tenant access controls. Provide controlled replay/re-drive with authorization, reason, idempotency protection and audit.

## 13. Failure and recovery tests

- Crash after business commit but before publisher sees outbox row.
- Crash after event send but before outbox delivery mark.
- Duplicate event delivery to a consumer.
- Consumer crash after effect commit but before acknowledgement.
- Tenant context missing or incorrectly reused by worker.
- Two scheduler instances create same run/occurrence.
- Worker lease expires while original worker is still running.
- Provider accepts request but response is lost.
- Permanent mapping error enters manual resolution.
- Dead-letter replay after policy or schema correction.
- Newer analytics result completes before an older run.
- Tenant suspension while jobs are queued/running.
- Cancellation during partially completed bulk work.
- Tally agent disconnects after remote voucher creation.

## 14. Open decisions

ODR-021 notification providers/consent; ODR-022/023 analytics/forecasting; ODR-027 workload and queue targets; ODR-028 recovery objectives; ODR-029 integrations; ODR-030 broker/scheduler/workflow technology; ODR-031 deployment topology.

## 15. Exit criteria

Before implementation, select which workflows require durable asynchronous processing, define event/job contracts and tenant context, identify transaction/outbox boundaries, approve retry/dead-letter/replay policies, define operation visibility and retention, and demonstrate duplicate-safe processing under failure in a proof of concept.
