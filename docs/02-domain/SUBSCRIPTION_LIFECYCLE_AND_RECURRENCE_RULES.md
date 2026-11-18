# Subscription Lifecycle and Recurrence Rules

**Status:** Recommended domain baseline — ODR-006/007 lifecycle and recurrence policy selected; Tenant-specific cut-offs and commercial choices remain configurable  
**Related open decisions:** ODR-006, ODR-007, ODR-008, ODR-013, ODR-014, ODR-020, ODR-021  
**Scope:** Dairy customer recurring supply; not the SaaS tenant's commercial subscription.

## 1. Purpose and invariants

The subscription capability must support recurring delivery of milk and other products to customers, including small owner-operated dairies and multi-branch businesses.

Core invariants:
1. A subscription is an agreement/configuration, not proof that a product was delivered, sold, invoiced, or paid.
2. Every planned supply is represented by a dated occurrence with a traceable source and status.
3. Planned quantity, dispatched quantity, delivered quantity, returned quantity, and billable quantity are distinct values.
4. Schedule changes must be effective-dated and must not silently rewrite completed or financially posted history.
5. Reprocessing a schedule-generation window must be idempotent: no duplicate occurrence for the same subscription line, business date, shift, and schedule version.
6. Customer mobile/web self-service login is not part of current scope. Authorized tenant staff manage customer subscriptions.
7. A tenant may operate without delivery staff; the owner can manage and execute the same workflow.
8. Subscription recurrence must not assume only morning/evening; shifts are configurable, with common AM/PM presets if desired.

## 2. Subscription structure

A subscription should conceptually contain:
- Customer and service address.
- Responsible tenant and service branch/area.
- Start date and optional end date.
- Status and lifecycle reason.
- One or more subscription lines.
- Product/variant/SKU, unit, quantity and price basis per line.
- One or more shift assignments per line.
- Recurrence rule per line or schedule group.
- Effective-dated schedule versions.
- Delivery instructions and allowed substitutions, if configured.
- Billing cycle and billing policy reference.
- Pause/skip/holiday/extra-supply exceptions.
- Audit history and staff attribution.

Do not assume every product on a customer's subscription shares the same recurrence, shift, price, or effective date.

## 3. Lifecycle states (candidate)

| State | Meaning | Allowed next outcomes (subject to policy) |
|---|---|---|
| Draft | Being prepared; not yet active | Pending confirmation, Active, Cancelled |
| Pending Confirmation | Awaiting internal/customer agreement confirmation | Active, Rejected, Cancelled |
| Active | Eligible to generate planned occurrences | Paused, Change Pending, Completed, Cancelled |
| Paused | Temporarily not generating normal supply | Active, Cancelled, Completed |
| Change Pending | Future-dated changes awaiting effective date/approval | Active, Paused, Cancelled |
| Completed | End date/contract completion reached | Reopened only through explicit new action/policy |
| Cancelled | Terminated; no future normal occurrences | New subscription required for restart unless policy explicitly allows reinstatement |

The exact states and transitions require product-owner approval. State must not be inferred from a date alone.

## 4. Recurrence model

The recurrence engine must support at least these patterns without creating a separate hard-coded feature for each:

- Every day.
- Selected weekdays (e.g. Monday, Wednesday, Friday).
- Every N days (e.g. every 2 days, every 3 days).
- Alternate-day anchored cycle (must define anchor date).
- Selected dates of month, if supported after month-end edge-case design.
- Custom date pattern/one-time overrides.
- Per-shift recurrence and quantity.
- Multiple products with independent schedules.

### 4.1 Required recurrence properties

A recurrence definition needs, conceptually:
- Pattern type and parameters.
- Start/anchor date.
- Optional end date.
- Applicable weekdays or interval.
- Shift(s).
- Quantity and unit per shift.
- Time zone/business calendar context (India time zone is the product baseline; store timestamps consistently).
- Effective-from/effective-to.
- Schedule version and creation source.
- Cut-off policy reference.
- Exception precedence rules.

For every-N-day patterns, the anchor date is essential. “Every other day” without an anchor is ambiguous.

### 4.2 Worked examples

Assume a schedule starts Monday 2 November 2026; these examples illustrate semantics, not a fixed calendar policy.

| Customer request | Intended rule | Example eligible days |
|---|---|---|
| Daily morning | Every day, AM | Mon, Tue, Wed, Thu... |
| Evening only | Every day, PM | Mon PM, Tue PM... |
| Alternate day | Every 2 days from anchor | Mon, Wed, Fri, Sun... |
| Every third day | Every 3 days from anchor | Mon, Thu, Sun... |
| Monday/Wednesday/Friday | Selected weekdays | Mon, Wed, Fri |
| Weekdays only | Mon–Fri | Mon, Tue, Wed, Thu, Fri |
| Morning Mon–Sat, no Sunday | Selected weekdays + AM | Mon–Sat AM |
| Milk AM, curd PM | Separate line schedules | Product-specific shifts |

### 4.3 Calendar boundaries

The recurrence engine must explicitly define:
- What happens when a monthly date does not exist (e.g. 29th–31st).
- Whether a paused interval shifts an every-N-day anchor or merely suppresses occurrences.
- Whether holidays suppress supply globally, by branch, route, or customer.
- Whether a schedule is evaluated by local business date or UTC timestamp.
- How daylight/time-zone changes are handled if future geographic expansion occurs.
- Whether branch closures override customer schedules.
- How backdated schedule changes are handled after billing or fulfilment.

Do not decide these implicitly in code.

## 5. Exceptions and precedence

Potential exception types:
- Customer skip for a date/shift.
- Pause for a date range.
- Branch/service-area closure.
- Route or shift non-operating day.
- Extra/additional supply.
- Product substitution.
- Quantity override.
- Address/instruction override.
- Staff-entered correction with reason.

A candidate precedence model is:
1. Legally/operationally mandatory block or product hold.
2. Tenant/branch closure.
3. Explicit subscription cancellation/end.
4. Approved date-range pause.
5. Date/shift-specific skip.
6. Approved date/shift override or extra supply.
7. Base recurrence.

This precedence is a proposal and needs scenario validation. Conflicts must be surfaced, not silently resolved.

## 6. Cut-off and change handling

The system must support configurable cut-off policies by branch, shift, service area, and possibly product:
- Before cut-off: change may affect the next eligible occurrence.
- After cut-off but before dispatch lock: change may require supervisor approval and a visible operational alert.
- After dispatch/fulfilment: do not mutate the historical occurrence; record a correction/return/credit or next-occurrence adjustment according to approved policy.

Cut-off values, approval thresholds, and whether customers can request changes through staff channels are open decisions.

## 7. Occurrence generation and status

Candidate occurrence states:
- Planned
- Confirmed
- Allocated
- Picked
- Dispatched
- Partially Fulfilled
- Fulfilled
- Skipped
- Cancelled
- Failed
- Exception Review

State transitions must be controlled and auditable. An occurrence may be fulfilled partially or through multiple attempts only if quantity aggregation and billing semantics are explicitly defined.

Generation must:
- Use a bounded date window and a versioned recurrence definition.
- Store the source schedule version and generation run.
- Enforce a uniqueness/idempotency key.
- Respect tenant/branch calendar and effective-dated exceptions.
- Record why an expected occurrence was not generated where operationally important.
- Support safe regeneration after a failed job.

## 8. Billing and price separation

Subscription schedule generation must not itself create revenue or a paid invoice.

Billing may be based on:
- Actual delivered quantity.
- Confirmed fixed recurring quantity, with adjustment rules.
- Periodic subscription charge.
- Hybrid line-level policy.

These are alternatives, not approved decisions. The billing policy must define:
- Treatment of skipped, failed, partial, extra, substituted, and returned supply.
- Rate effective date and price changes.
- Proration for start/end/pause.
- Advance balance and customer credit.
- Month-end cut-off and late fulfilment corrections.
- Statement versus tax invoice/document requirements.

## 9. Notifications and communication

Potential messages include schedule activation/change, pause confirmation, upcoming change, failed delivery, outstanding balance, and statement readiness. Channel availability, consent, language, retry and cost policy are separate requirements. Notifications must not be treated as proof that a customer received or accepted supply.

## 10. Acceptance scenarios

The later test specification must cover:
1. Daily AM and daily PM subscriptions.
2. Multiple product lines with different shifts and quantities.
3. Every-N-day schedules with a stable anchor across month boundaries.
4. Selected weekdays and branch-specific closed days.
5. Pause/resume without unintended anchor drift.
6. Skip for one date/shift only.
7. Future-dated quantity/product/rate change while preserving earlier occurrences.
8. Change after cut-off with approval and audit.
9. Extra supply outside base recurrence.
10. Failed/partial delivery and its billing treatment.
11. Schedule-generation retry without duplicates.
12. Concurrent staff edits and generation job without lost updates or duplicate occurrences.
13. Branch transfer/service-area change with clear effective date.
14. Customer with multiple active subscriptions/addresses where permitted.
15. Month-end statement/invoice calculation with adjustments and receipts.

## 11. Configuration and evidence dependencies

- ODR-006/007 baseline is selected in ODR sections 16.10–16.11: immutable past occurrences, effective-dated agreement versions, explicit recurrence anchors, configurable shifts/calendars/cut-offs and idempotent occurrence generation.
- Tenant/Branch calendar, closed days, lead time, cut-off and subscription exceptions are configuration values; precedence must follow the configuration-key scope declared under ARC-PV-001.
- Unit/price/discount/billing policy remains under ODR-008/013/014 and must be snapshotted on occurrences/billable fulfilment.
- Offline mutation guarantees remain limited by ODR-020; stale subscription edits must be revalidated and never override newer versions.
- Notification provider/channel delivery remains ODR-021; communication delivery never changes subscription state.

No recurrence or billing configuration may rewrite completed delivery/posted financial history. No physical schema is approved by this document.
