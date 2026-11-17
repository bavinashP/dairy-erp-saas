# State Transition Matrix

**Document ID:** STM-001  
**Status:** Candidate lifecycle model — not approved state machine  
**Purpose:** Define controlled lifecycle transitions, actors, guards, side effects and recovery paths across core dairy workflows.

## 1. State-machine conventions

- A state describes the persisted lifecycle of one identified business record; a screen label is not a state.
- A transition is a command with actor, permission, current version, guard conditions, reason/evidence and resulting event.
- All transitions execute server-side and are concurrency-safe.
- Repeated commands must be idempotent or return the original result.
- A failed transition must not leave partial stock or financial effects.
- Posted/issued/closed history is corrected through linked compensating records, not mutation.
- The states below are proposed from existing domain documents. Exact names and transitions remain subject to product-owner approval and related ODR decisions.

## 2. Cross-module transition controls

| Control | Required behavior |
|---|---|
| Authorization | Validate tenant, branch/location, action, resource state, assignment and approval limit at transition time |
| Concurrency | Require expected version or equivalent; stale command returns conflict and latest state reference |
| Idempotency | Store command key/source identity and outcome for retryable transitions |
| Audit | Record actor, timestamp, source, reason, before/after state, correlation ID and approval |
| Side effects | Commit source transition and local ledger effects atomically, or use durable outbox/retry/reconciliation |
| Reversal | Link correction to original event; do not erase original event |
| Effective date | Distinguish business date/effective date from server timestamp and posting date |
| Notifications | Trigger after commit; notification failure does not undo the transition |

## 3. Candidate state matrices

### 3.1 Customer subscription agreement

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Draft | Submit → Pending Confirmation | Required customer, service address, lines, recurrence and effective date valid | No normal demand generated |
| Draft | Activate → Active | Only if confirmation is not required by approved policy | Create versioned active schedule |
| Pending Confirmation | Confirm → Active | Confirmation evidence/actor valid | Schedule version becomes effective |
| Pending Confirmation | Reject → Rejected | Reason required | No future occurrences |
| Draft/Pending Confirmation | Cancel → Cancelled | Permission and reason | Preserve history |
| Active | Pause → Paused | Effective date/cut-off policy permits | Create schedule exception; do not delete generated occurrences |
| Paused | Resume → Active | Resume date and schedule version valid | Generate future eligible occurrences idempotently |
| Active | Amend → Active (new version) | Change effective date/cut-off guard | Prior version remains immutable for generated occurrences |
| Active/Paused | Cancel → Cancelled | Cancellation effective date and outstanding work reviewed | Stop future normal occurrences; preserve open fulfilment/billing |
| Cancelled | Reinstate | Not assumed | ODR-006 decision required; otherwise create a new agreement |

**Open:** exact state set, confirmation, reinstatement, holiday/skip semantics and exception precedence (ODR-006/007).

### 3.2 Schedule occurrence

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Generated/Planned | Allocate → Allocated | Active schedule, eligible product, stock policy | Reservation/allocation recorded separately from physical issue |
| Allocated | Dispatch → Dispatched | Dispatch run valid, quantity and assignment checked | Stock issue follows approved dispatch posting policy |
| Planned/Allocated | Skip/Cancel → Exception Review or Cancelled | Reason, cut-off and permission | Must not erase occurrence; billing treatment remains policy-driven |
| Dispatched | Record outcome → Fulfilled / Partially Fulfilled / Failed | Actual quantity and outcome reason | Create attempt and fulfilment facts |
| Fulfilled/Partially Fulfilled/Failed | Correct → Exception Review | Reason, approval and original reference | Linked correction/return/credit; no overwrite |
| Any eligible state | Duplicate generation retry | Same deterministic generation key | Return existing occurrence, never create duplicate |

**Open:** partial fulfillment aggregation and multiple-attempt billing semantics (ODR-006/007/014/020).

### 3.3 Delivery run

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Draft | Plan → Planned | Stops and source demand valid | Demand references preserved |
| Planned | Assign → Assigned | Worker/device scope valid | Assignment audit |
| Assigned | Validate/lock → Dispatch Ready | Quantities, route and exceptions reviewed | Prevent unapproved edits |
| Dispatch Ready | Dispatch → In Progress | Actor and dispatch checks pass | Record dispatch timestamp and stock issue according to policy |
| In Progress | Complete → Completed | All stops have outcomes | Unresolved stops block normal completion |
| In Progress | Close with exceptions → Completed with Exceptions | Each exception has reason and review path | Preserve unresolved financial/stock consequences |
| Completed states | Correct/Reopen → Controlled Exception Review | Authorized supervisor, reason and linked correction | Original run remains historically visible |

**Open:** offline protocol, run locking, assignment scope, proof requirements and correction authority (ODR-019/020).

### 3.4 Delivery stop / attempt / fulfilment

| Record | Candidate states/outcomes | Required guards and evidence |
|---|---|---|
| DeliveryStop | Pending, In Progress, Delivered, Partially Delivered, Failed, Refused, Rescheduled, Exception Review | Outcome reason; actual quantity; attempt identity |
| DeliveryAttempt | Started, Succeeded, Partial, Failed, Aborted | Actor, timestamp, stop, evidence as configured |
| Fulfilment | Draft, Confirmed, Corrected/Reversed (linked) | Source demand line, product/UOM, actual delivered/returned quantities |

An attempt outcome must not automatically imply billing, payment receipt or customer acceptance beyond the evidence actually captured.

### 3.5 Procurement / goods receipt

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Draft | Submit → Pending Approval (if policy requires) | Supplier, lines and terms valid | No stock/payable effect |
| Draft/Pending Approval | Approve → Approved | Approver permission and limits | Preserve approved version |
| Approved | Receive → Partially Received / Received | Receipt quantities and location valid | Accepted quantity posts to stock once; rejected quantity remains separate |
| Partially Received | Receive remaining → Partially Received / Received | Outstanding quantity reconciled | Partial receipts supported |
| Received | Return/Adjust → Exception Review / Return Posted | Reason, source receipt and approval | Linked stock and payable adjustments |
| Any draft state | Cancel → Cancelled | No posted irreversible effect | Posted effects require reversal, not cancel |

**Open:** PO mandatory policy, tolerances, receipt/ invoice matching and procurement payable trigger (ODR-008/010/012/016/017).

### 3.6 Milk collection, quality and farmer settlement

| Record | Candidate states | Guards / downstream effects |
|---|---|---|
| Collection entry | Recorded → Sampled/Tested → Accepted / Rejected / Quarantined / Pending Decision | Farmer, collection session, measured quantity, test evidence and policy version |
| Quality decision | Pending → Accepted / Rejected / Quarantined → Released (if policy permits) | Authorized reviewer and reason; rejected milk not available stock |
| Farmer settlement | Draft → Calculated → Reviewed → Approved → Partially Paid / Paid / Reversed by linked adjustment | Accepted quantity, rate snapshot, deductions/advances and no duplicate allocation |
| Farmer payment | Recorded → Confirmed/Reconciled/Failed/Reversed | Payment evidence and account; settlement is not payment |

**Open:** quality thresholds, rate formula, sample requirements and settlement cycle (ODR-009/010).

### 3.7 Inventory transfer and stock count

| Record | Candidate states | Guards / downstream effects |
|---|---|---|
| Transfer | Draft → Requested → Approved → Dispatched → Partially Received / Received → Closed / Exceptioned | Dispatch reduces source available stock; in-transit custody tracked; destination increases only on receipt |
| Stock count | Draft → In Progress → Submitted → Reviewed → Approved → Posted → Closed | Count cut-off, movement-aware reconciliation, approval and stock adjustment movement |
| Stock adjustment | Draft → Pending Approval → Posted / Rejected | Reason, source count/exception, actor and linked movement |

**Open:** transfer custody, lot rules, count cut-off, negative stock and valuation (ODR-008/011/012/017).

### 3.8 Billing document and receipt

| Record | Candidate states | Guards / downstream effects |
|---|---|---|
| Billing run | Draft → Calculated → Reviewed → Approved → Posted/Issued → Closed | Source billable items, effective price/tax versions, duplicate-billing prevention |
| Invoice/document | Draft → Reviewed/Approved (if required) → Issued/Posted → Partially Settled → Settled → Adjusted/Credited | Finalized document immutable; correction linked |
| Receipt | Recorded/Unverified → Confirmed by Evidence → Partially Allocated / Allocated → Reconciled; alternate Failed/Reversed/Refunded | Receipt and bank evidence separate; allocations cannot exceed unallocated amount |
| Statement | Generated → Reviewed → Issued/Shared → Superseded by new version | Statement summarizes account activity; not a substitute invoice |

**Open:** legal document applicability, billing cycle/cut-off, tax, receipt verification and proration (ODR-013..016).

### 3.9 Accounting journal and financial period

| Record | Candidate states | Guards / downstream effects |
|---|---|---|
| Journal entry | Draft → Pending Approval → Posted; or Rejected/Cancelled before posting | Balanced lines, valid period/accounts, unique source reference and approval |
| Posted journal | Posted → Reversed by linked journal; replacement/adjustment posted separately | No direct mutation or hard deletion |
| Accounting period | Open → Closing Review → Closed → Reopened by controlled approval (if allowed) | Close checks failed/unposted transactions, reconciliations and actor evidence |

**Open:** books/legal-entity scope, approval, voucher policy and close/reopen policy (ODR-003/017).

### 3.10 Tally synchronization batch

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Draft | Prepare → Validating | Paired authorized device and selected company | No target write |
| Validating | Preview Ready / Validation Failed | Schema/version and mapping checks | Unsupported/lossy fields shown |
| Preview Ready | Approve → Approved | Authorized reviewer and batch checksum/version unchanged | Freeze approved payload |
| Approved | Send/Import → Processing | Idempotency keys and connection valid | Per-item outcomes |
| Processing | Complete / Partial Failure / Failed | Acknowledgements reconciled | Retry only failed/retryable items |
| Any conflict | Conflict Review → Resolved / Rejected | Explicit mapping decision and audit | No blind overwrite |

**Open:** supported Tally releases, field mapping depth and offline agent conflict protocol (ODR-017/020/029/030).

## 4. Cross-domain transition dependencies

- Subscription activation/amendment creates or versions future schedule occurrences.
- Occurrence allocation may reserve stock; dispatch and fulfilment create separate actual events.
- Confirmed fulfilment and approved sales create billable items under billing policy.
- Billing document posting creates receivable and financial posting instructions.
- Receipt allocation reduces open receivable; bank reconciliation provides separate settlement evidence.
- Accepted milk collection may create raw-milk stock and farmer payable under approved policy; settlement approval and payment remain separate.
- Goods receipt acceptance creates stock; supplier invoice matching creates payable under accounting policy.
- Stock movements and financial journals must reconcile but are separate ledgers with linked source references.
- Tally synchronization exports/imports mapped accounting facts; it must not become the SaaS accounting source of truth.

## 5. Required state-machine test classes

For each transition:
1. Allowed transition from valid current state.
2. Rejected transition from invalid state.
3. Permission denied for wrong role/scope.
4. Missing/invalid required evidence or reason.
5. Duplicate command/retry idempotency.
6. Stale version/concurrent update conflict.
7. Failure between source event and downstream effect; recovery/reconciliation.
8. Reversal/compensation preserves original history.
9. Effective-date and business-date boundary.
10. Cross-tenant and cross-branch isolation.

## 6. Approval boundary

This document is a candidate transition model. It does not close ODR items or authorize implementation of unapproved business semantics.
