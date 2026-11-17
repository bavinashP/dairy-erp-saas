# Exception, Reversal and Recovery Matrix

**Document ID:** ERR-001  
**Status:** Proposed control baseline  
**Purpose:** Define how the system detects, contains, corrects and recovers from exceptional or partially completed business operations without corrupting stock, customer accounts, farmer payables or double-entry books.

## 1. Recovery principles

1. Preserve the original event and its evidence; never silently erase a material transaction.
2. Distinguish retry (same intended command), correction (fix a draft/input), reversal (compensate a posted effect) and replacement (create a corrected new event).
3. Retry must be idempotent and must reuse the original command/source identity.
4. A reversal must reference the original posting/movement/document and state its reason and actor.
5. Correcting one subsystem must not silently leave dependent ledgers inconsistent.
6. A partial success must be represented explicitly per item/line; do not report a bulk job as wholly successful if some items failed.
7. Concurrent changes require version checks and conflict resolution, never silent last-write-wins for material quantities or money.
8. Recovery jobs must be observable, permissioned, retryable and auditable.
9. A notification, report refresh or analytics projection failure does not undo a committed source transaction.
10. Legal, tax, payroll and retention corrections require verified applicable policy; do not invent a workaround.

## 2. Exception matrix

| ID | Domain / failure | Detection and containment | Permitted recovery | Prohibited shortcut / audit evidence |
|---|---|---|---|---|
| ERR-001 | Schedule generation rerun or duplicate job | Deterministic occurrence key detects existing occurrence | Return existing result; retry missing items only | Never create duplicate occurrence; retain job/correlation ID |
| ERR-002 | Subscription changed after cut-off | Compare effective date, schedule version, dispatch lock and current version | Apply to next eligible occurrence or authorized exception review | Do not rewrite already dispatched/fulfilled history |
| ERR-003 | Customer skips/pause conflicts with holiday/extra supply | Evaluate versioned precedence policy and flag conflict | Authorized review; record selected outcome and reason | No silent precedence guess; ODR-006/007 |
| ERR-004 | Delivery partial, failed, refused or customer unavailable | Require actual quantity and reason per attempt | Retry/redelivery as new linked attempt; approved credit/adjustment if needed | Do not mark failed as delivered or bill planned quantity automatically |
| ERR-005 | Delivery worker offline submits stale data | Device command ID, expected version, event time and server state conflict | Queue for conflict review; accept only non-conflicting facts or supervisor resolution | No blind overwrite; no duplicate fulfilment |
| ERR-006 | Dispatch quantity differs from planned/allocation | Compare demand, allocation, picked and dispatched quantities | Controlled reallocation, shortage or excess exception | Do not alter original plan snapshot |
| ERR-007 | Stock insufficient during allocation/dispatch | Atomic availability check by product/location/status/lot | Backorder, partial allocation, substitute or authorized exception per policy | No implicit negative stock; ODR-012 |
| ERR-008 | Duplicate stock posting from retry/source replay | Unique source-event and idempotency constraint | Return prior movement result; reconcile missing downstream effects | No second movement for same source effect |
| ERR-009 | Transfer short/excess/damaged on receipt | Compare dispatched and received quantities/lot/status | Record discrepancy; receive accepted amount; investigate in-transit loss and post approved adjustment | Do not force transfer to balance by editing stock |
| ERR-010 | Physical count conflicts with live movements | Count cut-off/snapshot and movement sequence comparison | Reconcile movements since count; approved variance movement | Never overwrite live balance with counted quantity |
| ERR-011 | Duplicate goods receipt or wrong accepted/rejected quantity | Source PO/receipt uniqueness and quantity tolerance | Cancel draft or reverse posted movement; create corrected receipt | Do not delete posted receipt; rejected goods not available stock |
| ERR-012 | Milk quality test missing, late or contradictory | Quality state remains Pending/Quarantined | Retest or authorized decision with evidence | No accepted-stock/payable release before required decision |
| ERR-013 | Farmer rate or settlement formula changed retroactively | Compare effective rate version snapshot on collection | Recalculate only unapproved draft; posted settlement correction by adjustment | Do not rewrite approved/paid settlement |
| ERR-014 | Farmer settlement includes same collection twice | Allocation uniqueness by accepted payable quantity | Remove duplicate from draft; posted duplicate corrected by reversal/adjustment | No duplicate payable or silent deletion |
| ERR-015 | Invoice/billing run includes same billable item twice | Unique source billable allocation and run version | Rebuild draft; posted duplicate credit/reversal according to document rules | Never issue duplicate charge as a retry |
| ERR-016 | Delivered quantity and billed quantity mismatch | Reconcile fulfilment, return, approved adjustment and billable item | Hold billing item; review and post linked adjustment | Do not use planned quantity as default billable quantity |
| ERR-017 | Wrong price/tax version applied | Compare transaction snapshot with effective approved version | Draft recalc; issued document corrected by valid credit/debit/reissue workflow | Do not rewrite finalized tax/price snapshot |
| ERR-018 | Receipt amount exceeds invoice/open balance or is partly allocated | Validate unallocated receipt and open item balance atomically | Partial allocation; retain unapplied balance/on-account | Do not over-allocate or alter source receipt |
| ERR-019 | Manual UPI reference cannot be verified | Mark Recorded/Unverified and add follow-up evidence task | Manual bank statement matching or approved provider evidence | Never label user-entered reference as confirmed |
| ERR-020 | Bank statement duplicate import | File/profile/account checksum and row fingerprint | Skip known rows; import only validated new rows | Amount/date alone is not sufficient duplicate identity |
| ERR-021 | Bank reconciliation mismatch/unmatched item | Exception queue with amount/date/reference and account | Manual match with reason, adjustment or leave unmatched | Reconciliation cannot rewrite original payment evidence |
| ERR-022 | Journal unbalanced, invalid account or closed period | Posting validation fails before commit | Correct draft/mapping; reopen period only under approved workflow | Never post unbalanced journal or silently change closed period |
| ERR-023 | Duplicate journal on retry | Unique source-event/version and idempotency key | Return existing journal; reconcile downstream status | Never create duplicate financial posting |
| ERR-024 | Wrong posted journal | Immutable original with linked source reference | Authorized reversal and replacement/adjustment in permitted period | No direct edit/delete |
| ERR-025 | Subledger does not reconcile to control account | Reconciliation exception identifies source items and posting gaps | Rebuild projection; repair via approved missing/duplicate posting correction | No manual balance overwrite |
| ERR-026 | Tally mapping mismatch or target voucher already exists | Compare company-scoped mapping, checksum, voucher identity and acknowledgement | Resolve mapping; retry only items proven not committed; import acknowledgement | Never rely on names alone or blind resend |
| ERR-027 | Tally batch partially succeeds | Per-item state and target response captured | Retry failed/retryable items only; reconcile successful items | Do not resend whole batch as new vouchers |
| ERR-028 | Desktop agent offline or paired device revoked | Agent heartbeat/session and revocation state | Resume after re-authentication and batch reconciliation | No public inbound desktop access or unbounded credential reuse |
| ERR-029 | Notification provider outage | Delivery attempt failed independently from business commit | Retry with backoff/provider fallback; show undelivered status | Do not roll back sale/delivery/payment |
| ERR-030 | Report/forecast job fails | Run status, source cut-off and error recorded | Retry/rebuild projection or forecast version | Do not mutate source ledger or present stale output as current |
| ERR-031 | Payroll input corrected after finalization | Compare finalized payroll version and period status | Approved off-cycle adjustment or controlled reversal/re-run policy | Never silently recalculate finalized payslip/payroll |
| ERR-032 | Unauthorized cross-tenant/branch request | Authorization/scope check denies and records security event | No business recovery; investigate access attempt | Never fall back to unscoped query |
| ERR-033 | Attachment/import contains malicious or invalid content | Quarantine, type/size/content validation and scan status | Reject or safely reprocess through controlled pipeline | Never trust filename/MIME supplied by client |
| ERR-034 | Data export or bulk import partially fails | Per-record validation and export job manifest | Resume/retry failed records; produce downloadable error report | No silent omission or scope expansion |

## 3. Standard recovery envelope

Every recoverable command/job should retain:
- Tenant and authorized branch/location context.
- Stable source record ID and source version.
- Idempotency key and correlation/causation ID.
- Actor or service identity and permission context.
- Command received time, business/effective date and processing time.
- Input checksum/version and validation outcome.
- Per-step/per-record result, retryability classification and error code.
- Linked stock movement, financial journal, document, external voucher or reversal IDs.
- Retry count, last error and next eligible retry policy.
- Manual resolution actor, reason, approval and timestamp.

Secrets, passwords, OTPs, payment PINs and full credentials must never appear in this envelope or ordinary logs.

## 4. Failure classes and retry policy

| Class | Example | Retry behavior |
|---|---|---|
| Validation/business rejection | Invalid state, missing approval, closed period | Do not auto-retry unchanged command; correct input/policy |
| Authorization rejection | Wrong tenant/branch/role | Do not retry until authorization is legitimately changed |
| Transient infrastructure | Network timeout, temporary database/provider unavailability | Bounded exponential backoff with jitter; same idempotency key |
| Concurrency conflict | Stale version, simultaneous allocation | Fetch current state and require explicit merge/redecision |
| Partial batch outcome | Some Tally/import rows succeeded | Retry only failed items after acknowledgement reconciliation |
| Permanent external incompatibility | Unsupported voucher field/version | Stop, report mapping limitation and require adapter/mapping update |
| Compensating action required | Incorrect posted sale/journal/stock movement | Create approved linked reversal/adjustment; not a blind retry |

Exact retry counts, delay schedules and dead-letter retention are NFR/operations decisions, not defined here.

## 5. Reconciliation and recovery queues

At minimum, operational monitoring should expose:
- Schedule generation gaps/duplicates.
- Delivery attempts without finalized fulfilment outcome.
- Dispatch/stock issue discrepancies.
- Transfer in-transit ageing and quantity variance.
- Accepted collection not reflected in raw milk stock or farmer payable as configured.
- Billable fulfilment not billed; duplicate or over-billed source items.
- Unallocated receipts, unmatched bank lines and unreconciled accounts.
- Failed/unbalanced/missing journals and subledger-control mismatches.
- Tally batch items with unknown target outcome or unresolved conflict.
- Payroll, notification, analytics and export jobs with terminal failures.

Each queue must have tenant/branch scope, responsible role, age/SLA indicator where approved, safe retry/resolution action and audit history.

## 6. Open decisions and dependencies

- ODR-006/007/014: subscription precedence, cut-offs and billing correction.
- ODR-008/011/012: quantity precision, lots, stock policy and negative stock.
- ODR-009/010: quality and farmer settlement recovery.
- ODR-013/016/017: pricing, tax, accounting and legal document correction.
- ODR-015: payment verification evidence.
- ODR-019/020: mobile/offline conflict handling.
- ODR-021: notification channels and retry provider policy.
- ODR-025/026: tenant suspension, data export/retention and offboarding.
- ODR-027/028/031: capacity, recovery objectives and deployment topology.
- ODR-029/030: integration versions and technology choices.

## 7. Acceptance test requirements

Every exception row must eventually have:
- Trigger and fixture data.
- Expected state before/after.
- Expected stock, receivable/payable and journal effects.
- Permission and tenant/branch scope checks.
- Retry and duplicate-command test.
- Concurrent execution test where relevant.
- Reversal/compensation and audit verification.
- Recovery queue visibility and resolution test.

This matrix is a proposed control baseline; it does not close related ODR items.
