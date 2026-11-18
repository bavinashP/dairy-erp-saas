# Milk Collection, Quality and Farmer Settlement

**Status:** Recommended domain baseline — ODR-009/010 configurable quality and rate-chart policies selected; regulatory values remain externally verified  
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

Supported units, precision and conversion are governed by DBR-004 and ODR-008 section 16.40. Each measurement carries an explicit UOM/dimension. Do not assume that litres and kilograms are interchangeable or that a universal density conversion is valid for every milk condition.

Where volume is measured, preserve the original source volume, UOM, source resolution, method/device, timestamp and calibration/provenance where available. Where weight is measured, preserve the original gross and tare values and derive net weight under the same compatible dimension with a reproducible calculation/version. Submitted, accepted, rejected, adjusted, converted and posted quantities remain separate.

A mass/volume conversion is permitted only through an approved contextual rule with the required material/density/temperature basis, effective version, precision and evidence; source and derived results are retained separately. If the conversion basis is missing, the system does not invent a default factor.

Use the validated workflow/device precision profile selected by the Tenant within platform-supported bounds. Instrument resolution/accuracy, business acceptance tolerance and rounding are separate. Source measurements are not overwritten or rounded to a target merely because a value falls within tolerance.

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

## 12. Configuration and evidence dependencies

- ODR-009/010 baselines are in ODR sections 16.12–16.13: versioned quality profiles, provenance-rich samples/results, explicit quality states, optional analyzer integration, configurable flat or quality-linked rate charts and explainable settlement calculations.
- Profile parameters, test methods, collection sessions, quality limits and settlement formulas are configured per valid milk/material and operating process. The platform does not impose one universal Fat/SNF formula, rate table, statutory test frequency or pay cycle.
- Any threshold represented as mandatory for food safety/statutory compliance must be backed by current authoritative evidence and post-project qualified review; an administrator cannot mark failed mandatory evidence as passed by changing a setting.
- Quantity precision, unit conversion and rounding use DBR-004/ODR-008's governed UOM and precision catalogue and preserve source/device readings, conversion versions and calculation evidence.
- Blending, lot lineage, processing release and stock hand-off follow ODR-011/012 and retain collection source traceability.
- Analyzer/weighment devices use optional tenant-bound adapters; manual capture remains supported with actor/source/method evidence.
- Farmer acknowledgement/evidence method is configurable by collection/settlement workflow, with proof and dispute history retained.
- Applicable tax/accounting treatment remains under ODR-016/017 and the post-project legal review boundary.

This working domain document defines capabilities and safeguards; it does not constitute a legal quality standard or approve commercial farmer rates.
