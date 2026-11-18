# Batch, Lot, Expiry and Traceability

**Status:** Recommended traceability baseline selected under the product owner's delegated decision mandate — product-level tracking profiles, versioned shelf-life and controlled lot lineage; statutory specifics remain evidence-gated  
**Related decisions:** ODR-011, ODR-012, ODR-016, ODR-017  
**Scope:** Traceability for purchased goods, raw milk, ingredients, packaging inputs where relevant, work-in-progress and finished products.

## 1. Purpose and applicability

Traceability depth depends on what a tenant does:
- Reselling sealed branded products may need supplier batch and expiry capture where available or required.
- Repacking loose milk needs source collection/lot linkage and packaging/production linkage appropriate to the operation.
- Processing dairy products needs input-to-output lineage, production date, batch identity and relevant shelf-life controls.
- A small business selling same-day loose milk may use a simpler controlled record where full lot handling is not operationally justified.

The system must support configurable product/material tracking profiles without losing required lineage. A Tenant may simplify tracking only where the product/process risk profile and verified applicable rules permit it. A setting cannot disable a fixed traceability/expiry control required by the platform's verified product or legal policy.

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

Expiry policy must be product/category/process-specific, versioned and configurable within validated boundaries. Each profile defines whether date capture is required, accepted date types, source evidence, shelf-life basis, storage conditions, near-expiry thresholds and disposition controls. Support:
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

**Selected allocation baseline:** where an eligible lot has a valid expiry/use-by date and the product is controlled by expiry, FEFO is the default allocation strategy. Where expiry is not meaningful or not recorded by the approved profile, FIFO by eligible receipt/production date is the default. Manual lot override is permitted only with a reason and the required permission. A lot with unknown expiry must not be assigned a fabricated date or be treated as later-expiring than a known lot; its allocation follows a separate conservative product policy. FEFO/FIFO are operational defaults, not a claim about statutory requirements.

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

A hold or recall identifies the affected source lot and propagates restrictions to all known derived lots and downstream stock/transactions through the preserved lineage. Where a product profile lacks lot-level allocation, reports must explicitly disclose the resulting traceability limitation. Release or disposition requires a separately authorized, evidenced workflow. Authorized release or disposition requires actor, timestamp, reason and evidence.

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

## 11. Selected tracking policy and configuration dependencies

- Tracking profile choices are assigned by product/material class: (a) no lot tracking for low-risk products where permitted; (b) supplier/manufacturer batch reference capture; (c) full internal lot and expiry lineage; (d) serial/unique-unit tracking only where each unit requires identity. The profile is effective-dated and cannot weaken a verified mandatory requirement.
- Raw milk, ingredients, repacked goods and manufactured finished lots use the traceability level justified by their source/process/quality risk; product examples are not legal determinations.
- Shelf-life rules require a defined source (manufacturer data, validated product/process specification or verified official requirement), method, version, approver and effective dates. No generic default may invent an expiry date.
- FEFO is the default for eligible expiry-controlled lots; FIFO is the default where expiry does not apply; manual selection requires a permission and reason.
- Recall/hold authority, release and disposition use separate permissions, evidence and notification workflow; an ordinary stock-task grant does not imply release authority.
- Cans/crates/bags are returnable custody assets/containers with identity/condition/deposit where needed, not consumable packaging lots unless a separate material SKU is consumed in production.
- Legal retention and statutory traceability records require current authoritative review after project completion under ODR-003/016/026. Do not claim compliance until verified.

This baseline is governed by ODR-038. Exact product classes and statutory obligations remain evidence-dependent; profile scope and history preservation are stable.
