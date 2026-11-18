# Delivery Lifecycle and Exception Rules

**Status:** Recommended delivery baseline selected under the product owner's delegated decision mandate — optional proof-of-delivery and route capabilities; fixed fulfilment, custody, audit and authorization controls  
**Related decisions:** ODR-002, ODR-004, ODR-006, ODR-007, ODR-008, ODR-013–015, ODR-019–022, ODR-037; see the Open Decisions Register for the current disposition.  
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

These are the recommended domain states. The detailed implementation state machine must preserve allowed transitions, permission checks, versioning and correction paths described here and in the State Transition and Exception/Reversal Matrices.

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

The default proof-of-delivery evidence is the authenticated actor/device, run/stop reference, event timestamp, actual quantities, outcome and reason. Customer acknowledgement, OTP/PIN, signature, photo, GPS/location evidence or a customer application are optional policy-controlled evidence methods, selectable by Tenant and workflow risk. Do not require all methods universally. Any location/photo/identity capture must be purpose-limited, permission-controlled, retained under the applicable data policy and reviewed for privacy/legal applicability before production use. A Tenant may strengthen evidence but cannot remove the minimum transaction/audit facts.

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

- Dispatch records an explicit custody hand-off from the source StockLocation to the DeliveryRun's assigned custody context (worker, vehicle, route bag/container or other configured holding position). The stock remains owned by the source Branch unless a separate valid ownership-transfer transaction says otherwise. A worker or vehicle is not automatically a StockLocation; a controlled vehicle/custody location is configured explicitly where inventory is physically held.
- Successful fulfilment must produce the defined stock and billable-delivery effects exactly once.
- Failed delivery must not be billed as successful unless an explicitly approved charge policy applies.
- Returned goods require condition-based disposition and stock movement.
- Corrections after billing must create linked adjustment/credit/reversal events rather than rewrite posted history.
- Cash collected on delivery, if allowed, must be recorded as a receipt and reconciled separately.
- Delivery completion is not proof of payment or bank settlement.

## 8. Assignment and authorization

The system should support:
- Owner self-assignment and execution without an employee record.
- One or multiple named workers assigned to a run; hand-off is a separate custody/assignment event.
- Assignment, reassignment and expiry are effective-dated and permission-controlled.
- Access to customer/contact/address data is limited to assigned run/stops and minimum necessary fields; reassignment/revocation invalidates the previous worker's access.
- Exception review, redelivery, refund/credit or charge approval are separate permissions and workflows; no supervisor/job title has authority by name.

A worker must not be able to change customer rates, subscription recurrence, financial balances, or unrelated branch data merely because those records appear in a delivery screen.

## 9. Offline and concurrency requirements

The initial offline baseline under ODR-020 permits a limited, encrypted and expiring snapshot of assigned delivery-run work and local capture of delivery outcomes as queued commands. The server remains authoritative: every queued command is revalidated against current Tenant membership, effective permission, run assignment, task/stock context, version, time window and resource state before it has business effect. A local event is not a completed stock issue, billing post or payment settlement. Before expanding offline scope, define:
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

### 12.1 Selected delivery policy baseline

- Branch, ServiceArea and Route ownership boundaries are resolved under ODR-002: each Route belongs to one Branch; it may serve only ServiceAreas assigned to that Branch; each DeliveryRun uses a Route owned by its own Branch. Multiple Branches serving the same ServiceArea use separately owned Routes.
- Route templates and automated optimization are optional. Manual route ordering/dispatch remains a complete valid workflow for one-person and small businesses. Optimization may be introduced when route volume and business benefit justify it.
- Stock dispatch is a custody hand-off to an explicitly assigned run/worker/vehicle/custody position. Ownership remains with the owning Branch until a distinct authorized transfer is posted. Handover, return, shortage, damage and discrepancy are recorded separately.
- Basic proof of delivery is mandatory at the transaction level: actor, time, run/stop, quantities, result and exception reason. Additional customer acknowledgement, PIN/OTP, photo, signature or location evidence is Tenant/workflow-configurable and permission-controlled; none is universally mandatory by default.
- The first offline workflow is limited to assigned run snapshots and delivery outcome capture, with expiry, encryption, idempotency, conflict visibility and server revalidation under ODR-020. No offline financial posting, payment settlement or authoritative stock posting is implied.
- Subscription cut-offs and future schedule changes follow ODR-007/section 16.11. Already dispatched or completed work is not silently changed.
- Failed or partial delivery is not billed as full successful fulfilment. Redelivery references the original exception and is a distinct fulfilment; any failed-delivery fee, charge or credit must be explicitly configured and authorized, never assumed.
- Returnable cans/crates/bags are tracked as reusable custody/returnable-container records with deposit/condition where needed, not mixed with saleable milk quantity. Vehicle/asset maintenance integration is optional.
- Customer delivery communication uses the provider-neutral notification policy in ODR-021; opt-in/consent and recipient permissions are verified before external channels.

### 12.2 Remaining evidence dependencies

- Numeric route-optimization thresholds, offline duration/device support, cache expiry and client support windows require telemetry, field trials and ODR-019/020 evidence.
- Customer acknowledgement and location/photo retention/legal applicability require post-project privacy/legal review; the product design must preserve optionality and minimization.
- Exact vehicle/fleet/returnable-container accounting depends on the selected Asset and accounting workflows (ODR-017/029). These are not blockers for the core run/stop/fulfilment model.
- Delivery notification provider/channel pricing remains ODR-021 provider selection.

No customer login, continuously tracked GPS, signature, OTP, or route-optimization vendor is mandatory by this domain baseline.
