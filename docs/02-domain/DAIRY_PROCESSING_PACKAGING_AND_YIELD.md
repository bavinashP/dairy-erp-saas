# Dairy Processing, Packaging and Yield Management

**Status:** Working baseline — domain requirements  
**Related decisions:** ODR-008, ODR-009, ODR-011, ODR-012, ODR-016, ODR-017  
**Scope:** Optional processing and packing capability for tenants converting raw milk or other inputs into packaged or derived dairy products.

## 1. Operating profiles

The platform must distinguish:
1. Retail-only business buying sealed products for resale.
2. Loose milk retailer measuring and packing purchased raw milk.
3. Dairy performing basic processing/standardization and packing.
4. Dairy manufacturing products such as curd, paneer, ghee or other configured goods.

A tenant must enable only the process capabilities it performs. Product examples do not imply that every tenant manufactures them or that one universal process formula applies.

## 2. Process definition and versioning

A process/recipe definition may specify:
- Output product and expected output UOM.
- Input materials and expected quantities.
- Optional by-products and recoverable outputs.
- Packaging materials.
- Process steps and checkpoints.
- Quality parameters and release criteria.
- Expected yield range and loss tolerance.
- Equipment/work centre where used.
- Effective dates, version and approval.

A recipe/BOM change must not rewrite completed production records. Each production batch retains the exact process version and input assumptions used.

## 3. Production order and batch lifecycle

Candidate lifecycle:
1. Draft production plan/order.
2. Material availability check and reservation.
3. Approval if tenant policy requires.
4. Raw material and packaging issue.
5. Processing execution and checkpoint capture.
6. Actual input, output, by-product and loss recording.
7. Quality review and hold/release decision.
8. Finished goods packing/label reference capture.
9. Finished goods receipt into stock.
10. Cost and variance calculation.
11. Close or controlled correction.

Small operators may use a simplified “pack/convert” transaction without a full production order, provided the stock and traceability effects remain auditable.

## 4. Input and output accounting

A production batch must distinguish:
- Planned input quantity.
- Actual issued input quantity.
- Returned unused input.
- Output quantity by product.
- By-product quantity.
- Process loss/wastage.
- Rework or rejected output.
- Quantity placed on quality hold.
- Released finished quantity.

Units and conversions must be explicit. The platform must not assume litres, kilograms, pieces and packs are interchangeable.

Mass balance or yield checks should be configurable by process and measurement basis. A variance outside approved tolerance should require explanation and, where configured, review. Avoid false precision when input and output measurements use different units or measurement conditions.

## 5. Yield and wastage

Possible metrics:
- Gross yield.
- Saleable yield.
- Recovery/by-product yield.
- Process loss.
- Packaging loss.
- Rejection/rework rate.

Every metric must define numerator, denominator, UOM, process stage and whether it includes by-products or held/rejected output.

Record loss with a reason such as spillage, equipment/process loss, sampling, quality rejection, handling damage or unexplained variance. Reason categories are configurable, but the audit trail is not optional.

## 6. Quality and food-safety checkpoints

The process may capture temperature, time, test readings, operator checks, cleaning/sanitation evidence and release decisions as appropriate to the product and operation.

The system should support:
- Required versus optional checkpoints by process version.
- Not performed / pass / fail / pending states.
- Hold and release authority.
- Re-test and deviation records.
- Equipment/device references where integrated.
- Attachment of relevant test or inspection evidence.

This document does not prescribe technical process temperatures, legal limits, mandatory tests or food-safety procedures. Those require product-specific authoritative validation and must be maintained as controlled process instructions.

## 7. Packaging and label data

Where applicable, capture:
- Pack type/material and pack size.
- Number of packs and net content.
- Packaging material consumption.
- Batch/lot and packing date.
- Best-before/use-by/expiry data from approved product rules.
- Label/artwork version reference.
- Required declarations and verification status.

Label content and legal metrology/food labelling requirements must be separately researched against current official Indian sources before approval. Do not infer mandatory declarations from a generic ERP template.

## 8. Costing boundary

Production cost may include consumed raw materials, packaging, direct labour, utilities, overhead allocation and process loss, depending on the selected accounting policy.

Retain actual source quantities and cost components. The final cost allocation and inventory valuation method must be determined in the accounting architecture and checked against applicable Indian accounting requirements. Do not double count raw material cost when transferring it into work-in-progress and finished goods.

## 9. Multi-branch and contract processing

Support process execution at a designated location and transfer of finished goods to other branches. If one branch processes for another, distinguish ownership, custody, processing responsibility and financial responsibility.

External/contract processing may be a future extension; it should not be assumed to be in the initial release without a confirmed business requirement.

## 10. Reports and analysis

- Production plan versus actual.
- Input consumption by product/batch.
- Yield and recovery trend.
- Process loss and rejection.
- Quality holds and release time.
- Packaging material consumption.
- Production cost and variance.
- Finished goods output and availability.
- Batch traceability and recall exposure.
- Process/branch/equipment performance where data exists.

Forecasting must use historical data with visible assumptions and confidence limitations.

## 11. Acceptance scenarios

1. Retail-only tenant sells sealed goods without production setup.
2. Small dairy records a simple milk packing transaction.
3. A processor creates a production batch using a versioned recipe.
4. Actual input differs from planned input and variance is recorded.
5. Unused input is returned to stock.
6. Output is split into saleable, held, rejected and by-product quantities.
7. A quality failure blocks finished stock release.
8. A recipe change affects only future batches.
9. Finished lots retain input-lot lineage.
10. Packaging materials are consumed and reconciled.
11. Process loss beyond tolerance triggers review.
12. Production posting retry does not duplicate input consumption or output.
13. Concurrent batch edits are detected and resolved.
14. Production cost flows to inventory without double counting.

## 12. Open decisions

- Which process types and product families are in initial scope.
- Whether simple packing is a separate workflow from manufacturing.
- Recipe/BOM authoring and approval permissions.
- Required quality checkpoints by product.
- Yield tolerances and measurement basis.
- Packaging and label compliance requirements.
- Cost allocation and valuation method.
- Equipment, analyzer and weighing-scale integrations.
- Whether contract processing is required.
