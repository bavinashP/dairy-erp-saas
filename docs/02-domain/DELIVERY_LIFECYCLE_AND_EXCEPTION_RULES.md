# Delivery Lifecycle and Exception Rules

**Status:** Recommended delivery baseline selected under the product owner's delegated decision mandate — optional proof-of-delivery and route capabilities; fixed fulfilment, custody, audit and authorization controls  
**Related decisions:** ODR-002, ODR-004, ODR-006, ODR-007, ODR-008, ODR-013–015, ODR-019–022; ODR-002 sections 16.36–16.37; see the Open Decisions Register for the current disposition.  
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
- Delivery plan: Branch/date/shift-specific demand and dispatch plan that may group one or more DeliveryRuns.
- Delivery run: a Branch-owned route/route-plan execution for a date/shift; personnel and vehicle assignments are separate, optional where appropriate, time-bounded records.
- Delivery stop: one customer/address stop in a run.
- Delivery line: product and quantity for that stop.
- Personnel assignment: named participant and operational duty for the run, effective-dated; the accountable run assignee is distinct from the person who plans or dispatches.
- Transport-resource assignment: optional vehicle/carrier/transport resource assigned to the run or a defined run segment; it is not a personnel permission.
- Dispatch batch: grouped goods prepared and released.

Do not require a registered vehicle, fleet module, reusable route template or route optimization for a one-person dairy. A simple Branch-owned one-off manual route/stop sequence, owner self-assignment and printed/mobile list must remain viable.

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

The system supports:
- Owner self-assignment and execution without an employee record.
- One accountable run assignee and one or more additional participants; the same person may perform multiple duties in a small business.
- Driver, delivery handler, helper and accountable run assignee are operational duty labels, not permission roles or authority classes.
- A participant must have an active identity, the exact effective permission/scope and a valid run assignment. Where Tenant workforce policy requires a shift/duty assignment, that is separately authorized.
- Assignment, reassignment, expiry and revocation are effective-dated and permission-controlled. The previous participant loses access to further work; any goods physically held remain in that party's custody until an evidenced handover or controlled resolution.
- A delivery participant's access to customer/contact/address data is limited to assigned run/stops and minimum necessary fields.
- Vehicle/transport resource assignment is optional, separate from personnel assignment and validated for availability, capacity, product compatibility and readiness where these are tracked/configured.
- A run may have sequential vehicle/resource assignments during its lifecycle. If a plan is split across concurrently operating vehicles, use separate runs under the same DeliveryPlan with non-overlapping stop/quantity allocation.
- Cross-Branch run work requires explicit run/Branch permission and any workforce assignment required by Tenant policy. Home/Primary Branch or a stock-task grant does not authorize delivery work.
- Planning, assigning, dispatching, executing, exception review and closing are distinct actions/permissions. Independent approval is required only where fixed safeguards or configured risk/threshold policy requires it; do not impose an enterprise maker-checker ceremony on every small operator.

A delivery participant must not change customer rates, subscription recurrence, financial balances, or unrelated Branch data merely because those records appear in a delivery screen.

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

Concurrent dispatch edits, personnel/resource assignment changes and authorized corrections require optimistic versioning or equivalent conflict detection. Never silently use last-write-wins for quantity, custody, assignment or financial-impact records.

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
- Route templates and automated optimization are optional. A one-off Branch-owned manual route/stop sequence is sufficient for an owner-operated or small business; manual route ordering/dispatch remains a complete valid workflow. Optimization may be introduced when route volume and business benefit justify it.
- A DeliveryPlan may group multiple DeliveryRuns. A run preserves the Route/plan version, manifest and assignment snapshot at dispatch. Concurrent vehicle splits use separate runs with unique stop/quantity allocations; one run may have sequential resource changes for breakdown/replacement.
- Stock dispatch is a custody hand-off to an explicitly assigned run/worker/vehicle/custody position. Ownership remains with the owning Branch until a distinct authorized transfer is posted. Handover, return, shortage, damage and discrepancy are recorded separately.
- Basic proof of delivery is mandatory at the transaction level: actor, time, run/stop, quantities, result and exception reason. Additional customer acknowledgement, PIN/OTP, photo, signature, sensor or location evidence is Tenant/workflow-configurable and permission-controlled; none is universally mandatory by default unless a verified applicable rule requires it.
- Vehicle, driver and carrier assignments are separate effective-dated records. Vehicle registration/fleet setup is optional; capacity, hygiene, temperature capability, maintenance and qualification evidence are validated only where the Tenant tracks them or applicable policy requires them.
- Dispatch, route/stop reassignment, personnel/vehicle replacement and run close are separately permissioned. Changes after dispatch preserve the original manifest and use explicit versioned exception/reassignment events.
- The first offline workflow is limited to assigned run snapshots and delivery outcome capture, with expiry, encryption, idempotency, conflict visibility and server revalidation under ODR-020. No offline financial posting, payment settlement or authoritative stock posting is implied.
- Subscription cut-offs and future schedule changes follow ODR-007/section 16.11. Already dispatched or completed work is not silently changed.
- Failed or partial delivery is not billed as full successful fulfilment. Redelivery references the original exception and is a distinct fulfilment; any failed-delivery fee, charge or credit must be explicitly configured and authorized, never assumed.
- Returnable cans/crates/bags are tracked as reusable custody/returnable-container records with deposit/condition where needed, not mixed with saleable milk quantity. Vehicle/asset maintenance integration is optional.
- Customer delivery communication uses the provider-neutral notification policy in ODR-021; opt-in/consent and recipient permissions are verified before external channels.

### 12.2 Remaining evidence dependencies

- Numeric route-optimization thresholds, offline duration/device support, cache expiry and client support windows require telemetry, field trials and ODR-019/020 evidence.
- Customer acknowledgement and location/photo retention/legal applicability require post-project privacy/legal review; the product design must preserve optionality and minimization.
- Exact vehicle/fleet/returnable-container accounting depends on the selected Asset and accounting workflows (ODR-017/029). These are not blockers for the core run/stop/fulfilment model.
- Driver qualification, vehicle fitness/insurance/permit evidence, exact statutory document categories and carrier liability require current jurisdiction-specific review; this domain does not hardcode unverified legal checklists.
- Delivery notification provider/channel pricing remains ODR-021 provider selection.

No customer login, continuously tracked GPS, signature, OTP, or route-optimization vendor is mandatory by this domain baseline.


## 13. Route execution, personnel/resource assignment and dispatch accountability

### 13.1 Route, plan and run separation

A Route is a Branch-owned reusable route or one-off manual sequence. A DeliveryPlan groups demand, date/shift and one or more DeliveryRuns. A DeliveryRun is one actual execution unit for one Branch and one Branch-owned Route/route plan. These concepts must not be collapsed into a single editable schedule.

- Every Route belongs to exactly one Branch and covers only ServiceAreas effectively assigned to that Branch.
- Every DeliveryRun references a Branch-owned Route/route plan whose owner matches the run Branch. A small-business user can create the route as part of run planning; a saved RouteTemplate is not mandatory.
- Route order, service area, run date/shift, delivery window, stop/service-time estimates and constraints are versioned. Completed/dispatched runs retain the applied version.
- The same template may be reused on different days/shifts. A new run receives a new manifest and assignments.
- A plan may split demand across several runs. Each source demand line/stop quantity must have a uniquely traceable allocation; competing runs cannot deliver the same allocated quantity twice.
- A route optimizer is optional and advisory by default. A suggested route must respect Branch ownership, ServiceArea assignment, capacity, product-handling, time-window, workforce and customer constraints. It cannot silently alter an approved/dispatched manifest.

### 13.2 Run personnel assignments

A run records one accountable run assignee/operator and may record multiple participants. In a solo business this can be the owner alone and does not require a separate employee profile.

A participant assignment records the person/principal, functional duty, run/stop scope, effective period and assignment authority. Duty labels such as driver, delivery handler or helper are operational descriptors only. Authorization remains based on current explicit permissions and resource scope. A participant cannot gain permission merely by being assigned as driver, helper or run assignee.

Workforce shift/duty assignment remains separate and is required where the Tenant's active workforce policy requires it. Home/Primary Branch does not grant run access. A cross-Branch worker must have explicit Branch/run permission plus any required workforce assignment. A StockTaskAccessGrant is not a delivery-run assignment or delivery permission.

### 13.3 Vehicle/transport resource assignments

A vehicle is optional. Walking, cycling, customer pickup, owner-operated transport, contracted transport or other supported delivery methods may be used without a registered fleet record.

Where tracked, a vehicle may be an Asset or an explicitly referenced external transport resource. Assignment is time-bounded and linked to the DeliveryRun or run segment. One primary transport resource is active for an indivisible run segment at a time. A vehicle breakdown/replacement closes or releases the old assignment and creates a new assignment; it does not overwrite history. Concurrent multi-vehicle execution is modelled as separate DeliveryRuns under one plan with non-overlapping stop/quantity sets.

Tenant-configured constraints may include vehicle capacity by mass/volume/container count; compatible product/temperature equipment; vehicle availability and maintenance downtime; cleaning/sanitation/inspection status; operator/driver qualification evidence; and route/service time constraints. A small business may keep this lightweight. Where an active policy or verified rule marks an item mandatory, the dispatch validation cannot bypass it.

The same vehicle/transport resource must not be double-booked over overlapping exclusive assignment windows. If shared/concurrent use is actually supported, the resource model must explicitly define that capacity and the applicable collision checks.

### 13.4 Dispatch authorization and readiness

Dispatch is a distinct action from planning, assigning and executing. Before dispatch, the system validates:
1. Run Branch, Route owner and effective ServiceArea coverage.
2. Current plan/route/manifest version and unique source-demand/stop allocations.
3. Product and quantity allocation, stock availability, picked quantity and any required custody handover.
4. Active participant identities, exact permissions/scopes, assignment windows and required workforce assignment.
5. Vehicle/resource availability and capacity where a resource is used.
6. Configured/mandatory qualification, inspection, cleaning, sanitation, calibration and product-handling evidence.
7. Required temperature/condition/packaging profile and unresolved hold/incident states.
8. Dispatch authority, any required independent approval and idempotent dispatch command identity.

Planning, assignment, dispatch, execution, stop outcome, exception resolution and closure each have separate permissions. A small owner-operated Tenant may assign these actions to one authorized user where the policy allows it. A Tenant may require independent approval by risk category or threshold. Tenant configuration cannot remove fixed authorization, ownership, integrity, custody or verified mandatory safety controls.

Dispatch freezes or versions the route, manifest, stop/quantity allocation, participants, transport resource and custody context. Any change after dispatch must create a linked exception/reassignment event with reason, actual actor, effective permission, timestamp and approval if required.

### 13.5 Execution exceptions and safe replacement

Driver absence, participant disablement, vehicle breakdown, road/access blockage, temperature excursion, vehicle cleanliness failure, route closure, customer safety issue or device outage must produce a visible hold/exception.

A replacement participant/vehicle must be revalidated against current permissions, workforce policy, resource availability and mandatory qualifications. When goods physically change hands, record the actual custody handover under DOM-CUST-001. When the worker still physically holds goods after permission revocation, preserve that custody and hold further stock-affecting work until valid handover/reassignment; never infer a transfer or complete the run automatically.

Post-dispatch stop changes, reordering, route deviation, split, merge or cancellation must preserve the original plan and record the actual execution path. A new run may be created for an authorized split/continuation; do not reuse completed stop allocation or duplicate source fulfilment.

### 13.6 Product safety, transport and traceability evidence

For a relevant perishable product, the effective product/process handling profile determines suitable transport conditions and the evidence required. Depending on the product and applicable rules, the profile may cover clean/covered/hygienic food-suitable vehicle, prescribed temperature, food-grade packaging, protection from dust/moisture/pests/damage/cross-contamination, loading/unloading care, lot/container identification and consignment traceability.

FSSAI's advisory dated 15 October 2026 specifically addresses the perishable food commodities described in that advisory (including examples such as khoa and paneer). It directs relevant Food Business Operators to use clean, covered and hygienic suitable conveyances, maintain prescribed temperatures, use food-grade packaging, protect products during storage/loading/unloading/transport and maintain consignment records/traceability. Apply according to the advisory's actual product/activity scope and current legal applicability; do not impose one universal temperature or checklist on every SKU.

FSSAI's 2019 Milk and Milk Products FSMS guidance and NDDB material provide practical raw-milk handling examples, including clean collection/transport containers and equipment, chilling and insulated tanker/can transport, receipt checks and equipment cleaning. The guidance's raw-milk parameters must be treated as source-specific guidance and validated for the actual process and current rules, not copied as universal statutory values for all dairy products.

Exact readings, thresholds, timing, calibration evidence and retention are effective-dated and source-backed. Preserve raw measurements and distinguish manual observations from verified sensor/device evidence. A temperature/condition exception follows the configured hold/quarantine/retest/review path; it does not automatically write off stock, reject a supplier, deduct a worker's pay or post a financial loss.

