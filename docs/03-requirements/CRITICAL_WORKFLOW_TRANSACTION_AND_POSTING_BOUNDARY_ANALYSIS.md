# Critical Workflow Transaction and Posting Boundary Analysis

**Document ID:** DBR-005  
**Status:** Decision-support analysis — recommended logical transaction contracts; storage/implementation decisions remain open  
**Date:** 2026-11-17  
**Authority reviewed:** ERS-001, FRS-001, NFR-001, ARC-CONC-001, ARC-EVT-001, ARC-TEN-001, ARC-IAM-001, ERR-001, end-to-end workflows, inventory ledger, milk collection/farmer settlement, billing/payments, accounting double-entry controls, Tally sync architecture and voucher mapping, and ODR-004/005/008/010/012/014/017/020/027/030/031.

## 1. Purpose and boundary

This document connects critical business workflows to their authoritative source events, local transaction boundaries, stock/subledger/journal effects, asynchronous work, idempotency and recovery behavior.

It is a logical transaction analysis, not a physical database design or implementation plan. It does not select SQL isolation levels, ORM patterns, database constraints, queue/broker, distributed locks, API contracts, code, SQL, DDL or migrations. ARC-CONC-001 and ARC-EVT-001 remain the architecture authorities for concurrency and asynchronous processing.

## 2. Cross-workflow invariants

1. Every material business effect has one authoritative source document/event with stable identity and tenant ownership.
2. Tenant and authorized branch/location scope are checked before reads and writes and are carried through jobs, events, reports, exports and integrations.
3. Ordinary employees remain limited to their assigned branch. Cross-branch transfer, reporting or management operations require the specific permission and scope; Tenant Owner all-branch visibility does not imply unrestricted mutation.
4. One business command must not create duplicate stock movements, fulfilments, receipts, settlements, billable allocations, journals or Tally vouchers when retried.
5. Domain uniqueness is required in addition to request idempotency.
6. A local transaction must atomically commit the business state changes that must succeed or fail together, including the outbox intent where used.
7. Do not keep a database transaction open while waiting for a bank, payment provider, notification provider, mobile device, Tally desktop agent or other network participant.
8. Where a workflow spans multiple commits or external systems, model it as a durable state machine with pending, completed, failed, compensation and reconciliation states.
9. Posted financial entries, stock movements, receipts, settlements and completed fulfilments are immutable; corrections use linked adjustments/reversals.
10. A source operational event, its stock/subledger effects and journal posting status must be traceable and reconcilable; a derived balance is not the authoritative evidence.
11. A failed notification, analytics refresh or Tally export must not undo an already committed operational transaction.
12. No design may claim exactly-once network delivery. The target is one logical business effect through idempotent processing and reconciliation.

## 3. Transaction vocabulary

| Term | Meaning |
|---|---|
| Source event/document | Authoritative business fact that initiates downstream effects |
| Local atomic commit | One bounded database transaction that commits a defined set of related state changes or none |
| Posting instruction | Versioned, validated instruction to create a stock/subledger/journal effect |
| Outbox event | Durable publication intent committed with the source transaction |
| Idempotency key | Stable key for retrying one intended command |
| Domain uniqueness | Business identity rule preventing two commands from creating the same logical event |
| Compensation/reversal | New linked action that counteracts an already committed effect |
| Reconciliation | Process to resolve gaps/unknown outcomes between source and downstream systems |

## 4. Critical workflow boundary matrix

| Workflow | Authoritative source event | Same local atomic commit should include | Separate/asynchronous or later stage | Key uniqueness / concurrency guard |
|---|---|---|---|---|
| POS sale | Confirmed sale command | Sale header/lines, price/tax snapshots, stock issue intent or ledger movement, tender/receivable records and audit; outbox intent if required | Receipt delivery, analytics, Tally export, provider/bank confirmation | Idempotency key; sale source identity; stock availability guard; tender total validation |
| Delivery dispatch | Authorized dispatch confirmation | Run/stop dispatch state, picked/issued stock movements and assignment snapshot | Mobile delivery outcomes, customer notification, later return/reconciliation | One active dispatch per applicable run/stop/line; stock reservation/issue concurrency |
| Delivery fulfilment | Accepted delivery outcome | Fulfilment record, actual delivered/returned quantities, source occurrence linkage, billable eligibility event and audit | Billing run, customer message, analytics, Tally | One finalized outcome per attempt/version; duplicate mobile sync idempotency; branch assignment |
| Milk collection | Recorded collection entry and acceptance decision | Collection entry/source measurements, accepted/rejected quantities, quality decision/version, stock receipt if immediate policy requires, payable-accrual source event/outbox | Rate/settlement batch where policy defers it, analytics, Tally export | Source device/event key plus duplicate review; collection session scope; no double allocation to settlement |
| Farmer settlement | Approved settlement run/version | Settlement header/lines, claims/allocations of eligible accepted collection entries, supplier payable subledger effect and audit | Actual payment, bank reconciliation, Tally export | Unique collection-entry allocation per payable quantity; locked/claimed source set; run idempotency |
| Packaged-goods receipt | Accepted goods receipt | Receipt/line facts, accepted quantity, stock movements and supplier payable/accrual source event according to invoice policy | Supplier statement match, payment, Tally export | Source receipt line uniqueness; partial receipt accounting; lot/location scope |
| Stock transfer | Approved transfer request and each physical milestone | Source dispatch issue and in-transit state as one commit; destination receipt and destination stock movement as a separate commit | Carrier/notification and discrepancy workflow | Transfer ID + line + milestone uniqueness; source availability lock/version; partial receipt and variance |
| Customer billing | Finalized billing run/version | Invoice/document, billable-item allocations, customer receivable subledger and journal/posting intent according to approved posting architecture | PDF/email delivery, analytics, Tally export | Each billable source item allocated once; run idempotency; effective price/tax snapshot |
| Customer/supplier receipt | Receipt record/confirmation as defined by payment policy | Receipt, receiving account/custody facts, allocation rows, subledger and journal intent where applicable | External verification, bank reconciliation, Tally export | Idempotency key; external reference duplicate review; allocation cannot exceed unapplied amount |
| Refund/payment | Approved refund command | Refund authorization/source link, payment-outflow record and journal intent | Provider execution/acknowledgement and bank reconciliation | Original receipt/refund limit; idempotency; unknown provider outcome reconciliation |
| Accounting journal | Approved posting instruction | Balanced journal header/lines, source-to-journal mapping, posting status and outbox intent if used | Tally synchronization and analytics | Unique source event + posting rule version + accounting scope, unless explicit split policy |
| Tally synchronization | Approved sync batch/item | SaaS sync item state, stable source reference, payload checksum and attempt record | Desktop agent sends to Tally; read-back/acknowledgement and reconciliation | Target company + source ID/version + export purpose; per-item state; do not blindly resend unknown outcome |

This matrix is a logical recommendation. The exact commit contents depend on the approved accounting and stock posting model and must be validated through the selected storage PoC.

## 5. Detailed critical transaction patterns

### 5.1 POS sale

Recommended flow:
1. Resolve tenant, branch, register, operator and permission scope.
2. Validate product, price/tax version, discount authority and stock availability.
3. Claim idempotency key and validate canonical request fingerprint.
4. In one bounded local transaction, persist the sale and lines, immutable commercial snapshots, required stock issue, tender or receivable facts, audit and durable outbox intent where asynchronous downstream work is needed.
5. Return a durable sale result only after commit.
6. Generate receipt/notification/analytics/Tally work asynchronously.
7. If external payment confirmation is required, keep payment state distinct from sale commit and reconcile uncertain outcomes.

A sale must not be considered successful merely because a client displayed a confirmation or a payment provider was called.

### 5.2 Milk collection and farmer settlement

Collection, acceptance, settlement calculation and actual payment are separate business facts:
- Collection preserves original measured quantity, unit, source evidence and collector/branch/session.
- Quality/acceptance decision records the policy version and authorized actor.
- Accepted stock receipt is posted once when the applicable workflow says it becomes usable stock.
- Payable accrual may occur at accepted collection or at an approved settlement stage; the accounting owner must select and document the recognition trigger.
- Settlement claims eligible accepted collection entries with line-level allocation; one accepted quantity cannot be settled twice.
- Payment is a separate event that reduces payable only when recorded under the approved payment/verification policy.
- Recalculation after finalization creates a linked adjustment/reversal; it does not rewrite source collection or payment history.

The exact payable recognition point, deductions, advances, rate formulas and settlement period remain ODR-010/017 decisions.

### 5.3 Stock transfer across branches

A branch transfer is not a sale or purchase and must be modelled as a multi-step custody workflow:
1. Authorize the initiating branch and destination branch/location.
2. Reserve or validate source stock against the applicable availability policy.
3. Commit source issue and in-transit quantity exactly once.
4. Record destination receipt separately, including accepted, rejected, damaged and variance quantities.
5. Close the transfer only when the configured quantity/variance conditions are met.
6. Create a controlled discrepancy/claim for missing or damaged quantity; do not rewrite the source dispatch.
7. Reconcile source and destination ledgers.

A branch employee must not gain general access to the destination branch's records merely because they initiated a transfer. The transfer-specific permission may expose only the information necessary for that transfer. Owner all-branch visibility remains governed by the separate all-branch scope grant.

### 5.4 Billing, receipts and journals

- Billing must allocate each eligible fulfilment/sale/adjustment source once.
- A billing run is versioned; rerun must return or continue the same logical run unless a controlled new version/revision is authorized.
- Receipt capture, verification, bank settlement, allocation and reconciliation are separate states/facts.
- Partial allocations must be concurrency-safe and cannot exceed the unallocated receipt or open item.
- Journal posting consumes a stable source event and posting-rule version, validates account/period/dimensions and exact balance, then commits the immutable journal and its source mapping.
- Reversals reference the original journal and preserve the original amount, period and reason.

The project has not yet approved all source-event accounting recognition rules or the final posting architecture.

## 6. Idempotency and domain uniqueness

A single idempotency key is not enough. Each critical workflow needs:
- A command key scoped to tenant, principal/service and operation.
- A canonical semantic fingerprint so the same key with different content is rejected.
- A domain uniqueness rule for the business event itself.
- A stable source ID/version for downstream posting and external sync.
- A durable result/status for unknown or in-progress outcomes.
- A retention policy long enough to prevent delayed retries from recreating a harmful effect.

Candidate business uniqueness dimensions include:
- POS sale: tenant + source command/register context + stable sale identity.
- Schedule fulfilment: subscription line/occurrence + attempt/version.
- Milk collection: collection session + source device/event identity + duplicate review attributes.
- Farmer settlement: accepted collection entry/quantity allocation + settlement version.
- Stock movement: source document/line + movement type + posting version.
- Billing: billable source item + billing allocation identity.
- Journal: source event + posting rule version + accounting entity/book scope + posting purpose.
- Tally voucher: target company + stable SaaS source identity/version + export purpose.

Exact keys and constraint forms are technology-specific and must be proven under concurrent execution.

## 7. Transactional outbox and asynchronous effects

Where a source commit must reliably trigger asynchronous work:
1. Commit authoritative business state and outbox intent in the same local transaction.
2. Publisher claims events with a lease/claim protocol.
3. Consumers deduplicate by stable event ID and apply their own idempotency/domain uniqueness.
4. Retries use bounded backoff and expose terminal/manual-resolution states.
5. Events carry tenant and required branch context, source identity/version, schema version and correlation/causation identity.
6. Consumers revalidate tenant status, branch scope and service authorization; internal origin does not make a payload trusted.
7. A worker must never reuse ambient context from another request/job.

Outbox publication may duplicate delivery. The design must not claim exactly-once delivery.

## 8. Subledger and general-ledger reconciliation

For every financial source class, define a traceable chain:

**Business source event → operational/subledger record → posting instruction/version → immutable journal → external accounting mapping (if exported).**

Required controls:
- Source and journal identities are stable and linked.
- Each posting rule defines expected debit/credit accounts, accounting scope, branch/cost-centre dimensions, tax treatment, rounding and reversal behavior.
- Subledger totals reconcile to the corresponding control account at an agreed period/cut-off.
- Missing, duplicate, unbalanced or out-of-period postings create visible exceptions.
- Corrections use a linked journal reversal/adjustment and repair the source-to-posting status without overwriting history.
- Customer receivable and supplier/farmer payable remain separate subledgers; no implicit netting.
- Stock ledger and inventory valuation are reconciled but remain distinct facts.

The exact accounting recognition rules, chart of accounts and branch/legal-entity dimensions remain owner/accountant decisions.

## 9. Tally synchronization boundary

Tally is an external accounting target, not the SaaS source of truth.

Recommended sequence:
1. A finalized, approved SaaS journal/source record becomes export-eligible under policy.
2. Create a versioned sync batch/item with tenant, accounting scope, target Tally company, source identity/version, schema/adapter version, idempotency key and checksum.
3. Resolve required masters/mappings before dependent vouchers.
4. Send through the separately installed desktop agent; do not hold the SaaS transaction open.
5. Persist per-item response and target identifiers where returned.
6. If the agent disconnects or acknowledgement is lost, query/reconcile target state using stable source identity and mapping before retry.
7. Retry only failed/retryable items; never resend a whole partially successful batch as new vouchers.
8. If the target cannot prove whether a voucher was created, place the item in an unknown-outcome/manual reconciliation state rather than blind resend.
9. A changed posted source creates a correction/reversal workflow, not silent mutation of an already exported voucher.

Tally release-specific unique identifiers, precision, import acknowledgement and read-back behavior require a version-specific PoC. Voucher number alone is not a safe universal idempotency key.

## 10. Branch authorization and data context

Every transaction boundary must carry:
- Tenant identity.
- Actual business branch/location where applicable.
- Actor/service identity and effective role/permission scope.
- Resource ownership and state.
- Idempotency/correlation identity.
- Required source and destination branch references for transfers.

For ordinary employees:
- Reads and writes remain restricted to their assigned branch.
- Cross-branch transfer visibility is limited to the specific authorized transfer records and actions.
- A report or export must apply the same branch scope as interactive access.
- Background jobs and events must not widen scope by using tenant-wide queries.
- Owner all-branch visibility is a separate explicit scope grant; mutation, approval, export and sensitive fields still require their own permissions.

## 11. Failure and recovery matrix

| Failure point | Required outcome |
|---|---|
| Crash before local commit | No business effect; same command may be retried |
| Commit succeeds but response is lost | Retrieve the durable result by idempotency key; do not create a second effect |
| Outbox send succeeds but delivery mark is lost | Duplicate delivery is safe; consumer deduplicates |
| Consumer commits effect but acknowledgement is lost | Redelivery returns existing logical result |
| Stock issue races with another issue | One valid outcome or explicit conflict; no oversell beyond policy |
| Two settlements claim the same collection | One allocation succeeds; the other conflicts or excludes already claimed quantity |
| Two billing runs claim one billable item | Unique allocation prevents duplicate billing |
| Receipt is allocated concurrently | Total allocations cannot exceed available amount |
| Journal posts while period closes | Serialized/versioned boundary; either valid posting or explicit closed-period rejection |
| Tally accepts voucher but acknowledgement is lost | Unknown outcome; read-back/reconciliation before retry |
| User loses branch access while a job is queued | Worker revalidates current scope; stale authorization must not be trusted |
| Partial transfer receipt | Keep in-transit and received/variance quantities explicit until resolved |

## 12. ERS and architecture refinement requirements

Before physical database design:
- Define source-event identities and source-to-effect cardinalities for every critical workflow.
- Define posting instruction and posting-rule version semantics.
- Define billable item allocation and farmer collection settlement allocation constraints.
- Define stock reservation, movement, transfer milestone and in-transit semantics.
- Define journal source uniqueness by accounting scope and split-posting exception.
- Define transaction boundaries and outbox contracts for each critical command.
- Define unknown external outcome and reconciliation queue entities/states.
- Define branch-scoped transfer permissions and owner all-branch visibility without broadening employee access.
- Map Tally sync batch/item, mapping, acknowledgement and reversal identities to ERS.
- Validate all critical uniqueness/concurrency rules with representative race-condition scenarios.

## 13. Open decisions and evidence dependencies

- ODR-004/005: membership, branch assignment, roles, scope grants, cross-branch mutation and approval matrix.
- ODR-008/010/012: UOM/precision, farmer payable recognition, inventory and transfer policy.
- ODR-014/016/017: billing/tax/accounting source events, legal document and journal rules.
- ODR-020: offline delivery outcome idempotency, revocation and conflict behavior.
- ODR-027/028/030/031: workload, storage/queue PoCs, technology, topology and recovery evidence.
- Tally release/company matrix and version-specific integration PoC.
- Accountant/owner approval of posting rules, chart-of-accounts templates and period-close behavior.

No ODR status is changed or closed.

## 14. Consistency review

- Builds on ARC-CONC-001 rather than replacing its idempotency, transaction and concurrency invariants.
- Builds on ARC-EVT-001's outbox and duplicate-delivery model; it does not claim exactly-once delivery.
- Consistent with ERR-001's retry/correction/reversal distinctions and recovery envelope.
- Consistent with stock movement and farmer settlement immutability, billing source allocation and accounting double-entry controls.
- Consistent with Tally's per-item batch status, master-before-voucher dependency and unknown-outcome reconciliation.
- Preserves branch-level isolation and the separate Tenant Owner all-branch visibility grant.
- Does not approve physical schema, storage technology or accounting policy.

## 15. Non-goals

- No application code, SQL, DDL, ORM models, migrations or database provisioning.
- No final physical transaction/locking implementation.
- No final chart of accounts, tax policy or journal posting-rule catalogue.
- No claim of distributed atomicity or exactly-once network delivery.
- No automatic cross-branch access for ordinary employees.

## 16. Exit criteria

The transaction architecture can move toward physical design only when:
1. Each critical workflow has approved source event, state machine and commit boundary.
2. Stock, subledger, journal and external voucher identities are traceable.
3. Domain uniqueness and idempotency rules are specified for concurrent retries.
4. Transfer, billing, receipt and settlement allocation invariants are testable.
5. Outbox, worker context, failure recovery and reconciliation responsibilities are defined.
6. Branch authorization tests cover ordinary staff, explicitly multi-branch users and Tenant Owner.
7. Accounting owner confirms posting recognition, books scope, dimensions and correction rules.
8. Technology PoCs prove the required atomicity, concurrency and recovery behavior.
