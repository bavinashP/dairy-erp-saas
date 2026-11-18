# Quantity, UOM Precision and Money Rounding Analysis

**Document ID:** DBR-004  
**Status:** Recommended engineering baseline — precision architecture selected under the product owner's delegated decision mandate; per-workflow commercial/accounting/statutory values remain evidence-gated  
**Date:** 2026-11-17  
**Authority reviewed:** ERS-001, FRS-001, NFR-001, ODR-008, inventory stock ledger, milk collection/quality/farmer settlement, processing/packaging/yield, billing/receivables/payments, accounting double-entry/financial controls, canonical glossary and Tally field/voucher mapping specifications.

## 1. Purpose and boundaries

This analysis establishes a consistent conceptual approach for quantities, units of measure (UOM), measurement precision, conversions, monetary calculations and rounding across milk procurement, processing, inventory, sales, billing, farmer settlements and accounting.

It does not approve final UOM lists, decimal scales, measurement-device tolerances, commercial rates, statutory tax rounding, accounting policy, SQL types, DDL, ORM models or migrations. It is not a legal, tax or accounting opinion.

## 2. Existing documented requirements

The reviewed documents already establish that:

- ODR-008 governs supported units, precision, conversion, rounding and pack sizes.
- Milk collection must distinguish gross, tare, net, submitted, accepted, rejected and adjusted quantities.
- Litres and kilograms are not interchangeable by assumption; a universal milk density conversion is expressly disallowed.
- Quality measurements retain parameter, value, unit, method, device, timestamp and relevant calibration/policy evidence.
- Processing distinguishes planned/actual inputs, returned inputs, outputs, by-products, losses, rework, held and released quantities.
- Inventory movements retain quantity and base UOM; original entered quantity and conversion details must be preserved.
- Stock balances are derived from posted movements; posted movement history is not overwritten.
- Money must use exact decimal representation and explicit rounding; floating-point arithmetic must not be used for authoritative financial calculations.
- INR is the current product accounting currency baseline; multi-currency is not a current requirement.
- Posted journals must balance exactly under the defined precision policy; Tally mappings must not silently lose precision.

These are existing constraints, not newly invented requirements.

## 3. Recommended precision architecture

Treat precision as a set of separate policies rather than one global decimal setting.

| Precision layer | Meaning | Recommended treatment |
|---|---|---|
| Measurement/input precision | Resolution of the source scale, analyzer, meter or human entry | Preserve the actual entered/device value and its unit; retain device/method metadata where available |
| Stored quantity precision | Precision needed to preserve source evidence and downstream calculation | Must be at least sufficient for the accepted source resolution and approved conversions; do not prematurely round |
| Calculation precision | Precision used in conversions, rates, yields, taxes and intermediate arithmetic | Use exact decimal arithmetic with documented precision and deterministic operations |
| Posting precision | Precision accepted by a stock ledger, settlement, invoice or accounting posting | Apply a defined, domain-specific finalization and rounding rule at the posting boundary |
| Display precision | Digits shown to users in forms/reports | May be less than stored precision, but must not silently change the underlying value; show unit and rounding context where material |
| Device precision/tolerance | Accuracy/resolution and permitted deviation of an instrument | Defined per device/model/process and verified; not inferred from the number of digits displayed |

A single tenant-wide decimal scale is not sufficient for all measurement and commercial domains.

## 4. UOM and dimensional model

### 4.1 Unit dimensions

The domain model should distinguish unit dimensions, including at least:

- Mass/weight (for example kilogram, gram).
- Volume (for example litre, millilitre).
- Count/discrete quantity (piece, can, pack, crate).
- Time/duration where used operationally.
- Other dimensions only when a documented workflow requires them.

Units from incompatible dimensions cannot be added, compared or converted by a generic factor.

### 4.2 Product-specific stock base UOM

Recommended baseline:
1. Each stock-managed product/SKU has one configured stock base UOM for its stock ledger.
2. Purchase, sale, collection, production and transfer may accept other permitted UOMs only through an explicit validated conversion.
3. The transaction preserves the original entered quantity/UOM, normalized base quantity, conversion factor, conversion rule/version and any relevant measurement conditions.
4. Stock movement records use the product's base UOM consistently; reports may display another UOM only with a disclosed conversion.
5. Pack count and pack content remain separate: e.g. number of packs is discrete count, while net content is a mass or volume measure.
6. A change to a product's base UOM must not reinterpret historical movements. It requires a controlled migration/conversion plan or a new stock identity where necessary.

The final choice of product-versus-variant base UOM and allowed purchase/sales UOMs requires product catalogue review.

### 4.3 Milk mass/volume conversion

- Preserve the source measurement exactly as recorded: volume or weight, source device/method and timestamp.
- Do not convert litres to kilograms, or vice versa, using a universal fixed factor.
- Where the business explicitly needs a conversion, require a documented, versioned and approved basis suitable for the milk/material and measurement conditions (such as density and temperature context where relevant).
- Store the source measurement and converted result separately, with the factor/method/version and responsible actor.
- A conversion must not erase the original measurement or falsely imply greater accuracy than the source instrument supports.

### 4.4 Conversion governance

Each allowed conversion should define:
- Source and target UOM and dimension.
- Exact factor or controlled formula.
- Effective interval and version.
- Applicability (product/material, process, location or tenant) where needed.
- Precision and rounding behavior.
- Approval/verification status and source/evidence.
- Whether conversion is reversible and under what tolerance.

Conversion cycles must be tested. A conversion from A to B and back to A may produce a small numerical difference after rounding; the system must not silently use rounded intermediate values as the authoritative source.

## 5. Quantity precision and rounding rules

**Selected baseline:** use a governed precision catalogue by UOM, instrument and workflow; do not impose one Tenant-wide or database-wide decimal scale. Store source readings and declared device resolution separately from normalized/base quantities, calculation precision, posting precision and display precision. Tenants may select only among validated precision profiles supported for the relevant UOM/device/workflow; they may not enter arbitrary scales that exceed tested capability or rewrite prior readings.


1. Use decimal/fixed-point arithmetic for all authoritative quantities and rates; do not use binary floating-point as the source of truth.
2. Preserve source measurements before normalization or display formatting.
3. Define supported precision by UOM and workflow, considering instrument resolution, commercial practice, stock handling, process capability and downstream accounting.
4. Distinguish a measured value from an accepted quantity, a converted quantity, a posted stock quantity and a displayed quantity.
5. Define rounding mode and rounding boundary explicitly for each workflow; do not rely on language/runtime defaults.
6. Avoid repeated rounding at every intermediate calculation. Carry sufficient precision through the calculation and round only at the documented boundary.
7. Record any manual quantity adjustment separately with reason, actor, timestamp, approval and before/after values.
8. Define tolerance separately from rounding. A tolerance is an acceptable variance; it is not permission to alter source measurements.
9. Do not sum quantities expressed in incompatible UOMs. Convert to a common compatible unit under an approved rule first.
10. Preserve calculation inputs, conversion version and rounded result so the result can be reproduced.

### 5.1 Workflow-specific considerations

| Workflow | Required distinction |
|---|---|
| Milk collection | Gross, tare, net, submitted, accepted, rejected and adjusted quantity; source measurement and unit |
| Quality testing | Parameter value and unit; not tested, missing, zero and failed remain distinct states |
| Procurement rate | Rate basis UOM, quality inputs, rate-chart version and calculation precision |
| Inventory | Product base UOM, lot/location/status and immutable movement quantity |
| Processing | Input/output/by-product/loss UOM, process stage, mass-balance basis and configured tolerance |
| Packaging | Pack count, net content per pack and total content as separate quantities |
| Sales/delivery | Sold/fulfilled quantity, selling UOM and stock-base conversion |
| Reports | Explicit UOM basis, conversion and aggregation grain |

## 6. Monetary precision and rounding

### 6.1 Recommended engineering baseline

- Use exact decimal arithmetic for prices, rates, discounts, tax calculations, settlements, allocations and journal lines.
- INR is the current accounting currency baseline. The standard posting representation is rupees and paise (two decimal places); retain exact decimal calculation inputs and a versioned rounding result. Statutory document rounding and any whole-rupee rounding must be verified against applicable current rules after the separately scoped legal review.
- Keep unit rates/prices at a separately governed precision from final payable/receivable amounts. A rate per litre or kilogram may require more precision than a final invoice or settlement amount.
- Preserve the unrounded calculation inputs and intermediate result needed to reproduce a posted amount.
- Apply rounding once at an explicitly defined boundary unless a reviewed legal/accounting rule requires line-level rounding.
- Posted journal lines and totals must balance exactly at posting precision. Any rounding difference must be handled by an explicit, auditable rule—not an unexplained plug.
- Never silently truncate precision when exporting/importing Tally or another external accounting format. Detect and disclose unsupported precision or lossy mapping.

### 6.2 Rounding boundaries and selected default hierarchy

Use the following safe default hierarchy unless a verified statutory/accounting rule for the exact transaction type requires another method: preserve source precision; carry unrounded decimal intermediates; calculate line and document values using one versioned policy; round only at the declared final document/posting boundary; post INR amounts to paise precision; record any residual explicitly. A legal rule overrides a configurable business preference. The exact choice between per-unit rate precision, line-level tax rounding and document-level rounding is not invented here; it must be selected by a versioned domain policy after worked examples and later legal/accounting verification.

The following choices must be decided and tested; they are not interchangeable:

1. Per-unit rate calculation versus line extension.
2. Line-level discount and tax rounding versus document-level calculation.
3. Invoice total versus tax-component total rounding.
4. Farmer collection-line amount versus settlement-period aggregation.
5. Supplier/customer allocation and partial payment residual handling.
6. Journal-line rounding versus source-document rounding.
7. Stock valuation cost precision versus financial currency posting precision.
8. Tally export/import precision and any target-format limitation.

Every finalized rounding policy must define rounding mode, scale, boundary, order of operations, treatment of negative/reversal values and residual allocation. If no valid policy exists for an operation, it must not silently use runtime defaults.

### 6.3 Avoiding inconsistent totals

A single transaction may be represented in an operational subledger, invoice, payment allocation and general ledger. The platform must use one versioned calculation result or a reproducible posting instruction so that:
- The displayed amount matches the posted document.
- Subledger entries reconcile to the general ledger.
- Partial allocations cannot exceed the source amount.
- Reversals reproduce the original posted value and link to it.
- A retry cannot produce a different amount due to changed configuration or runtime defaults.

## 7. Worked scenarios

### Scenario A — milk measured by volume

A collection operator records a volume reading from an approved measuring device. The system retains the original volume, UOM, device/method, timestamp and source precision. Accepted quantity is recorded separately. If a downstream process needs mass, the system requires an approved conversion basis and retains both source and converted values. It must not apply a universal litre-to-kilogram factor.

### Scenario B — weight-based collection

The system retains gross weight, tare weight and calculated net weight with units and source evidence. If the container tare is changed or corrected, the original value and correction remain auditable. The net amount used for settlement is traceable to the exact calculation and approved adjustment.

### Scenario C — packaged product

A sale of 12 packs is a discrete count. Each pack's declared content is a separate volume or mass quantity. Stock may be tracked in packs or a content base UOM depending on the product configuration, but the conversion and pack definition version must be retained. A count of 12 packs must not be added directly to a quantity measured in litres.

### Scenario D — farmer settlement

Collection entries retain measured/accepted quantities and the rate-chart version. The settlement calculation uses the approved rate basis and precision. The final payable amount follows an explicit settlement rounding rule. Recalculation after settlement creates an adjustment/reversal workflow; it does not overwrite the original collection or payment.

### Scenario E — invoice and journal

A bill line is calculated from actual billable quantity and the effective price/tax policy. The final invoice amount and resulting journal are linked to the same calculation snapshot. The journal balances exactly at posting precision. The system records any permitted rounding residual explicitly and does not silently alter the source quantity or rate.

## 8. ERS-001 refinement requirements

Before physical database design, ERS-001 must define:
- UOM dimension and canonical unit catalogue ownership.
- Product/SKU base UOM and permitted transaction UOMs.
- Quantity scale/precision policy by UOM and workflow.
- Original versus normalized quantity and conversion evidence.
- Conversion versioning, applicability, effective dates and tolerance.
- Measurement device/method/calibration metadata where relevant.
- Rate precision versus amount precision.
- Monetary currency/scale and rounding-policy version.
- Rounding boundaries across billing, procurement settlement, inventory valuation and journals.
- Precision compatibility and loss detection for Tally import/export.
- Historical snapshot and correction behavior.

No SQL type or physical decimal scale is prescribed by this analysis.

## 9. Acceptance and verification scenarios

The future test suite must include:
1. Reject incompatible-unit addition and conversion.
2. Preserve a device reading at its actual source precision.
3. Convert an allowed UOM using the correct effective version.
4. Prove historical transactions do not change after a conversion factor update.
5. Verify volume-to-mass conversion is unavailable without an approved basis.
6. Reconcile gross, tare and net calculations and their correction history.
7. Verify pack count and pack content are not conflated.
8. Test conversion round-trip drift and tolerance handling.
9. Test quantities near precision and rounding boundaries.
10. Test rates with more precision than final currency amounts.
11. Compare line-level and document-level rounding against approved test vectors.
12. Verify negative values, credit notes, reversals and partial allocations.
13. Verify exact journal balance at posting precision.
14. Verify retry and concurrent posting produce the same single financial/stock effect.
15. Verify Tally round-trip detects unsupported precision and never silently truncates.
16. Verify reports disclose unit basis and do not aggregate incompatible dimensions.

## 10. Open decisions and dependencies

- **ODR-008:** UOM catalogue, supported precision, conversions, rounding and pack-size policy.
- **ODR-009/010:** milk quality, rate formulas and farmer settlement calculation/periods.
- **ODR-011/012:** processing yield, measurement tolerance, stock UOM and valuation.
- **ODR-013/014:** price, discount, tax, billing and invoice rounding boundaries.
- **ODR-016/017:** current statutory tax rounding and accounting policy verification.
- **Tally:** target release/version and field precision must be proven through a version-specific PoC before finalizing mapping limits.

No ODR status is changed or closed by this analysis.

## 11. Consistency review

- Consistent with ERS-001 quantity/stock integrity and financial integrity requirements.
- Consistent with inventory's requirement to preserve original quantity and conversion details.
- Consistent with milk collection's gross/tare/net and no-universal-density-conversion rule.
- Consistent with processing's explicit units, measurement basis and configurable yield tolerance.
- Consistent with accounting's exact decimal arithmetic, balanced journals and immutable posting history.
- Consistent with billing's effective-dated pricing/tax snapshots and rounding policy.
- Consistent with Tally's stable mapping and requirement to disclose unsupported/lossy fields.
- Does not approve a legal tax rule, commercial rate scale, device accuracy threshold or final database decimal type.

## 12. Non-goals

- No application code, SQL, DDL, ORM model, migration or physical database schema.
- No universal UOM list or fixed decimal scale for every quantity.
- No universal milk density conversion or quality formula.
- No final statutory tax/rounding interpretation.
- No final accounting, valuation or commercial pricing policy.


### 6.4 Configuration and historical integrity

The platform precision catalogue owns supported decimal ranges and unit dimensions. Product/SKU and workflow profiles select validated source/base UOM, allowable input resolution and any narrower precision profile. Device resolution/calibration is recorded separately from business tolerance. A Tenant may choose only valid supported profiles where the domain permits; it cannot weaken physical measurement integrity or statutory rules. Every transaction stores the precision/conversion/rounding policy versions required to reproduce its result. Later policy changes apply prospectively unless a lawful, explicitly approved correction/reprocessing workflow says otherwise.
