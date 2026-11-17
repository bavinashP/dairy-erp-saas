# Milk Collection, Quality and Farmer Settlement

**Status:** Working baseline — domain requirements  
**Related open decisions:** ODR-008, ODR-009, ODR-010, ODR-011, ODR-012, ODR-016, ODR-017  
**Scope:** Recording raw milk received from farmers/producers and determining accepted quantity, payable amount, inventory impact, and traceability.

## 1. Design objective

Support a simple manual milk receipt for a small dairy as well as a more controlled collection workflow for a dairy that tests quality, manages multiple producers, chills or processes milk, and settles farmers periodically.

The system must not require every tenant to own laboratory equipment or use automated analyzers. Manual readings and externally generated test results may be recorded with source and operator details.

## 2. Collection event

A milk collection entry should conceptually capture:
- Tenant and receiving branch/collection location.
- Farmer/supplier identity.
- Collection business date and precise timestamp.
- Shift/session or collection round, if used.
- Milk type/category as configured.
- Gross measured quantity and unit.
- Measurement method/device and operator, where available.
- Container/can identifiers where the tenant uses them.
- Sample reference and quality test linkage, if performed.
- Preliminary acceptance status.
- Notes, attachments and correction history.

The collection entry is an evidence record. It must not be silently overwritten after it has contributed to stock, processing, or supplier settlement.

## 3. Quantity and measurement

The design must distinguish:
- Gross measured quantity.
- Tare/container weight, if weight-based measurement is used.
- Net quantity.
- Submitted quantity versus accepted quantity.
- Rejected quantity.
- Quantity adjusted under an approved, evidenced rule.

Supported units and precision are governed by ODR-008. Do not assume that litres and kilograms are interchangeable or that a universal density conversion is valid for every milk condition.

Where volume is measured, retain the measured value and unit. Where weight is measured, retain gross/tare/net evidence and the configured conversion basis, if a conversion is required.

## 4. Quality testing

Potential quality attributes may include Fat, SNF, density/lactometer reading, temperature, acidity, adulteration screening, or other tenant-defined tests. This list is a domain discovery list, not a claim that every parameter is legally mandatory or sufficient.

A quality result should retain:
- Sample/collection reference.
- Parameter, value and unit.
- Test method and device (manual or instrument).
- Test time and operator.
- Device/calibration reference where relevant.
- Result status and any retest/correction relationship.
- Applicable quality policy/rate chart version.

The platform must allow a tenant to configure which tests are captured for a milk category and workflow. If a test is not performed, record “not tested” distinctly from zero, failed, or missing data.

## 5. Acceptance and rejection

Candidate outcomes:
- Accepted.
- Accepted with adjustment/condition.
- Quarantined pending test/review.
- Partially accepted.
- Rejected.
- Re-test required.

Acceptance policy may consider quantity, test results, supplier history, temperature, adulteration concerns, and tenant-defined quality bands. The system must preserve the policy version and decision actor.

A rejected quantity must not enter usable raw-milk stock. A quarantined quantity must not be allocated to processing or sale until released by an authorized action.

## 6. Rate determination

The rate engine may support several tenant-selected methods:
- Flat rate per unit.
- Rate based on Fat.
- Rate based on SNF.
- Combined Fat/SNF formula.
- Quality slab/range.
- Fixed supplier-specific rate.
- Manually agreed rate with approval and reason.

Do not hardcode a single industry formula. Each rate chart needs:
- Effective-from and optional effective-to.
- Milk category and applicable supplier/collection scope.
- Formula or slab definitions.
- Units, precision and rounding policy.
- Bonus, penalty, deductions and caps where configured.
- Version and approval history.

A rate change applies prospectively unless an explicitly authorized correction is recorded. Historical collection entries retain the rate chart/version and calculation inputs used.

## 7. Farmer settlement

Settlement periods may be daily, weekly, fortnightly, monthly, or tenant-configured. The system should support:
- Aggregating accepted collection entries by farmer and period.
- Rejected/adjusted quantities and quality deductions.
- Bonus/incentive entries.
- Advances and recoveries.
- Other authorized deductions with reason.
- Gross payable, adjustments, net payable, payments and balance.
- Settlement statement and acknowledgement/evidence.

A settlement run should have a controlled lifecycle such as Draft → Reviewed → Approved → Posted → Partially Paid/Paid, with cancellation or correction through explicit reversal/adjustment rather than destructive edits.

A payment record and bank settlement evidence are separate. Cash, bank transfer, UPI or other methods may be recorded without requiring a payment gateway.

## 8. Inventory and processing hand-off

Accepted milk may:
- Enter raw-milk stock at the collection location.
- Transfer to a chilling/processing location.
- Be combined into a traceable lot under an approved blending policy.
- Be issued to a processing batch.
- Be disposed of as loss/spoilage under controlled reason codes.

The system must define how collection entries aggregate into lots and how lot lineage is retained. If multiple farmers' milk is combined, preserve the many-to-many relationship between source collections and resulting lots to the level required by the tenant's traceability policy.

Do not count the same accepted milk twice when moving from collection to raw stock, or from raw stock to processing input.

## 9. Corrections and fraud/error controls

Support:
- Duplicate collection detection.
- Correction with before/after values, reason, actor, timestamp.
- Supervisor approval for changes after settlement or stock posting.
- Device/operator attribution.
- Outlier detection for quantity, rate, or test readings.
- Reconciliation between collection register, analyzer output, weighment evidence, stock receipt and settlement.
- Period close and controlled reopening.
- Idempotent posting to stock and supplier payable.

A corrected reading must not erase the original reading. Recalculation after settlement must produce an adjustment/reversal workflow and preserve the original statement.

## 10. Reports and analytics

Required reports and analysis:
- Farmer-wise daily/shift-wise collection.
- Accepted/rejected quantity and rejection reasons.
- Fat/SNF and other parameter trends where captured.
- Rate chart and average procurement rate.
- Farmer-wise gross/net payable, deductions, advances and ageing.
- Collection-to-stock and stock-to-processing reconciliation.
- Collection centre/branch comparison.
- Seasonal and historical procurement trends.
- Supplier concentration and procurement dependency.
- Quality-versus-cost and yield analysis where sufficient data exists.

Forecasts must distinguish measured historical facts from estimated future quantities/rates. Low-volume or incomplete data must display confidence/limitations rather than false precision.

## 11. Acceptance scenarios

1. Record a simple manual collection without analyzer integration.
2. Record separate morning/evening collection sessions.
3. Capture gross, tare and net weight with unit.
4. Record a sample with multiple quality parameters.
5. Mark a parameter not tested without storing zero.
6. Quarantine milk pending a test and prevent allocation.
7. Partially accept a collection and keep rejected quantity out of usable stock.
8. Calculate payment using a versioned flat rate and a versioned quality-based chart.
9. Change a rate chart prospectively without recalculating prior collections.
10. Create a settlement with advances, deductions and partial payments.
11. Correct a collection after settlement through an auditable adjustment.
12. Combine milk from multiple collections into a lot and preserve source lineage.
13. Retry stock/payable posting without duplicate effects.
14. Reconcile collection quantity to stock and settlement totals.

## 12. Open decisions and evidence needed

- ODR-009: mandatory/optional quality parameters by operating profile; test methods and acceptance bands.
- ODR-010: rate formulas, settlement periods, advances, deductions and dispute handling.
- ODR-008: measurement units, conversion, precision and rounding.
- ODR-011/012: blending, lot traceability, processing and inventory hand-off.
- ODR-016/017: applicable tax/accounting treatment and supporting documents.
- Whether weighment/analyzer integrations are needed and which devices/providers are in scope.
- Whether farmer acknowledgement is required and acceptable evidence methods.

This document does not prescribe universal Fat/SNF formulas, legal quality limits, mandatory testing frequency, or a statutory payment cycle. Those require authoritative and operational validation before approval.
