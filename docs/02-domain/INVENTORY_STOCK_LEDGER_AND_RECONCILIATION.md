# Inventory, Stock Ledger and Reconciliation

**Status:** Working baseline — domain requirements  
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

A branch may contain several stock locations. A counter, cold room, processing area, vehicle, or delivery staging area may need distinct stock custody, but their exact modelling is a design decision.

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

A movement should record product, quantity, base UOM, location, lot where applicable, stock status, business date, posting timestamp, source reference, actor and reason. Preserve the original entered quantity and conversion details where UOM conversion is involved.

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

## 7. Inter-location transfers

A transfer should have a controlled lifecycle:
1. Draft/requested.
2. Approved where policy requires.
3. Dispatched from source location.
4. In transit.
5. Received at destination with actual quantities and discrepancies.
6. Closed or exceptioned.

Source and destination must be authorized locations. Dispatch reduces source available/on-hand according to policy and records in-transit custody. Receipt records destination stock. Shortage, damage or rejection during transit must be separately recorded and investigated.

Do not treat a transfer as a sale or purchase. A transfer must preserve lot identity and cost basis where applicable.

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

## 12. Open decisions

- Base UOM, supported precision, conversion and rounding (ODR-008).
- Lot/batch and expiry tracking profiles (ODR-011).
- Transfer and stock custody rules (ODR-012).
- Valuation method and accounting integration (ODR-016/017).
- Reservation timing and allocation priority.
- Negative stock policy and authorized exception.
- Count cut-off and closed-period correction.
- Whether vehicles and delivery bags/cans are stock locations or custody containers.
