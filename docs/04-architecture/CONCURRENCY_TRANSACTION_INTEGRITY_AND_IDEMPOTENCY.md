# Concurrency, Transaction Integrity and Idempotency

**Document ID:** ARC-CONC-001  
**Status:** Proposed architecture baseline — storage-specific mechanisms open  
**Authority:** FRS-001, ERS-001, NFR-001, Accounting Double-Entry and Financial Controls, Inventory Stock Ledger and Reconciliation, Subscription Lifecycle and Recurrence Rules, Tally Desktop Synchronization, Business Rules Catalogue, and ODR-006/007/008/010/012/014/017/020/027/030/031.  
**Purpose:** Prevent duplicate, lost, inconsistent or untraceable business effects when users, devices, APIs and background workers act concurrently or retry after uncertain outcomes.

## 1. Scope

This document defines logical consistency rules for transactional and asynchronous workflows. It does not select a database engine, isolation level, ORM, queue, distributed lock service or deployment topology. Those decisions require technology evaluation and proof of concept.

## 2. Core invariants

1. Every material business effect has one authoritative source event/document and stable identity.
2. A retry MUST NOT create a second logical effect.
3. A concurrent conflicting update MUST be detected or serialized; it must not silently overwrite another committed change.
4. A committed operation has a durable, queryable outcome.
5. An operation must not report success before its required authoritative state is durably committed.
6. Failure of a later notification, analytics refresh or external synchronization must not roll back an already committed core transaction unless it is part of the same explicitly defined atomic boundary.
7. Financial journals, stock movements, receipts, settlements and fulfilments are immutable after posting/completion except through linked correction/reversal workflows.
8. Related state changes and their audit/outbox intent must be committed atomically where the selected architecture uses an outbox.
9. A pre-check followed by an insert/update is not sufficient to enforce uniqueness under concurrency.
10. Tenant and branch scope are part of every operation's authorization and consistency boundary.

## 3. Concurrency hazard catalogue

| Domain/workflow | Concurrent hazard | Required control |
|---|---|---|
| Subscription recurrence | Two schedulers generate same customer/line/date/shift occurrence | Deterministic occurrence key plus persistence uniqueness |
| Schedule edits | Edit overlaps with generated future occurrences | Effective-dated immutable schedule versions and explicit regeneration policy |
| Delivery | Two devices mark same stop delivered/failed or edit actual quantity | Versioned state transition, actor/time/evidence and conflict resolution |
| POS | Retry after timeout creates duplicate sale/receipt | Idempotency key and durable command result |
| POS cash session | Two operators close or reconcile same session | State/version precondition and serialized close |
| Stock reservation | Two orders reserve same available quantity | Atomic reservation against authoritative stock/lot availability |
| Stock issue/transfer | Concurrent issue exceeds stock or transfer consumes twice | Atomic ledger posting and source reference uniqueness |
| Stock count | Count overlaps with movements or another count | Cut-off/versioned count and controlled variance posting |
| Milk collection | Same farmer/session/measurement submitted twice | Source device/event identity and duplicate detection policy |
| Farmer settlement | Two settlement runs include same accepted collection | Unique settlement allocation and locked/claimed source set |
| Billing | Two billing workers invoice same billable item | Unique source billable allocation and run idempotency |
| Receipts | Same bank/UPI evidence imported twice | External reference deduplication with manual ambiguity queue |
| Refunds | Repeated request issues duplicate refund | Idempotency plus original receipt/refund limit control |
| Accounting | Two posting attempts create duplicate journal | Unique source event and posting-rule version reference |
| Accounting period | Close races with a backdated posting | Period state lock/serialized transition and posting-date validation |
| Tally sync | Agent retries after lost acknowledgement | Stable sync record identity and external mapping/verification |
| Payroll | Two finalization runs pay the same period twice | Unique employee/period/pay-run scope and approval lock |
| Approvals | Initiator and approver act on stale or changed payload | Approval bound to immutable payload/version/hash and policy version |
| Delegated approvals | Delegation expires/revokes or delegator/delegate authority changes while approval is in flight | Revalidate both parties, delegation scope/limits and payload at commit; coordinate revocation/expiry atomically; idempotent decision identity |
| SaaS entitlement | Concurrent requests exceed a usage limit | Atomic quota reservation/consumption; reconciliation for abandoned reservations |
| File import | Same file/batch submitted repeatedly | File fingerprint plus tenant/operation idempotency and row-level identity |
| Analytics | Multiple refreshes overwrite newer snapshots with older data | Source watermark, run version and monotonic publication rule |

## 4. Transaction boundary design

For each use case, document:
- Aggregate/resource whose invariants must hold.
- Rows/entities or equivalent records read and changed.
- Required uniqueness and referential constraints.
- Whether atomicity is required across all changes.
- Locking or optimistic version precondition.
- Idempotency key and semantic fingerprint.
- Audit event and outbox message.
- External side effects and compensation/reconciliation.
- Retry classification and user-visible outcome.

Keep database transactions bounded. Do not hold a database transaction open while waiting for a mobile client, user approval, email/SMS provider, bank API, Tally desktop agent or other network service.

A multi-step business workflow that cannot fit in one local transaction must be modelled as a durable state machine/process with explicit pending, completed, failed, compensating and reconciliation states. Never imply distributed atomicity where none exists.

## 5. Optimistic concurrency and conflict handling

Mutable resources SHOULD carry a monotonically changing version or equivalent concurrency token. A command may include the version it read. The server commits only if the current version matches the precondition; otherwise it returns a conflict with safe latest-state information.

Use this for edits to:
- Subscription schedules and exceptions.
- Delivery run/stop state.
- POS sessions.
- Procurement/quality decisions before posting.
- Stock counts and adjustments.
- Approval requests and payloads.
- Accounting period and posting configuration.
- Tenant/branch configuration and permission assignments.

For highly contended inventory, quota or financial resources, evaluate atomic conditional updates, row-level serialization, or another storage-native mechanism. Choose based on measured contention and invariant needs, not a blanket lock on every request.

Conflict response must state whether the command was not applied, already applied, or has an unknown external outcome. Do not silently auto-merge quantities, money or approval decisions.

## 6. Idempotency protocol

For each retryable material command:
1. Client/service creates a stable idempotency key for one intended action.
2. Server scopes it to tenant, principal/service and operation.
3. Server computes/records a canonical semantic request fingerprint.
4. An atomic uniqueness mechanism claims the key.
5. The business operation and durable result are committed according to its transaction boundary.
6. Repeating the same key and same payload returns the stored result or current operation status.
7. Same key with different semantic payload is rejected as conflict.
8. Concurrent requests using the same key converge on one operation result.
9. Key expiry/retention must not permit a delayed retry to recreate a harmful effect; policy depends on workflow and is explicitly configured.
10. Unknown outcomes are reconciled before a new key is used for the same external action.

Idempotency keys do not replace domain uniqueness. For example, one schedule occurrence, one billing allocation or one source-event journal must also be unique by its business identity.

## 7. Inventory and quantity integrity

- Stock balances derive from stock movement ledger entries, not independent mutable totals.
- Reservations, physical issues, transfers, returns, wastage and adjustments are distinct event types.
- Availability calculation must account for the defined reservation and in-flight policy.
- A stock issue cannot exceed available quantity under the approved negative-stock policy.
- Transfer source and destination effects must be linked and cannot be posted twice.
- Lot/expiry/quality constraints must be checked at commit time.
- Unit conversions and precision use versioned rules/snapshots.
- Reconciliation differences create controlled adjustment records; they do not rewrite historical movements.

## 8. Financial and double-entry integrity

- A journal is posted only after all lines, accounts, period, dimensions, source and debit/credit totals validate.
- Debits and credits must balance exactly under the approved currency precision policy; do not use binary floating-point for money.
- A source event can create at most one intended posting per posting-rule version/financial scope unless an explicit split policy exists.
- Subledger source records and control-account postings must be reconcilable.
- Posted journals are immutable; corrections use linked reversal/adjustment entries.
- Receipt allocation and refunds cannot exceed the valid unapplied/available amount.
- Period close and posting must have a race-safe boundary.
- Accounting source event, posting state and outbox intent must not diverge silently.
- Tally retries must not create duplicate vouchers; lost acknowledgement requires lookup/reconciliation using stable source identity and verified target-version behavior.

## 9. Scheduling, billing and settlement integrity

- Schedule occurrence generation is deterministic from subscription line, schedule version, business date and shift.
- A changed recurrence creates a new effective version; it must not silently rewrite completed or billed occurrences.
- Billing must allocate each billable source item once according to approved policy.
- Partial fulfilment, return, cancellation, pause and correction must have explicit billable quantity rules.
- Farmer settlement must claim accepted collection entries under a defined period/rate version and prevent duplicate inclusion.
- Reopening/recalculation must create a traceable new version or adjustment, not erase a finalized settlement.
- Every cut-off uses an explicit business date/timezone and policy version.

## 10. Quotas, approvals and configuration

Plan usage limits, approval thresholds and configurable business policies can be raced by concurrent operations. The system must reserve/consume quotas atomically where a hard limit is promised, and evaluate approval against the exact immutable operation payload and policy version.

A policy change must define whether it affects in-flight requests. Do not retroactively reinterpret finalized transactions without an explicit migration/recalculation process.

Delegated approval validity, both parties' current membership/permissions, scope, limits and the immutable approval payload/policy version must be revalidated server-side at the approval commit boundary. The validation and commit must be coordinated atomically with delegation revocation/expiry and authority changes. A revocation, expiry or loss of underlying authority that wins the race must reject an uncommitted approval; a decision already committed while valid remains immutable. Duplicate retries must not create a second approval action.

## 11. Failure and recovery semantics

Classify outcomes:
- **Not started:** no business effect; safe retry.
- **Committed:** authoritative effect persisted; return/retrieve prior result.
- **Rejected:** validation/authorization/state precondition failed; correct request/policy.
- **In progress:** durable operation exists; poll/subscribe to status.
- **Unknown external outcome:** provider may have acted; reconcile before retry.
- **Compensation required:** a prior committed step must be counteracted by a new linked action.
- **Manual resolution:** ambiguous or unsafe automatic recovery; preserve evidence and queue for authorized action.

A process crash at every boundary must be considered in tests. No retry loop may create an unbounded storm or silently discard a permanent error.

## 12. Required concurrency test scenarios

Use deterministic barriers/latches or equivalent coordination to force races; timing-only tests are insufficient.

- Two identical idempotency requests arriving simultaneously.
- Two different updates using the same stale version.
- Two schedulers generating the same occurrence.
- Two sales consuming the last available stock.
- Two settlements claiming the same collection entries.
- Two billing workers allocating the same billable item.
- Two accounting workers posting the same source event.
- Period close racing with a journal post.
- Approval decision racing with payload/policy change.
- Delegation revocation/expiry or delegator/delegate permission loss racing with an approval commit.
- Duplicate submission/retry of the same delegated approval decision.
- Quota consumption at the last available unit.
- Tally sync retry after remote success but before local acknowledgement.
- Outbox publisher crash after send but before marking delivered.
- Worker lease expiry while original worker is still executing.
- Tenant A and tenant B jobs executing concurrently in the same worker process.

Each test must assert final persisted state, ledger totals, uniqueness, audit trail, outbox state and client-visible outcome.

## 13. Technology proof-of-concept requirements

When candidate persistence/queue technologies are evaluated, demonstrate:
- Unique constraints under concurrent inserts.
- Transaction isolation and deadlock behavior.
- Conditional update/compare-and-swap or row-lock support.
- Transactional outbox feasibility.
- Retry and worker lease behavior.
- Backup/restore of transactional state and pending messages.
- Tenant and branch scoped query patterns.
- Observed throughput/latency under representative contention.
- Failure recovery without duplicate financial or stock effects.

## 14. Open decisions

ODR-006/007 recurrence/cut-off; ODR-008 quantity precision; ODR-010 farmer settlement; ODR-012 stock policy; ODR-014 billing; ODR-017 accounting; ODR-020 offline conflict handling; ODR-027 workload/concurrency targets; ODR-030 storage/queue technology; ODR-031 production topology.

## 15. Exit criteria

Before implementation, every critical workflow must have a documented transaction boundary, business uniqueness key, idempotency behavior, concurrency precondition, audit/outbox behavior, recovery path and deterministic test case. Storage-specific mechanisms require a technology ADR and proof-of-concept evidence.
