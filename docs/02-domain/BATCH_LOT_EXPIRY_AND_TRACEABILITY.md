# Batch, Lot, Expiry and Traceability

**Status:** Working baseline — domain requirements  
**Related decisions:** ODR-011, ODR-012, ODR-016, ODR-017  
**Scope:** Traceability for purchased goods, raw milk, ingredients, packaging inputs where relevant, work-in-progress and finished products.

## 1. Purpose and applicability

Traceability depth depends on what a tenant does:
- Reselling sealed branded products may need supplier batch and expiry capture where available or required.
- Repacking loose milk needs source collection/lot linkage and packaging/production linkage appropriate to the operation.
- Processing dairy products needs input-to-output lineage, production date, batch identity and relevant shelf-life controls.
- A small business selling same-day loose milk may use a simpler controlled record where full lot handling is not operationally justified.

The system must allow capability profiles without losing traceability for transactions where the tenant has enabled it or where applicable rules require it.

## 2. Identity model

Distinguish:
- Supplier/manufacturer batch reference.
- Internal receiving lot.
- Raw milk collection group/lot.
- Production batch.
- Finished goods lot.
- Serial identity, where a product genuinely requires individual serial tracking.

Do not assume supplier batch numbers are globally unique. Retain supplier, product, source document and supplier's original batch reference. Internal IDs must be unique within the platform's defined scope.

A lot may be split into child lots or combined into a derived lot. Preserve parent-child or many-to-many input/output relationships rather than replacing the source references.

## 3. Lot record

A lot record may contain:
- Tenant, product and lot type.
- Internal lot identifier.
- Supplier/manufacturer lot reference, if supplied.
- Source receipt, collection or production reference.
- Received/produced quantity and UOM.
- Receipt/production date and time.
- Production date, best-before/use-by/expiry date where applicable.
- Quality/inspection status.
- Storage requirements and location history where captured.
- Current stock status and quantity derived from movements.
- Relevant documents, test records and recall/hold status.

Dates must preserve source meaning. Manufacture date, packing date, best-before date and expiry/use-by date are not interchangeable.

## 4. Expiry and shelf-life

Expiry policy must be product/category-specific and configurable within validated boundaries. Support:
- Explicit supplier/manufacturer expiry date.
- Production date plus configured shelf-life rule.
- Best-before and use-by concepts as separate fields where relevant.
- Near-expiry thresholds and alerts.
- Expired stock quarantine/blocking.
- Controlled disposition such as return, disposal or authorized non-sale use.

Do not calculate a legally valid expiry date from a generic default. Where source data is missing, retain “not provided/unknown” rather than inventing a date. Any default shelf-life rule must be approved for the specific product/process and versioned.

## 5. Allocation and picking

Candidate lot allocation strategies include FEFO (First Expiry, First Out), FIFO (First In, First Out), manual lot selection or product-specific policy.

The strategy must consider expiry, quality status, customer/order constraints and tenant configuration. Quarantined, recalled, expired or blocked lots must not be picked for normal sale or processing.

FEFO is a candidate operational strategy, not a universal legal requirement for every product.

## 6. Traceability lineage

The system should answer:
- Which supplier/collection entries contributed to a given raw milk lot?
- Which raw milk lots and ingredients were consumed in a production batch?
- Which finished product lots resulted from that batch?
- Which locations currently hold quantity from a lot?
- Which sales, deliveries or customers received units from an affected lot, to the extent the transaction model captures lot allocation?
- What quantity remains, was returned, wasted or transferred?

For blended milk, preserve source contribution quantities and measurement basis. For production, preserve input/output and loss records with units and conversion assumptions.

## 7. Quality holds and recalls

Support controlled states such as:
- Pending inspection.
- Released.
- Quarantined.
- Rejected.
- Recalled/blocked.
- Disposed/closed.

A hold or recall should identify the affected lot and propagate restrictions to derived lots and downstream stock where lineage exists. Authorized release or disposition requires actor, timestamp, reason and evidence.

Customer/transaction impact reports must show traceability coverage limitations if lot allocation was not captured at sale/delivery time.

## 8. Corrections and data integrity

Lot identity and source references must not be silently rewritten after downstream consumption. Corrections should use linked amendments, split/merge lineage events or reversals.

Concurrent allocation, split, merge, transfer and consumption operations require locking or equivalent concurrency-safe controls. Prevent the same quantity from being consumed by two operations.

## 9. Reporting

- Lot-wise available, reserved, quarantined and expired quantity.
- Near-expiry inventory.
- Supplier/manufacturer lot exposure.
- Raw milk source contribution.
- Production batch input/output lineage.
- Recall/hold affected stock and downstream transactions.
- Expiry loss and disposal.
- Traceability completeness by product and process.

## 10. Acceptance scenarios

1. Receive a branded product with supplier batch and expiry reference.
2. Record an unknown expiry without fabricating a date.
3. Create a raw milk lot from multiple collection entries and preserve contribution quantities.
4. Split a lot and retain parent lineage.
5. Combine inputs into a production batch and derive finished lots.
6. Quarantine a lot and prevent normal picking.
7. Place derived lots on hold when a source lot is recalled.
8. Allocate stock using a configured FEFO strategy.
9. Show which downstream transactions consumed a lot where lot-level issue capture is enabled.
10. Prevent concurrent consumption beyond available lot quantity.
11. Correct lot data through auditable amendment without losing source history.

## 11. Open decisions

- Which product categories require batch/expiry tracking by default.
- Required traceability depth for each operating profile.
- Shelf-life rule governance and evidence.
- FEFO/FIFO/manual allocation defaults.
- Recall workflow and notification responsibilities.
- Handling of reusable milk cans/containers versus consumable packaging.
- Regulatory record retention and traceability requirements after official-source review.
