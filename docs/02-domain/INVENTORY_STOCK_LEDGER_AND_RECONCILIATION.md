# Inventory, Stock Ledger and Reconciliation

**Status:** Recommended domain baseline — stock ownership, ledger and safe negative-stock baseline selected; costing/valuation remains policy-gated  
**Related decisions:** ODR-008, ODR-011, ODR-012, ODR-016, ODR-017  
**Scope:** Inventory for raw milk, packaged goods, resale products, ingredients, packaging materials, consumables and finished products.

## 1. Objectives and operating profiles

Inventory must support:
- A small counter retailer tracking product-wise opening, purchase, sale and closing stock.
- A delivery-focused dairy reserving and issuing stock for planned routes.
- A dairy packing loose milk into saleable packs.
- A processor managing ingredients, work-in-progress and finished goods.
- A multi-branch operator transferring stock between locations.

Do not force advanced warehouse, batch or manufacturing workflows on every tenant. Conversely, a tenant that enables traceability or processing must not bypass the controls required by those capabilities.

## 2. Inventory concepts and boundaries

Keep these concepts distinct:
- Product/SKU: what is bought, stored, sold or consumed.
- Stock location: where stock is held.
- Stock status: whether stock is available, reserved, quarantined, damaged, expired or otherwise restricted.
- Lot/batch: a traceable quantity with a common receipt or production identity.
- Stock movement: immutable evidence of a quantity change.
- Stock balance: a derived view of movements by product, location, lot and status.
- Valuation: the cost assigned to inventory under the selected accounting policy.

A Branch may contain several StockLocations. A counter, cold room, processing area, vehicle or delivery staging area may require distinct stock-location and/or custody treatment. Physical location, StockLocation, stock-owning Branch and physical custodian are separate concepts; the custody model is governed by [Shared-Site Accountability and Custody Management](SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md).

## 3. Stock ledger as source of truth

Every posted stock change must create a durable, auditable stock movement linked to its source transaction. The balance is derived from posted movements and controlled opening entries, not edited directly.

Movement types may include:
- Opening balance.
- Purchase receipt.
- Accepted milk collection.
- Transfer out and transfer in.
- Reservation and release (if represented in the stock availability model).
- Issue to POS, delivery, internal use or processing.
- Processing consumption and output.
- Customer return and supplier return.
- Damage, spoilage, expiry and wastage.
- Physical count gain/loss adjustment.
- Reversal or correction.

A movement should record product/SKU, quantity, SKU base UOM, location, lot where applicable, stock status, business date, posting timestamp, source reference, actor and reason. Preserve the original entered quantity/UOM, source precision, normalized base quantity, conversion factor/rule version, precision profile and relevant evidence where UOM conversion is involved.

Each stock-managed SKU has exactly one effective base UOM at a time (Product defaults may be inherited only where compatible). The stock ledger uses that SKU base UOM consistently. Purchase, receipt, issue, sale, transfer and count may use a permitted alternate UOM only through an active compatible UnitConversionVersion. Incompatible dimensions cannot be added or generically converted. Pack count and declared content remain separate; pack/unpack/case-break/repack is a controlled versioned event, not a generic UOM conversion.

A later base-UOM, conversion, pack-definition or precision-profile change applies prospectively and never rewrites posted movements or historic quantity interpretation. An unsafe/incommensurate base-UOM change requires a controlled migration/reconciliation or a new SKU.

## 4. Availability and stock status

At minimum, distinguish:
- Available: eligible for allocation/sale.
- Reserved: committed to a valid demand but not yet issued.
- Quarantined: awaiting inspection or authorized release.
- Damaged: not saleable until disposition.
- Expired: not eligible for normal sale/use.
- In-transit: transferred but not yet received, if transfer workflow uses this state.

A displayed “on hand” total must not be confused with “available to promise”. Reports and APIs must define whether they show physical on-hand, available, reserved, quarantined, or in-transit quantity.

## 5. Stock receipt and issue

Receipts must originate from approved source workflows such as goods receipt, accepted milk collection, production output or authorized opening balance.

Issues must reference an operational reason such as POS sale, delivery dispatch, processing consumption, sample, internal use, return or disposal.

The same source transaction must not post stock twice. Posting endpoints and background jobs require idempotency controls and database-level integrity safeguards.

## 6. Reservations and demand allocation

Where a tenant uses subscriptions, orders or delivery runs, stock may be reserved against dated demand. Reservation policy must define:
- Reservation timing and cut-off.
- Branch and stock-location selection.
- Lot/expiry selection strategy.
- Partial allocation and shortage behavior.
- Cancellation, skip, pause and rescheduling effects.
- Release of unused stock after run closure.

A reservation is not a physical stock movement unless the ledger policy explicitly represents it as a paired status movement. Avoid counting reservation as both an issue and an on-hand reduction.

## 7. Internal StockLocation transfers

An internal transfer is a movement between two StockLocations that have the **same stock-owning Branch attribution**. It is governed by `inventory.stock.transfer` and the ordinary inventory transfer lifecycle:
1. Draft/request.
2. Approval where Tenant policy requires.
3. Dispatch from the source StockLocation.
4. In-transit status/custody where applicable.
5. Receipt at the destination with actual quantities and discrepancies.
6. Close or controlled exception.

The system must validate both StockLocations against the same current stock-owning Branch. It must reject this workflow if the source and destination resolve to different Branch attribution scopes. It preserves product, lot, serial/container, UOM, stock status and cost basis as applicable. It does not change Branch attribution.

Dispatch and receipt stock effects remain linked to actual custody evidence where physical custody changes. Missing acknowledgement, damage, shortage or rejection remains an explicit exception. A transfer is not a sale or purchase.

### 7.1 Branch stock-attribution transfers

A Branch stock-attribution transfer is a separate workflow that changes **operational stock-ledger attribution** from a source Branch to a different destination Branch. It is not an internal transfer, a StockTaskAccessGrant, a shared-site handling task, a location edit or a custody handover alone.

It must use `StockAttributionTransfer` and the dedicated `inventory.stock_attribution_transfer.*` permission family:
- `inventory.stock_attribution_transfer.create/update_draft/submit` creates and submits a versioned request for distinct source/destination Branches and exact StockLocations, products/lots, UOMs, quantities, reason and custody/evidence profile.
- `inventory.stock_attribution_transfer.approve_source` authorizes release by a principal with the exact source Branch scope.
- `inventory.stock_attribution_transfer.approve_destination` authorizes acceptance by a principal with the exact destination Branch scope.
- Both Branch-side approvals are mandatory and bound to the same immutable transfer version. One human may perform both stages only when independently assigned both permissions and no fixed/active separation-of-duties rule requires distinct users.
- `inventory.stock_attribution_transfer.dispatch/receive` records actual dispatch and receipt. In-transit quantity remains source-attributed until destination acceptance and posting.
- `inventory.stock_attribution_transfer.post` changes Branch attribution only for the quantity actually accepted and only after required evidence, scope, approval and entity/accounting/tax applicability checks succeed.
- `inventory.stock_attribution_transfer.reconcile/cancel/reverse` handles discrepancies, pre-dispatch cancellation and linked compensating corrections. After dispatch, no simple cancellation or assumed return is allowed.

**Quantity/custody treatment**
- At dispatch, the dispatched quantity leaves source available stock and is represented in the controlled in-transit state while preserving source Branch attribution.
- Physical custody is separately transferred only when the actual recipient accepts it under the effective custody assurance profile.
- At destination, received, accepted, rejected, damaged, disputed and outstanding quantities are captured separately.
- Only accepted quantities may be attributed to the destination Branch. Partial and rejected quantities remain linked to the transfer and last verified custodian/location until evidence-backed resolution.
- A StockMovement records the stock quantity/status/attribution effect. A CustodyEvent/Handover records physical custody. Link them where both facts occur; never double-count quantity or infer acceptance from time/notification.
- The stock task grant and ordinary `inventory.stock.transfer` cannot authorize, approve, dispatch, receive, post or reverse a Branch stock-attribution transfer.

**Legal/accounting boundary**
Branch attribution is an operational inventory responsibility, not a declaration of legal/economic title. The transfer does not automatically create a sale, purchase, GST supply, invoice, payable/receivable or journal. **Before dispatch**, resolve the source and destination Branches to the same verified LegalEntity and confirm that the applicable stock-attribution policy permits the internal movement. Unknown, conflicting, expired or unverified legal-entity mapping blocks dispatch. Different LegalEntities require a separately specified and legally/contractually/tax/accounting-validated inter-entity workflow; this internal transfer must not be used. Where the Branches share one verified LegalEntity but use different AccountingEntities/books, dispatch requires an active approved inter-book stock policy and reconciliation path. Revalidate legal entity, accounting-book and tax policies before final posting. Do not assume all Branches in a Tenant share one legal entity or that every Branch is a separate entity.

Valuation/cost-basis treatment follows the separately versioned ODR-017 policy. Transfer permissions do not imply valuation adjustment, write-off or financial posting.

## 8. Physical counts and adjustments

Support full and cycle counts, blind counts where required, count sessions, recounts, variance approval and controlled posting.

A stock adjustment must include:
- Count reference and scope.
- System quantity at count cut-off.
- Physically counted quantity.
- Variance quantity and value where valuation exists.
- Reason code, notes and evidence.
- Counter and approver.
- Posting and reversal history.

Concurrent sales, deliveries, receipts and transfers during counting require a defined cut-off/snapshot or movement-aware reconciliation. Never silently overwrite a live balance with a count result.

## 9. Costing and valuation boundary

Inventory quantity control and financial valuation are related but distinct. Candidate valuation methods and accounting treatment must be evaluated against applicable Indian accounting requirements and the tenant's business profile.

The domain must preserve purchase/production cost components, discounts, freight and adjustments as source facts. Do not hardcode a costing method or tax treatment in this document. ODR-016/017 and accounting design govern final choices.

## 10. Reporting and analytics

Minimum views:
- Stock on hand and available by product/location/status.
- Stock ledger and movement audit trail.
- Opening, receipts, issues, transfers and closing reconciliation.
- Low-stock and reorder suggestions.
- Slow-moving, non-moving and ageing stock.
- Near-expiry and expired stock.
- Damage, wastage and stock variance.
- Stockout frequency and lost/short fulfilment.
- Inventory value under the approved valuation method.
- Branch/location comparison.

Every report must specify business date, posting date, location scope, status inclusion and unit basis.

## 11. Acceptance scenarios

1. A small retailer records a direct purchase and counter sale with stock impact.
2. A delivery run reserves stock and releases unused quantity on closure.
3. A tenant without lot tracking can use simple product/location balances.
4. A tenant with lot tracking receives and issues specific lots.
5. A transfer remains in transit until destination receipt.
6. A partial transfer receipt records discrepancy without silently closing the shortage.
7. A physical count reconciles with concurrent stock movements.
8. An expired or quarantined lot is excluded from normal allocation.
9. Duplicate posting retries do not duplicate quantity.
10. Concurrent issue attempts cannot oversell beyond policy.
11. Reversal links to the original movement and preserves audit history.
12. Stock ledger totals reconcile to derived balances.

## 12. Configuration and evidence dependencies

- ODR-008/DBR-004 select a governed UOM dimension and precision catalogue, one effective base UOM per stock-managed SKU, validated effective-dated conversions and immutable source/derived quantity snapshots; no universal scale or cross-dimensional factor is permitted.
- Tracking profile determines whether lot/batch/expiry/serial details are mandatory by product, shelf life, traceability and quality risk. A product cannot disable tracking required by its verified profile.
- Stock ledger and physical custody are distinct but linked: StockMovement is authoritative for quantity/location/status effects; CustodyEvent records who physically held/handled the subject and where/when. A custody-only event does not create a stock movement. See [Shared-Site Accountability and Custody Management](SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md) and ODR-002 section 16.36. Inter-Branch ownership transfer is a separate workflow; a shared-site stock grant is not ownership transfer.
- Negative available stock is denied by default. A narrow Tenant-configured exception may apply only to eligible non-serialized/non-lot-controlled items, with explicit permission, audit and reconciliation; never bypass quality hold, expiry, ownership or closed-period controls.
- Reservation priority, expiry and release policy are configured per demand workflow and must be deterministic and auditable.
- Count sessions define cut-off/version; variance capture, approval and adjustment are separately permissioned. Closed-period changes use linked correction/reconciliation, not history rewrite.
- Vehicles and bags/cans are custody containers by default. A vehicle may be a StockLocation only where it physically holds controlled stock with explicit stock ownership, location scope and custody controls. Returnable cans are tracked through container/deposit/custody records, not mixed with saleable stock unless a separately defined SKU process requires it.
- Valuation/costing method and financial period treatment remain under ODR-017 and relevant accounting review.


## 13. Physical custody, handover and shared-site accountability

Stock ownership and ledger attribution do not prove who physically holds goods. Where the Tenant enables custody tracking, maintain a linked, append-oriented custody chain for the relevant stock quantity/lot, container, vehicle compartment or holding resource. Record the custodian, actual handler, source/destination, location, action, business and recorded timestamps, quantity/condition, required evidence and the custody assurance profile version.

A transfer dispatch and receipt have distinct inventory and custody effects:
- Dispatch records the stock ledger's approved source/in-transit effect and the actual custody handover to the next party only when that party's acceptance is captured as required by the effective profile.
- Transit handovers between carriers/holding points are separate linked custody events.
- Destination stock increases only for accepted quantities under the stock transfer policy. Partial, rejected or unacknowledged quantities remain explicit and retain the last verified custodian/location.
- A custody event that changes no stock balance must not create a synthetic StockMovement. If a stock quantity/location/status changes, the corresponding ledger movement is separately recorded and linked.
- Missing acknowledgement, failed notification or elapsed time never means acceptance. Preserve pending/disputed custody; do not create a custody gap.
- A custody discrepancy does not itself authorize stock adjustment, write-off, supplier deduction or financial posting.

A worker's stock-task grant or stock permission does not prove physical receipt. If a grant expires while the worker physically holds stock, block new tasks and stock mutations under ODR-002 while preserving the actual custodian/location until a real, authorized handover or incident resolution occurs.

Tenant custody assurance may be Basic single-party capture, Acknowledged handover or Verified high-assurance. Required evidence for raw milk/perishables may include collection/dispatch/arrival/unloading time, quantity, lot/container/tanker compartment, temperature reading and source/calibration, sample/quality status, seal integrity and receiving acceptance. Exact time/temperature and sanitation requirements come from the applicable versioned product/process profile and verified applicable rules; this inventory specification does not hardcode a universal limit.

Returnable cans/crates, vehicles and reusable containers are custody subjects, not automatically saleable stock. A vehicle is a StockLocation only where the Tenant explicitly models controlled stock physically held there and its stock ownership/location/custody controls are defined. Shared equipment and facilities have separate use, custody, maintenance/calibration and optional cost-allocation records; use does not transfer ownership or create an automatic financial posting.

All custody reads/actions remain permission- and scope-controlled. Full incident, handover, shared-resource, evidence, configuration and acceptance requirements are defined in [Shared-Site Accountability and Custody Management](SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md).
