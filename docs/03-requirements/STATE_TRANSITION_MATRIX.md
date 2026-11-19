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
| Draft | Plan → Planned | Branch, Route ownership, effective ServiceArea coverage, demand and stop allocations valid | Preserve source demand and route/plan version |
| Planned | Assign → Assigned | Accountable run assignee/participants and transport resource where used are current, scoped and available | Assignment version and workforce-policy evidence retained |
| Assigned | Validate/lock → Dispatch Ready | Stock, quantities, route constraints, required vehicle/driver evidence, custody and product-handling checks pass | Freeze candidate manifest/assignment version |
| Dispatch Ready | Dispatch → In Progress | Exact dispatch permission, current run/manifest version, all readiness guards and any required independent approval | Idempotent dispatch; snapshot route, demand, personnel, resources, custody and policy versions |
| In Progress | Hold → Held / Exception Review | Safety, route, vehicle, participant, device or custody exception; reason/evidence | Preserve completed stops, actual custody, uncompleted work and current resource assignments |
| Held / Exception Review | Reassign/Resume → Assigned / Dispatch Ready / In Progress | Current permission, valid replacement assignment/resource, capacity/readiness, custody handover where goods change hands and conflict-free version | Original assignment/dispatch remains immutable; append replacement events |
| In Progress | Complete → Completed | All required stops have outcomes; delivered/returned/undelivered and custody quantities reconcile | No unresolved material stock/cash/custody exceptions |
| In Progress | Close with exceptions → Completed with Exceptions | Exact close-with-exceptions permission; each unresolved item has reason, owner and resolution path | Preserve outstanding stock, cash, custody and customer obligations |
| Completed states | Correct/Reopen → Controlled Exception Review | Exact correction permission, reason, current version and linked evidence | Original run/manifest/fulfilment remains visible; correction is compensating |
| Any pre-dispatch state | Cancel → Cancelled | Permission, cancellation reason and no irreversible effect already committed | Release demand/stock reservations and assignments safely |
| Dispatched/In Progress | Cancel/Abandon → Controlled Exception Review | Authorized exception decision and full custody/stock reconciliation | Never erase dispatch or fabricate an automatic return/rollback |

### Run assignment lifecycle

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Proposed | Assign → Active | Named principal/transport resource, run scope, effective period and explicit assignment permission valid | Duty label does not grant permission |
| Active | Replace/Release → Superseded / Released | Authorized actor, effective time, reason and current assignment version | Preserve old assignment and link replacement |
| Active | Expire/Revoke → Expired / Revoked | Expiry or permission/assignment revocation | Block future actions immediately; physical custody remains until actual handover/resolution |
| Active | Conflict → Held | Vehicle/resource exclusivity, personnel conflict or readiness check fails | No double booking or silent override |

**Selected baseline:** offline delivery scope and server revalidation follow ODR-020; route/assignment/dispatch baseline follows ODR-002 section 16.37.

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

**Selected baseline:** physical custody and handover follow DOM-CUST-001 and ODR-002 section 16.36. Lot rules, count cut-off, negative stock and valuation remain under ODR-008/011/012/017.

**Custody handover state model (ODR-002 section 16.36 / DOM-CUST-001)**

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Draft | Prepare/Offer → Offered | Subject, parties, locations, quantity/condition expectation and permission valid | Freeze the applicable custody profile/version for the handover |
| Offered | Dispatch/Hand over → Dispatched / Handover Pending | Actual handler, source custodian, event time and required dispatch evidence captured | Custody changes only to the party that explicitly accepts it under the profile |
| Dispatched | Transit → In Transit | Carrier/vehicle/container and transit evidence where applicable | Preserve last confirmed custody party and location; record each subsequent handover separately |
| Dispatched / In Transit | Receive → Received Pending Verification | Receiver identity, actual quantity/condition and required receipt evidence | Does not update destination stock until separate stock receipt policy accepts quantity |
| Received Pending Verification | Accept → Accepted / Accepted with Discrepancy | Required acknowledgement and validation complete | Completes custody only for accepted subjects/quantities |
| Received Pending Verification | Reject/Dispute → Rejected / Disputed | Reason, observed evidence and actual current custodian/location captured | No assumed back-transfer or automatic stock write-off |
| Any pending/disputed state | Resolve → Closed / Exceptioned | Authorized resolver, evidence, reason and current custody outcome recorded | Administrative resolution is not presented as another party's acknowledgement |
| Any active handover | Cancel/Correct → Controlled Exception Review | Permission, current state/version and evidence | Original dispatch/receipt events remain immutable; corrections are linked |

A timeout, missed notification, disconnected device or silent receiver never transitions a handover to Accepted. The last verified custodian/location remains in force until a real handover or controlled evidence-backed resolution is recorded.

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

### 3.11 Cross-Branch StockTaskAccessGrant authorization lifecycle

The authorization state of a grant version is distinct from the operational state of any stock task that references it. A grant version never authorizes an action unless all independent membership, permission, scope, limit and workforce-policy conditions are valid at commit time.

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Draft | Submit → Submitted | Named worker, stock-owning Branch, OperatingLocation/StockLocation, action/task/item scope, limits, effective period, reason and requester permission valid | Freeze immutable version/payload hash; no authority granted |
| Submitted | Validate → Pending Approval | Worker membership/account, independent permission eligibility, target stock ownership and required workforce assignment checked | Route to stock-owning-Branch approval workflow; no authority granted |
| Pending Approval | Approve → Approved / Scheduled | Exact version/hash approved by authorized stock-owning-Branch user or currently valid delegate; all required approval stages complete | Record ApprovalAction and approval-time authority evidence; no authority before effective start |
| Pending Approval | Reject → Rejected | Authorized reviewer, reason and exact version | Terminal outcome for that version; new version/request required for reconsideration |
| Draft / Submitted / Pending Approval | Withdraw → Withdrawn | Requester still has withdrawal permission, or authorized reviewer cancels the pending request | No stock authority; retain reason/history |
| Approved / Scheduled | Effective start reached → Active | Exact approval remains valid; worker account/Tenant membership, independent action permissions, stock ownership and required workforce assignment are current | Effective start inclusive; grant permits only exact approved envelope |
| Active | Material amendment → New Submitted Version | Any worker/Branch/location/action/task/item/limit/period change | Existing version remains unchanged; amended scope cannot be used before fresh approval |
| Active | Approved successor becomes effective → Superseded | Successor version approved before current version's effective end; successor scope valid | Predecessor stops authorizing at successor effective start; no ambiguous overlap |
| Active / Approved / Scheduled | Revoke → Revoked | Dedicated, scoped grant-revocation authority and recorded reason | Immediate denial of new tasks and further stock mutations; linked in-progress tasks held |
| Active / Approved / Scheduled | Effective end reached → Expired | Authoritative time reaches exact effective end | End exclusive; immediate denial with zero grace; no effect from pending extension/notice delivery |
| Active / Approved / Scheduled | Prerequisite lost → Invalidated | Worker account/membership, independent permission, required workforce assignment, target Branch or approved stock/location relationship ceases to be valid | Immediate denial; record exact invalidation cause; new eligibility and fresh approval required |
| Rejected / Withdrawn / Superseded / Expired / Revoked / Invalidated | View history → No authorization change | Read permission and audit scope valid | Terminal authorization history is immutable; reissue requires a new version/request |

A continuous extension is a successor version approved and committed before the current version expires. The successor becomes effective no later than the prior version's effective end; at that instant the prior version is Superseded. If approval is still pending at expiry, the previous version expires normally. A later approval cannot retroactively authorize a gap.

The approver's later loss of access does not retroactively erase a valid committed approval. If the approval is suspected to have been invalid when committed, an authorized reviewer must investigate and explicitly revoke/invalidate; preserve the original approval evidence.

### 3.12 StockTask hold and reconciliation lifecycle after grant end

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Open / In Progress | Grant revoked/expired/invalidated → Held for Review | Task references the ended grant version and has outstanding work or physical custody | Block new tasks and stock-affecting transitions; preserve committed steps, stock movements, quantities, custody, evidence and outstanding work |
| Held for Review | Review → Reauthorization Required | Same named worker remains eligible and task can safely continue | Further stock mutation remains blocked until an active approved grant covers exact remaining task/action/scope |
| Held for Review | Review → Reassignment Required | Original worker is no longer eligible, unavailable or not selected to continue | Do not transfer the grant; replacement worker needs a new grant and any required workforce assignment |
| Reauthorization Required | Authorize → In Progress | Current approved grant/version for same worker, exact remaining work, independent permission and required workforce assignment are valid | Link new grant version to remaining task; preserve old hold and grant history |
| Reassignment Required | Reassign → In Progress | New StockTaskAccessGrant for named replacement worker is approved; independent permission/workforce assignment valid; real custody handover recorded where goods change hands | Record old/new worker, custody handover, scope and authority; never transfer the old grant |
| Held for Review | Reconcile / Close → Reconciled / Closed | Exact stock-owning-Branch review/reconciliation permission, reason, physical custody and ledger outcomes documented | Use linked correction/compensation records; original movements/events remain immutable |
| Held for Review | Escalate → Escalated / Incident Review | Custody, quantity, location, condition or source facts cannot be verified | Preserve unresolved variance/incident; do not close merely to clear a queue |
| Reconciled / Closed | Reopen → Controlled Exception Review | New evidence and exact reopen permission | New linked reconciliation event; never erase prior closure/history |

A notification, task reassignment request, pending grant approval or offline command does not change a held task's authorization state. Non-mutating safety/incident reporting and evidence capture may continue only under separate valid permissions. No automatic completion, rollback, stock write-off, custody transfer or balance adjustment is implied.

### 3.13 ApprovalPolicyProfile and ApprovalPolicyVersion lifecycle

An ApprovalPolicyProfile is Tenant-owned configuration. It is not a user class, permission bundle or authority. Every material configuration is stored as a new ApprovalPolicyVersion; published versions are immutable and effective-dated.

| Current state | Command / next state | Guards | Side effects / restrictions |
|---|---|---|---|
| Draft | Submit → Submitted | Exact scoped policy.configure permission; rule keys registered; units/scopes valid; policy payload/version frozen | No business approval decisions use the draft |
| Submitted | Classify → Ready to Publish / Independent Review Required | Compare with active policy and fixed safeguards; determine whether controls are tightened, unchanged or weakened | Store impact analysis, changed rules and policy hash; a weakening change cannot publish yet |
| Independent Review Required | Approve → Approved | Separate authorized reviewer with approval.policy.approve; reviewer differs from policy initiator; exact payload/version unchanged | Bind approval to immutable version and weakening classification |
| Ready to Publish / Approved | Publish → Scheduled / Active | Exact approval where required; approval.policy.publish permission; valid effective period; no fixed safeguard weakened | Future versions have no effect before start; publish and activation are audited |
| Scheduled | Effective start → Active | Policy version current, scope valid and no conflicting version | Preserve predecessor and policy-resolution history |
| Active | Replace → Superseded | Approved/publishable successor with valid transition time; scope and precedence unambiguous | Historical requests retain their matched version/evaluation snapshot |
| Submitted / Independent Review Required | Reject → Rejected | Exact approval.policy.reject permission, reviewer scope and recorded reason | Immutable rejected version; a new candidate version is required for reconsideration |
| Draft / Submitted / Independent Review Required | Withdraw → Withdrawn | Exact approval.policy.withdraw permission, requester/reviewer scope and reason | Terminal withdrawn version; no policy decision authority was granted |
| Active / Scheduled | Retire → Retired | Exact approval.policy.retire or governed publish authority; no unhandled weakening/gap; independent approval if retirement weakens control | Historical decisions remain; effective replacement or default fixed policy is validated before retirement |

**Policy change classification**
- A tightening-only version (lower threshold, more reviewers/stages/evidence, narrower scope, shorter validity) may be published by an authorized principal with exact configure/publish permissions where no stricter policy requires independent review.
- A weakening version (higher threshold, fewer reviewers/stages/evidence, broader scope, relaxed maker-checker/independence, wider export/data limits or newly delegable action) requires independent approval by a different authorized principal with the exact approval.policy.approve permission. The initiator cannot approve their own weakening change.
- No policy version can weaken a fixed platform/domain invariant, non-delegable action or required stock-owning-Branch grant approval.

### 3.14 ApprovalRequest policy re-evaluation while in flight

| Current request state | Effective policy change | Next state / command | Required outcome |
|---|---|---|---|
| Pending / Partially Approved | New policy adds stricter conditions | Re-evaluate → Additional Review Required | Link new evaluation snapshot; invalidate/repeat affected completed stages; add reviewers/evidence before commit |
| Pending / Partially Approved | New policy is less restrictive | Continue under stricter original snapshot | Do not remove existing stages, reduce reviewer count or lower required evidence for the pending request |
| Pending / Approved but not committed | Policy conflict, missing version or incomparable thresholds/scopes | Hold → Policy Conflict Review | Deny protected commit until authorized policy correction/review resolves the conflict |
| Pending / Approved but not committed | Current stricter policy now applies | Re-evaluate → Additional Review Required | Current policy must be satisfied at commit; prior snapshot remains immutable and new evaluation is linked |
| Committed | Later policy change | No retroactive state change | Preserve the committed decision/business effect and policy evidence; corrections use controlled domain workflows, not policy rewrite |

Every ApprovalAction and protected commit must be bound to the exact source payload/version and the policy evaluation snapshot that authorized it. The authoritative commit revalidates current permissions, policy versions, threshold units, reviewer/quorum and fixed safeguards. A stale policy cache or an old approval cannot authorize a commit after a stricter applicable policy becomes effective.

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
3. Permission denied for missing/invalid permission or resource scope.
4. Missing/invalid required evidence or reason.
5. Duplicate command/retry idempotency.
6. Stale version/concurrent update conflict.
7. Failure between source event and downstream effect; recovery/reconciliation.
8. Reversal/compensation preserves original history.
9. Effective-date and business-date boundary.
10. Cross-tenant and cross-branch isolation.

## 6. Approval boundary

This document is a candidate transition model. It does not close ODR items or authorize implementation of unapproved business semantics.
