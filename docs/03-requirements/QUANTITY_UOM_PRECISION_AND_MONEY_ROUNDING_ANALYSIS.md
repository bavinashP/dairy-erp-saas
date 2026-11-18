# Quantity, UOM Precision and Money Rounding Analysis

**Document ID:** DBR-004  
**Status:** Recommended domain baseline selected on 2026-11-19 under the product owner's delegated decision mandate. Canonical unit semantics, product/SKU base-UOM rules, precision layers, contextual conversions and money-rounding hierarchy are selected. Exact device tolerances, commercial formulas, statutory tax treatment and target-system limits remain versioned/evidence-gated.  
**Date:** 2026-11-19  
**Authority reviewed:** ODR-008; FRS; ERS; Business Rules Catalogue; Inventory Stock Ledger; Milk Collection, Quality and Farmer Settlement; Processing/Packaging/Yield; Billing/Receivables/Payments; Accounting/Double-entry; Tally field/voucher mapping and sync specifications.

## 1. Purpose and boundaries

This document defines the common conceptual rules for units of measure (UOM), quantity dimensions, product/SKU stock base units, conversions, source measurement precision, calculation and posting precision, money representation, rounding and external-system precision compatibility.

It applies across milk collection, supplier procurement, quality testing, inventory, processing, packaging, POS/sales, delivery, farmer settlement, billing, accounting, analytics and Tally exchange.

It is a domain and requirements decision, not a physical database design. It does not prescribe SQL types, DDL, ORM models, migrations, framework implementation, final instrument accuracy, universal milk-density values, commercial rate formulas, statutory tax interpretations beyond the specifically cited provision, or accountant/legal certification.

## 2. Selected invariants

1. **A quantity always has a dimension and UOM.** A naked number is not a complete quantity.
2. **Incompatible dimensions cannot be added, compared, averaged or converted using a generic factor.** Mass is not volume; count is not content volume; temperature is not a stock quantity.
3. **Every stock-managed SKU has exactly one authoritative stock base UOM at a time.** A Product may supply a default, but the SKU-level resolved value is authoritative where variants/SKUs differ.
4. **Source measurement is immutable evidence.** Normalized, accepted, converted, calculated, posted and displayed quantities are distinct representations and must not overwrite the original reading.
5. **Conversions are explicit, versioned and applicable to the material/process/context.** No universal milk litre-to-kilogram factor is permitted.
6. **Packaging is not a generic dimensional conversion.** Pack count and net content are distinct; pack/unpack is a controlled product/stock event governed by an effective PackDefinitionVersion.
7. **Precision is layered.** Source/device, stored, calculation, posting, display and tolerance are different policies; there is no single Tenant-wide decimal scale.
8. **Authoritative arithmetic uses exact decimal/fixed-point semantics.** Binary floating-point is not a financial or stock source of truth.
9. **Rounding is explicit, versioned and applied at named boundaries.** Tolerance is not rounding, and rounding must not modify source measurements.
10. **Historical outcomes are reproducible.** Transactions retain the unit, conversion, precision and rounding policy versions used at the time.
11. **External adapters must detect loss.** Tally or another target's lower precision must never cause silent truncation or hidden change to SaaS source values.
12. **Verified legal requirements override customer configuration.** A Tenant may select supported policies and stricter controls, but cannot weaken applicable statutory measurement, tax, package-declaration or audit requirements.

## 3. Canonical unit and dimension model

### 3.1 Core dimensions

The platform-governed catalogue uses stable, language-neutral dimension identifiers. The initial core dimensions are:

| Dimension | Typical canonical units | Dairy/business examples | Rules |
|---|---|---|---|
| Mass | milligram (mg), gram (g), kilogram (kg), tonne (t) | Milk powder, butter, cheese, ingredients, gross/tare/net weighment | Conversions within mass use exact/versioned factors |
| Volume | millilitre (mL), litre (L), kilolitre (kL) | Raw milk volume, liquid products, tank capacity | Conversions within volume use exact/versioned factors |
| Count | each/unit, piece, can, bottle, pack, crate, carton, pallet | Packets, cans, returnable crates, discrete stock units | Count conversions are discrete and only where the relationship is explicitly defined |
| Time | second (s), minute (min), hour (h), day (d) | Collection time, chilling duration, shift, processing duration, expiry intervals | Time units convert under explicit calendar/time-zone semantics |
| Temperature | degree Celsius (°C), kelvin (K) | Milk/product temperature, cold-chain and equipment readings | Temperature is a measurement attribute, not a quantity to aggregate; conversions include offsets |
| Length / area | metre (m), millimetre (mm), square metre (m²) | Packaging dimensions, tank/room or site measurements where needed | Enable only for a documented module/workflow |
| Energy / power | joule (J), kilowatt-hour (kWh), watt (W) | Refrigeration/equipment energy and utility allocation where required | Enable only for documented asset/utility workflows |
| Density / ratio / concentration | Explicitly typed derived measurement units | Milk density, composition ratios, Fat/SNF or other quality attributes | Parameter-specific unit and formula; never treat as an ordinary mass/volume conversion |

The catalogue may be extended with additional dimensions when a documented product capability requires them. A new dimension or unit must be centrally registered, tested and versioned; Tenants cannot redefine the meaning of a platform dimension or standard unit.

### 3.2 Unit identity and presentation

Each canonical UOM has:
- Stable machine identifier and owning dimension.
- Canonical symbol and formal name.
- Supported aliases/display labels and localized names.
- Exact relationship to the dimension's canonical reference unit where a simple conversion exists.
- Whether the unit is active, deprecated or restricted by a particular workflow/jurisdiction.
- Effective catalogue version and compatibility metadata.

Symbols and formal names are not Tenant-defined semantics. A Tenant may configure display labels, translations and approved business terminology without changing the canonical identity or dimensional meaning. Tenant-specific custom UOMs are allowed only as governed extensions within an existing compatible dimension or a registered new dimension; custom units cannot collide with or redefine canonical symbols/identifiers.

Prefer metric/SI-aligned units for the India-focused product. The litre is a widely used unit accepted for use with SI; the kilogram is the SI base unit of mass. The legal unit allowed for a particular trade, pre-packaged commodity, instrument or declaration must still be checked against applicable Indian Legal Metrology law/rules for that activity.

### 3.3 Pack, container and package content

Pack count and pack content are different values:
- 12 packs is a Count quantity.
- 500 mL per pack is a Volume content attribute.
- 6 L total declared content is a derived content value under a specific pack definition; it is not a substitute for the count of packs.

A PackDefinitionVersion identifies the SKU/package, count unit, content quantity/UOM, packaging level and effective period. A case/carton may contain a defined number of packs, and a pallet may contain a defined number of cases, but the hierarchy must be explicit and versioned.

Packaging conversion is a product-specific transformation, not a generic UOM conversion. Opening, breaking, combining, filling, repacking or unpacking packages must use a controlled stock/production event and preserve the source and resulting package identities/quantities. The system must not silently convert count into volume/mass or infer package content from a label alone.

## 4. Product/SKU base-UOM and transaction rules

### 4.1 Authoritative stock base UOM

- Every stock-managed SKU has one authoritative base UOM used by its stock ledger at any effective time.
- A Product may define a default base UOM that variants inherit only when the dimensions and handling semantics are compatible. An SKU-level assignment overrides the Product default and is authoritative for that SKU.
- Two SKUs under the same Product may legitimately use different base UOMs where their stock identity or packaging model differs (for example, individual sealed packs tracked by count versus bulk raw milk tracked by volume).
- Non-stockable services or fees do not require a stock base UOM.
- A SKU cannot have two simultaneous active base UOMs for the same stock ledger scope.
- The base-UOM assignment is effective-dated and versioned. A change never reinterprets historic stock movements. It requires a controlled conversion/migration/reconciliation plan, or a new SKU where the old and new stock identities are not safely convertible.

### 4.2 Transaction UOMs

Purchase, receipt, collection, sale, delivery, transfer, production and count workflows may accept a transaction UOM only if:
- The UOM dimension is compatible with the SKU's base dimension, or an explicitly approved contextual conversion/packaging transformation applies.
- The conversion is active for the transaction date, product/material, workflow, location and other required conditions.
- The exact source quantity/UOM and the normalized/base quantity are both retained.
- The applicable unit, precision profile, conversion version and rounding profile are recorded with the transaction.

A displayed alternate unit is not a change to the stock base UOM. Reports must disclose the unit basis and conversion used.

### 4.3 Milk mass/volume conversion

- Preserve the original milk measurement as recorded: volume or mass, UOM, device/method, timestamp, operator and calibration/source evidence where applicable.
- Do not convert litres to kilograms or kilograms to litres using a universal constant.
- A contextual conversion is permitted only where the business workflow requires it and an approved, versioned conversion rule defines the material/milk type, density or measurement basis, temperature/context where relevant, source and target UOM, effective dates, applicable conditions, precision, responsible approval and tolerance.
- Store the source value and derived value separately. A derived value must not claim greater measurement accuracy than the source instrument or method supports.
- If a required contextual factor/evidence is missing, the conversion is unavailable or the transaction remains in an explicit review state; the system must not invent a default density.

## 5. Conversion governance

### 5.1 Standard conversions

Conversions within the same dimension may use exact relationships from the canonical catalogue, for example:
- 1 kg = 1,000 g.
- 1 L = 1,000 mL.
- 1 t = 1,000 kg.
- 1 h = 60 min.
- 1 d = 24 h for duration arithmetic; calendar-day business rules still respect the configured calendar/time zone.

The conversion engine must preserve exact factors where available and avoid repeated conversion/rounding cycles.

### 5.2 Contextual and product-specific conversions

A conversion rule must declare:
- Source and target UOM/dimension.
- Exact factor or deterministic formula (including offset where applicable, such as temperature conversion).
- Applicable product/material/SKU, workflow, process, location, equipment or conditions.
- Effective-from/effective-to and immutable rule version.
- Required inputs (e.g. density, temperature, tare, concentration or pack definition).
- Source/evidence, verification/approval state and responsible authority.
- Calculation precision, rounding boundary and tolerance.
- Whether reverse conversion is valid and under what error/tolerance conditions.

A generic factor cannot cross dimensions. Packaging hierarchy uses PackDefinitionVersion and explicit packaging events, not the general dimensional conversion engine.

### 5.3 Conversion history and tolerance

- Every conversion result retains source value/UOM, target value/UOM, factor/formula, rule version, inputs/conditions, actor/system identity and timestamp.
- Updating a conversion rule applies prospectively. Existing transactions retain their historical version and result.
- A conversion round trip may show numerical drift; that drift must be measured, explained and tested. The system must not replace the original source with a rounded round-trip value.
- Device accuracy, measurement tolerance, business acceptance tolerance and numerical rounding remain distinct. An accepted variance does not authorize modifying source evidence.

## 6. Precision architecture and quantity rounding

### 6.1 Precision layers

| Precision layer | Meaning | Selected rule |
|---|---|---|
| Source/input precision | Actual digits/value produced by a device, instrument or human entry | Preserve the entered/source value and its stated resolution; do not invent extra accuracy |
| Instrument accuracy and calibration | Measurement uncertainty, tolerance and verification status | Governed per device/model, method and applicable process; number of displayed digits is not proof of accuracy |
| Stored precision | Precision needed to preserve the source and approved calculations | Must be sufficient for the maximum supported source resolution and conversions; no premature rounding |
| Calculation precision | Precision used in conversions, rates, yields, taxes and intermediate arithmetic | Exact decimal/fixed-point operations with deterministic precision and versioned rules |
| Posting precision | Precision committed to a stock, settlement, invoice or accounting record | Defined by the owning domain and effective posting/rounding policy |
| Display precision | Digits shown in entry forms, screens and reports | May be lower than stored precision, but must disclose unit and material rounding; display formatting never changes source values |
| Business tolerance | Acceptable operational variance, e.g. weighment or process yield tolerance | Versioned by product/process/device/business profile; never used as a rounding shortcut |

A single Tenant-wide/database-wide decimal scale is not suitable. The platform owns a governed precision catalogue by UOM, device/method, workflow and posting context. Tenants may select supported precision profiles and, where the domain permits, choose a narrower profile; they cannot exceed tested platform bounds, misstate device accuracy or weaken mandatory legal controls.

### 6.2 Quantity arithmetic

1. Use exact decimal/fixed-point semantics for all authoritative quantities and rates; binary floating-point must not be the source of truth.
2. Preserve source measurements before normalization, conversion, display or posting.
3. Keep measured, submitted, accepted, rejected, quarantined, converted, reserved, dispatched, delivered, returned, adjusted and posted quantities distinguishable.
4. Do not add, average or compare incompatible dimensions. Convert to a common compatible unit using a valid approved rule first.
5. Carry unrounded intermediate values to the documented finalization boundary. Avoid repeated rounding at every intermediate step.
6. Define the rounding mode, precision, order of operations and boundary explicitly for every workflow that finalizes a quantity.
7. Record manual quantity corrections as separate events with reason, actor, timestamp, approval where required, and before/after values. Do not edit the original source measurement.
8. A tolerance is a separate acceptance rule. A value inside tolerance is not silently rewritten to the target value.
9. Reports and analytics must specify UOM, conversion version, aggregation grain and whether values are source, normalized or derived.
10. Preserve all calculation inputs, unit/conversion/precision policy versions and final results needed for historical reproduction.

### 6.3 Workflow-specific precision boundaries

| Workflow | Required quantity distinctions |
|---|---|
| Milk collection | Gross, tare, net, measured, submitted, accepted, rejected and adjusted; source method/device/UOM and calibration |
| Quality testing | Parameter value and unit; missing, not tested, zero, invalid sample and failed result are distinct |
| Procurement | Ordered, received, accepted, rejected and invoiced quantities; purchase UOM and SKU base-UOM conversion |
| Inventory | SKU base UOM, lot/location/status, reserved/in-transit/available quantity and immutable movement |
| Processing | Planned/actual input, returned input, output, by-product, rework, loss and WIP; process stage and mass-balance basis |
| Packaging | Count, declared content per pack, pack hierarchy and total derived content as separate quantities |
| Sales/delivery | Ordered, allocated, picked, dispatched, delivered, returned, undelivered and billable quantities |
| Reports | Explicit UOM basis, conversion and aggregation rule; incompatible units cannot be combined |

## 7. Monetary precision and rounding

### 7.1 Currency and rate precision

- INR remains the initial product accounting currency; multi-currency is not a current product requirement.
- The ordinary INR currency posting baseline is rupees and paise (two decimal places) unless the applicable transaction-specific legal/accounting rule requires another treatment.
- Per-unit prices/rates, quality rates, conversion rates, cost rates and intermediate calculations may require greater precision than the final payable/receivable amount. Their supported precision is separately governed; do not prematurely round a rate to paise.
- Use exact decimal arithmetic for prices, rates, discounts, tax calculations, settlements, allocations and journal lines.
- For ordinary commercial calculations where no stricter legal/contract rule exists, the selected default rounding mode is decimal HALF_UP at the explicitly declared line/document/settlement/posting boundary. This is a configurable, versioned default, not a universal statutory tax rule.
- Negative values, credit notes, reversals and refunds must use a deterministic sign-symmetric policy and preserve the original posted calculation/version.

### 7.2 Statutory tax rounding boundary

Section 170 of the Central Goods and Services Tax Act, 2017 states that tax, interest, penalty, fine or any other sum payable, and any refund or other sum due under that Act, is rounded to the nearest rupee; fifty paise or more is increased to one rupee and less than fifty paise is ignored.

This provision is not a general rule that every selling price, commercial invoice line, customer account amount, farmer rate or internal stock value must be rounded to a whole rupee. The tax/domain rules engine must apply it only to amounts within its verified statutory scope and applicable transaction context. Exact document, tax-component, return/refund and state/central tax treatment remains governed by ODR-016 and current official legal review.

### 7.3 Rounding policy hierarchy

For an operation with no verified special statutory/accounting rule:
1. Preserve source quantities, rates and currency inputs at their accepted precision.
2. Carry exact decimal intermediates under a deterministic calculation policy.
3. Apply the effective, versioned commercial rounding profile at its declared line/document/settlement boundary using HALF_UP by default.
4. Post ordinary INR currency amounts to paise precision.
5. Record any permitted residual or rounding adjustment as an explicit, auditable result; never use an unexplained balancing plug.
6. Preserve the policy version, calculation inputs, pre-rounded amount, rounded amount and residual as needed to reproduce the result.

A verified statutory rule overrides a Tenant preference. A Tenant may select a different supported commercial rounding policy only where the owning domain explicitly permits it; no user can change the policy for an already finalized transaction.

### 7.4 Domain boundary rules

- Invoice/tax: exact calculation followed by the versioned tax/document rule applicable to the document type and jurisdiction. Line-vs-document and tax-component rounding must not be guessed; ODR-016 supplies legal applicability.
- Farmer settlement: rate chart, quality inputs, quantity basis, calculation and rounding policy are versioned; whether line or period aggregation is used is a Tenant-configurable commercial formula only within the approved domain contract. Recalculation after posting uses an adjustment/reversal, not an overwrite.
- Inventory costing/valuation: cost precision is distinct from physical quantity precision and currency posting precision. ODR-012/017 and accountant-approved valuation policy govern final cost layers and posting.
- Journals: each posted journal balances exactly at the approved currency posting precision. Rounding entries must follow a named, approved posting rule and retain source references.
- Partial payments/allocations: allocation and residual amounts cannot exceed the original source value; rounding residuals must be explicit and reconciled.
- Reversals: reproduce the original posted value and link to the original posting/policy version; do not recalculate a historic amount with today's configuration.

## 8. Tally and external-system precision compatibility

TallyPrime's current TallyHelp documentation (last updated 14 October 2026) states that UOMs support up to four decimal places; values/calculations exceeding the target precision are rounded by TallyPrime. Tally's currency setup documentation describes a default two-decimal configuration and permits configuration up to four decimal places. It also supports simple, compound and alternate units.

Therefore:
- SaaS precision is not capped globally at Tally's current documented limit. The internal canonical model remains vendor-neutral and may preserve greater supported precision where business/instrument needs require it.
- Each Tally adapter must detect the exact target company, product/release/build, UOM precision, currency precision, compound/alternate-unit capability and relevant mapping constraints before export/import.
- If a SaaS source value cannot be represented exactly by the target, the adapter must report the exact loss/rounding and either block the operation or use an explicitly approved, versioned mapping/aggregation/rounding policy with reconciliation evidence.
- No silent truncation, automatic reduction of a UOM's declared precision, or mutation of SaaS source values is allowed.
- Changes to Tally UOM precision after transactions exist must be treated as a compatibility/migration risk; prove the target behavior with disposable company data before claiming support.
- The external voucher payload carries decimal strings and policy/version references. It must not serialize authoritative amounts as binary floating-point.

## 9. Configuration, customization and extensibility

| Area | Classification | Rule |
|---|---|---|
| Canonical dimension identities, standard unit semantics, dimension compatibility, immutable source evidence, exact calculation semantics, audit and historical versioning | Fixed invariant | Cannot be changed by Tenant, plan, role, client or extension |
| Enabled UOMs, allowed transaction UOMs, validated precision profiles, SKU base-UOM assignment, pack definitions and permitted conversions | Tenant-configurable | Choose only platform-supported values and compatible dimensions; effective-date and version all changes |
| Device resolution/calibration, process tolerance, acceptance bands and measurement requirements | Workflow/device configurable | Must be evidence-based and cannot be inferred from display digits |
| Ordinary commercial rounding mode/boundary and rate precision within supported limits | Domain/Tenant-configurable | Versioned, permission-controlled and cannot override verified law or finalized records |
| Statutory tax rounding and regulated units/package declarations | Rule-configurable under compliance governance | Effective-dated, source-backed and reviewed; cannot be overridden by an ordinary Tenant preference |
| New dimensions, unit families, conversion strategies or external unit mappings | Extensible capability | Registered/versioned contract, explicit scope, test vectors, no collision with canonical semantics |

Configuration keys follow ARC-PV-001: owner, stable key, type/range/unit, default, applicable scope, inheritance/lock behavior, permission, validation, effective dating, versioning, audit, impact, rollback and historical snapshot behavior.

## 10. Worked scenarios

### Scenario A — raw milk measured by volume

A collection operator records a source-device reading of 25.375 L. The system retains the exact source value, UOM, device/method, timestamp, operator and source precision. Accepted volume is a separate quantity. If a later process needs mass, it requires an approved contextual density/temperature rule and retains the source and derived values separately. No universal litre-to-kilogram factor is applied.

### Scenario B — weight-based collection

The system records gross 18.420 kg and tare 2.150 kg; net 16.270 kg is a reproducible calculation under the same unit and precision. If the tare is later found incorrect, the original entry remains, and a correction event carries the new value/reason/approval. The settlement references the accepted quantity and calculation/policy version.

### Scenario C — packaged product

A sale of 12 bottles is a Count quantity. Each bottle's declared content (e.g. 500 mL) is a separate PackDefinition attribute. The system may derive total declared content for reporting as 6 L under the pack definition version, but it does not add 12 bottles directly to a stock quantity measured in litres or silently convert inventory without an explicit packaging event.

### Scenario D — farmer settlement

Collection entries retain the measured/accepted quantity, its source UOM, rate basis, quality inputs and effective rate-chart version. The settlement uses the approved Tenant formula and versioned rounding policy. The payable result is reproducible. A later correction creates a linked adjustment/reversal, not a rewrite of the collection or paid settlement.

### Scenario E — invoice and journal

A bill line uses the actual billable quantity, effective price and applicable tax rule. Commercial rounding and CGST Act section 170 tax rounding are kept separate. The invoice and resulting journal refer to the same calculation/tax-policy snapshot; the journal balances exactly at posting precision.

### Scenario F — Tally precision mismatch

A SaaS stock quantity or rate requires more than four decimal places but the configured TallyPrime UOM only supports four. The adapter does not silently truncate. It reports the incompatible precision, target release/company, source value and possible difference; export is blocked unless an approved explicit mapping policy resolves the loss and records a reconciliation acknowledgement.

## 11. Acceptance and verification scenarios

The future test suite must include:
1. Reject addition, comparison or generic conversion of incompatible dimensions.
2. Preserve a source device reading and its resolution/calibration metadata exactly.
3. Resolve Product default and SKU-level base-UOM inheritance; enforce one authoritative base UOM per stock-managed SKU/effective interval.
4. Validate transaction UOM against the SKU's base dimension and active conversion version.
5. Prove a conversion-factor update does not alter historical transactions.
6. Verify volume-to-mass conversion is unavailable without an approved contextual basis and retains both values when enabled.
7. Reconcile gross, tare and net calculations and their correction history.
8. Verify count and pack content are distinct; pack/unpack events are explicit, versioned and idempotent.
9. Test exact standard conversion factors, affine temperature conversion and round-trip drift/tolerance.
10. Test source precision, stored precision, calculation precision, posting precision and display precision independently.
11. Verify accepted/rejected/quarantined quantities without altering source measurement.
12. Verify rates with greater precision than final currency amounts.
13. Test ordinary HALF_UP commercial rounding at positive/negative tie boundaries and each declared line/document/settlement boundary.
14. Verify CGST Act section 170 rounding applies only to in-scope amounts and does not round every commercial invoice value to whole rupees.
15. Test credit notes, refunds, reversals, partial payments, allocations and residual handling.
16. Verify exact journal balance at posting precision and explicit rounding adjustment audit.
17. Verify Tally UOM/currency precision capability detection; unsupported precision blocks or requires explicit approved loss policy.
18. Verify Tally round-trip detects precision loss and never silently truncates.
19. Verify reports show UOM, conversion/profile version and aggregation basis.
20. Verify old transactions remain reproducible after UOM catalogue, precision, conversion, pack, rounding or statutory rule changes.

## 12. Remaining evidence and decision dependencies

- ODR-009/010: milk quality parameters, device methods, rate formulas and farmer settlement periods remain product/process/business configurable and may require legal/food-safety review.
- ODR-011/012: process mass-balance/yield tolerances, inventory costing/valuation and negative-stock exceptions remain process/accounting policy dependent.
- ODR-013/014/016: transaction-specific price, tax, billing-document and rounding boundaries require current official tax/legal verification; section 170 is recorded here only for its stated scope.
- ODR-017: accountant-approved valuation, books, journal recognition and reconciliation policies remain evidence-gated.
- Legal Metrology: exact permitted unit, instrument verification, pre-packaged declaration and trade-use applicability must be reviewed against the current Central/State rules and relevant product/activity before enforcement.
- Tally: target release/build, UOM/currency precision, XML/JSON/TDL behavior, compound/alternate units and mapping loss must pass a version-specific proof of concept.

## 13. Source and research basis

- BIPM, *The International System of Units (SI) — Base Units*: kilogram is the SI base unit of mass; second is the SI base unit of time. https://www.bipm.org/en/web/guest/measurement-units/si-base-units
- BIPM, *SI Brochure, Table 8 — Non-SI units accepted for use with the SI*: litre (L) and tonne (t) are listed among units accepted for use with SI. https://www.bipm.org/documents/20126/41483022/SI-Brochure-9-EN.pdf/2d2b50bf-f2b4-9661-f402-5f9d66e4b507?download=true&version=1.9
- Government of India, *The Legal Metrology Act, 2009*, sections 4, 5, 7, 10 and 11: metric system basis, base units, standard units and use/quotation in standard units. Applicability to exact trade/product context requires the current rules and qualified review. https://www.indiacode.nic.in/handle/123456789/15676?view_type=browse
- CBIC, *Central Goods and Services Tax Act, 2017*, section 170: nearest-rupee rounding for the specified amounts payable/refundable under that Act. This does not create a universal whole-rupee rule for commercial prices. https://cbic-gst.gov.in/hindi/CGST-bill-e.html
- TallyHelp, *How to Create and Alter Units of Measurement in TallyPrime*, last updated 14 October 2026: simple/compound/alternate UOMs and maximum four decimal places for UOM. https://help.tallysolutions.com/units-of-measurement/
- TallyHelp, *Set Up Base Currency Details in TallyPrime*: default two decimal places for currency, configurable up to four. https://help.tallysolutions.com/set-up-base-currency/

These sources inform the selected engineering baseline. The product must not make a final legal/tax/compliance determination from this analysis alone.

## 14. Consistency review

- Consistent with ERS quantity/stock integrity and financial integrity requirements.
- Consistent with inventory's requirement to preserve original quantity and conversion details.
- Consistent with milk collection's gross/tare/net and no-universal-density-conversion rule.
- Consistent with processing's explicit units, measurement basis and configurable yield tolerance.
- Consistent with accounting's exact decimal arithmetic, balanced journals and immutable posting history.
- Consistent with billing's effective-dated pricing/tax snapshots and separate statutory rules.
- Consistent with Tally's stable mapping and requirement to disclose unsupported/lossy fields.
- No physical schema, SQL type, database decimal scale, legal opinion, commercial rate or statutory rule beyond the cited provision is prescribed.
