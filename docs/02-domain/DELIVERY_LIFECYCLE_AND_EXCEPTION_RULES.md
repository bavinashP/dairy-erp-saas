# Delivery Lifecycle and Exception Rules

**Status:** Working baseline — requirements discovery  
**Related open decisions:** ODR-002, ODR-004, ODR-006, ODR-007, ODR-008, ODR-013, ODR-014, ODR-019, ODR-020, ODR-021, ODR-022  
**Purpose:** Define operational delivery concepts before route optimization, mobile UX, or implementation design.

## 1. Scope and principles

Delivery includes recurring subscription supply, one-time orders, and other authorized dispatches. It must support owner-operated delivery, a small number of workers, and multi-branch teams.

1. A route template, delivery run, stop, attempt, and fulfilment are distinct concepts.
2. A planned quantity is not an actual delivered quantity.
3. Failed or partial delivery must remain visible and must not silently become successful.
4. Delivery evidence must be proportionate to business size and configured policy.
5. A delivery worker receives only assigned work and minimum necessary customer information.
6. Delivery mobile capability is a client/workflow choice, not a separate source of business truth.
7. Offline operation must be explicitly designed; clients must not silently overwrite concurrent server changes.
8. All quantity, status, assignment, and financial-impact corrections must be auditable.

## 2. Demand sources

A delivery demand line may originate from:
- Subscription schedule occurrence.
- One-time customer order.
- Staff-created authorized delivery request.
- Approved replacement or redelivery.
- Approved extra supply.

Every demand line must preserve its source reference and distinguish planned, allocated, dispatched, delivered, returned, and billable quantities.

## 3. Planning hierarchy

Conceptual planning objects:
- Service area: geography served by a branch.
- Route template: reusable stop ordering/grouping.
- Delivery plan: date/shift-specific plan.
- Delivery run: execution assignment for a date/shift/vehicle or worker.
- Delivery stop: one customer/address stop in a run.
- Delivery line: product and quantity for that stop.
- Assignment: worker/vehicle/device responsibility, effective for the run.
- Dispatch batch: grouped goods prepared and released.

Do not require vehicle or route optimization for a one-person dairy. Manual ordering and simple printed/mobile lists must remain viable.

## 4. Lifecycle (candidate)

| Stage | Meaning | Key controls |
|---|---|---|
| Demand Ready | Eligible demand occurrences/orders identified | Exclude cancelled, skipped, blocked or invalid demand |
| Planned | Demand grouped into route/run | Preserve source demand and planned quantities |
| Allocated | Available stock reserved | Avoid over-allocation; record shortages |
| Picked / Packed | Physical goods prepared | Scan/count/weight evidence according to tenant configuration |
| Dispatch Ready | Run checked and ready to leave | Validate assigned worker, quantities, and exceptions |
| Dispatched | Run has left/handed to delivery operator | Lock or version dispatch manifest |
| In Progress | Stops being attempted | Track timestamps and actor |
| Completed with Exceptions | All stops processed, some not fully successful | Every exception has reason and outcome |
| Closed | Run reviewed and operationally closed | Reconcile dispatched versus delivered/returned/undelivered |
| Cancelled | Run cancelled before or under controlled dispatch policy | Release reservations and record reason |

These are conceptual states; the final state machine must define allowed transitions and correction paths.

## 5. Stop and fulfilment outcomes

Candidate stop outcomes:
- Delivered in full.
- Partially delivered.
- Customer unavailable.
- Customer refused.
- Address/access issue.
- Product unavailable.
- Quality concern.
- Payment-related hold (only where approved policy allows).
- Delivery postponed/rescheduled.
- Wrong item/quantity delivered.
- Other reason with mandatory notes where configured.

For each delivery line record as applicable:
- Planned quantity and unit.
- Dispatched quantity.
- Delivered quantity.
- Returned quantity and condition.
- Undelivered quantity and reason.
- Substitution details.
- Time and responsible actor.
- Customer acknowledgement/evidence according to configured policy.
- Link to any adjustment, redelivery, credit, or complaint.

Do not assume signature, OTP, photo, GPS, or customer app is mandatory. These controls have privacy, device, cost, and usability implications and require a policy decision.

## 6. Milk and perishable product handling

Where relevant, delivery requirements must support:
- Product/pack identity and quantity verification.
- Batch/lot traceability when maintained upstream.
- Temperature/handling evidence where applicable.
- Expiry/quality hold checks.
- Return classification (saleable, quarantine, waste, supplier return).
- Separation of reusable containers/crates/deposits from product quantity.
- Route/run time and product handling constraints.

The product's nature and applicable law determine which controls are required. Do not impose manufacturing-grade traceability on every low-risk packaged resale workflow without a justified need.

## 7. Stock and financial effects

- Dispatch may move stock from a branch/warehouse to a route/worker custody location, depending on the selected inventory model.
- Successful fulfilment must produce the defined stock and billable-delivery effects exactly once.
- Failed delivery must not be billed as successful unless an explicitly approved charge policy applies.
- Returned goods require condition-based disposition and stock movement.
- Corrections after billing must create linked adjustment/credit/reversal events rather than rewrite posted history.
- Cash collected on delivery, if allowed, must be recorded as a receipt and reconciled separately.
- Delivery completion is not proof of payment or bank settlement.

## 8. Assignment and authorization

The system should support:
- Owner self-assignment and execution.
- One worker assigned to a run.
- Multiple workers or hand-off where operationally required.
- Branch-scoped delivery supervisors.
- Temporary assignment and expiry.
- Reassignment with actor, reason, and timestamp.
- Restricted customer data on assigned stops only.
- Supervisor exception review and approval limits.

A worker must not be able to change customer rates, subscription recurrence, financial balances, or unrelated branch data merely because those records appear in a delivery screen.

## 9. Offline and concurrency requirements

If offline delivery is supported, define:
- Data downloaded and expiry/refresh window.
- Device/user identity and local action audit.
- Unique client-generated operation identifiers.
- Server reconciliation and conflict policy.
- Duplicate submission protection.
- Revoked assignment/session handling.
- Sensitive data retention and remote wipe/logout behavior.
- Which actions are prohibited offline (e.g. high-risk financial adjustments) until synchronized.

Concurrent dispatch edits, worker updates, and supervisor corrections require optimistic versioning or equivalent conflict detection. Never silently use last-write-wins for quantity or financial-impact records.

## 10. Performance and operational reporting

Required operational views include:
- Planned versus dispatched versus delivered quantity.
- Stop completion and exception rates.
- Route/run duration and late stops.
- Shortage, return, wastage and redelivery.
- Worker/route performance with fair context.
- Branch/service-area comparisons.
- Customer complaints and repeat failure patterns.

Metrics must define numerator, denominator, time basis, and exclusion rules. Avoid ranking workers without considering route size, distance, product mix, and assigned conditions.

## 11. Acceptance scenarios

1. Owner creates and completes a simple run without creating an employee record.
2. Subscription and one-time order demands appear with source references.
3. Stock shortage is visible before dispatch.
4. Repeated dispatch command does not duplicate stock issue or delivery.
5. Worker sees only assigned run/stops and minimum required customer details.
6. Partial delivery records exact quantities and reason.
7. Customer refusal records undelivered quantity and return disposition.
8. Redelivery is linked to the original failed/partial stop.
9. Reassignment during an active run is audited and old assignment access is revoked.
10. Offline duplicate synchronization does not duplicate fulfilment or receipt.
11. Concurrent edits detect conflicts instead of silently overwriting.
12. Delivered, returned, and undelivered totals reconcile to dispatched quantities under defined unit rules.
13. A post-billing correction creates an auditable adjustment.
14. Run closure prevents unauthorized modification while preserving an approved correction path.

## 12. Open decisions

- Branch, service area, route and delivery territory model.
- Stock custody model during dispatch.
- Whether route templates/optimization are required in early releases.
- Required proof-of-delivery methods by tenant configuration.
- Offline scope and conflict policy.
- Cut-off and late change rules.
- Redelivery and failed-delivery billing policy.
- Reusable container/deposit handling.
- Delivery workforce and vehicle/asset integration.
- Delivery notification channels and customer consent.

This document does not approve mandatory customer login, GPS tracking, signature/OTP proof, or a particular route-optimization provider.
