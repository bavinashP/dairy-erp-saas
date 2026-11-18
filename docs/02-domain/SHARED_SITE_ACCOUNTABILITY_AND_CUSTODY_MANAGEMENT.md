# Shared-Site Accountability and Custody Management

**Document ID:** DOM-CUST-001  
**Status:** Recommended domain baseline selected on 2026-11-19 under the product owner's delegated decision mandate  
**Authority:** ODR-002 sections 16.1–16.7 and 16.36; ARC-PV-001; ARC-IAM-001; ARC-AUTH-MATRIX-001; Inventory Stock Ledger; Batch/Lot Traceability; Business Rules Catalogue; ERS-001.  
**Purpose:** Define a configurable, evidence-led way to record physical custody, handovers, shared facilities, equipment use and incidents across co-located Branches, while preserving separate ownership, inventory control, workforce authority, accounting and legal accountability.

## 1. Product and operating-profile objective

The product must support one owner-operated dairy, a small counter retailer, a milk collection centre, a delivery-focused business, a processor/packer, and a large multi-Branch or multi-site enterprise without separate product forks.

Custody assurance must scale by business need:
- A small low-risk shop may use a single-operator workflow with a timestamped capture and a simple handover note.
- A growing dairy may require named custodian acceptance, quantity/condition checks and evidence for transfers between people or locations.
- A multi-Branch dairy may require source/receiver acknowledgement, carrier custody, container/lot identification, discrepancy review and cross-Branch approval.
- A high-risk cold-chain, bulk-milk or traceability workflow may additionally use calibrated sensor readings, seal/container identifiers, witness evidence, automatic exception holds and independent review.
- The customer enables only the capabilities and assurance level required by its actual operating profile. A retailer that only resells shelf-stable packaged goods must not be forced to configure tanker, chilling-centre or milk-collection custody workflows.

The same canonical custody model applies at every scale. Configuration changes the required evidence and workflow assurance, not the meaning of ownership, custody or historical events.

## 2. Non-negotiable conceptual distinctions

The following concepts MUST remain separate even when the same person or Branch performs several of them:

| Concept | Meaning | Does not imply |
|---|---|---|
| Economic/legal ownership | The party/Branch/entity that owns the goods or asset under its governing commercial/legal arrangement | Physical possession, permission to handle, or final legal liability |
| Stock ownership / inventory attribution | The Branch or inventory scope to which stock balance and stock movements are attributed in the SaaS business ledger | That its employee currently holds the stock physically |
| Stock ledger control | Authority to recognize validated quantity movements and maintain the authoritative stock record | Physical possession or unrestricted adjustment rights |
| Physical location | The actual premises, vehicle, room, tank, compartment, shelf, bin or other place | Ownership or access to everything located there |
| Physical custody | The current recorded holder or accountable custody party for identified goods, assets or containers | Ownership, broad data access, stock adjustment, or automatic employment liability |
| Handling / execution | The person or service that actually performed a specific action | Continuous custody before or after that action |
| Workforce assignment | The authorized allocation of a worker to a duty, shift, Branch, site or time | Stock ownership or stock permission |
| Permission / task grant | The explicit authority to view or perform a defined action within a scope | Proof that a physical handover actually occurred |
| Approval / review | A separate decision made by a principal with the required explicit permission | Physical receipt or custody acceptance unless expressly captured as that event |
| Asset ownership / stewardship | Ownership and operational responsibility for a reusable asset/resource | Permission to use, repair, move or write it off |

A StockMovement records a stock quantity/location/status effect. A CustodyEvent records a physical custody fact. They may reference the same business operation, but neither record substitutes for the other.

A custody handover may occur without a stock quantity change or a StockLocation change. Conversely, a stock movement may occur while the same person remains custodian. Where physical movement changes a controlled StockLocation, the stock ledger must also record the applicable movement under inventory rules.

The product records operational facts and evidence. It MUST NOT infer or adjudicate negligence, employment misconduct, contractual liability, insurance liability, legal ownership or payroll deductions from a custody record alone.

## 3. Custody coverage and applicability

Custody tracking is available for controlled items/resources such as:
- Raw milk, packaged milk, dairy products, ingredients, packaging materials and consumables.
- Identifiable lots/batches, containers, milk cans, reusable crates, seals and delivery containers.
- Stock in cold rooms, tanks, bulk milk coolers, shelves, bins, loading bays, vehicles and transit.
- Shared equipment such as milk analysers, weighing scales, chillers, generators, POS devices, tools and maintenance equipment.
- Cash drawers, petty-cash floats or payment devices only through their applicable cash/financial custody domain; this document does not replace those domain controls.

A Tenant configures which item classes and workflows require custody records. The configuration may be inherited or narrowed at Branch/site/StockLocation/resource scope only where the relevant configuration key permits it. Mandatory custody tracking for an enabled high-risk process cannot be disabled by a user preference or lower-scope override.

No custody record is required merely because an item exists in a Tenant. The system should avoid unnecessary handover steps for low-risk situations where a simpler stock or task record provides sufficient control.

## 4. Core custody model

The logical model must support the following concepts; this is a domain specification, not a database schema prescription.

- **CustodySubject:** The goods, stock quantity/lot, container, asset or resource whose custody is being recorded.
- **CustodyAssignment:** The effective assignment of a named person, service identity or external custody party as the recorded custodian for a defined subject/scope and period.
- **CustodyEvent:** An append-oriented fact such as received, placed in storage, picked, sealed, dispatched, loaded, unloaded, transferred, counted, inspected, cleaned, quarantined or handed over.
- **CustodyHandover:** A linked offer/dispatch/receipt/acceptance process between identified custody parties.
- **CustodyEvidence:** Structured readings and evidence references such as quantity, condition, temperature, seal, scan, signature/acknowledgement, calibrated-device reference, photograph or document.
- **CustodyIncident:** A discrepancy, loss, damage, contamination concern, temperature excursion, missing acknowledgement, equipment failure or other custody exception.
- **SharedResourceUse:** A bounded usage record for a resource made available to more than one Branch or operational capability.
- **ResourceAvailability / Maintenance Context:** Availability, cleaning, calibration, inspection and maintenance state relevant to permitted use.

Each record is tenant-bound, scoped to the owning/using Branch or explicitly shared relationship, time-aware, permission-checked and auditable. A custody party may be an internal named user, an authorized service identity, or an external carrier/supplier/customer where that party's acknowledgement is captured through an approved channel. An external party record does not automatically become a login account or receive Tenant access.

## 5. Custodian and accountability rules

1. A custody assignment names the person/service/party recorded as holding the subject, the subject/scope, location, effective start and, when ended, effective end.
2. The actual handler/operator is recorded separately for each event when different from the custodian.
3. The originating and receiving Branches are recorded where a cross-Branch operation applies; stock ownership remains attributed to the stock-owning Branch unless a separate approved ownership-transfer workflow changes it.
4. A supervisor/job title is not a custody authority. Any action to create, accept, amend, close, dispute or review a custody record requires the exact effective permission and scope.
5. The current custodian may be the worker, a named carrier, a contracted service provider or another explicitly recorded party. The system must not assume the sender remains custodian after dispatch.
6. A task assignment or StockTaskAccessGrant does not prove that goods were physically received. Handover evidence is required according to the applicable custody assurance profile.
7. Custody records do not grant unrelated Branch visibility or stock mutation permission.
8. One person may be both custodian and handler in a small business, but their separate event roles remain explicit in the record.
9. Multiple co-custodians may be represented only where the workflow genuinely requires shared control. Do not create ambiguous responsibility by assigning an undifferentiated group where one accountable party is needed.
10. A custody assignment ending or a worker's permission expiring does not erase physical possession. Where a person still holds goods, the record must preserve that fact until a real handover, verified return, or controlled incident resolution occurs.

## 6. Configurable custody assurance profiles

A Tenant may choose an assurance profile per workflow, subject class or risk category. Starter profiles are editable templates:

| Profile | Intended use | Minimum evidence |
|---|---|---|
| Basic single-party capture | Low-risk internal handling or a very small owner-operated business | Named actor/custodian, subject, location, timestamp, action and reason/reference |
| Acknowledged handover | Inter-Branch movement, carrier transfer, shared equipment or material stock handover | Sender and receiver/custody-party identity, dispatched and received times, subject/quantity/condition, location and acceptance/dispute |
| Verified high-assurance | Bulk, perishable, cold-chain, high-value, controlled or traceability-sensitive operations | Acknowledged handover plus configured independent readings/evidence such as calibrated temperature/weight, lot/container/seal identifiers, scan, witness or device evidence, exception review |

The chosen profile must be effective-dated and versioned. A profile change applies prospectively unless a separately approved migration/reprocessing policy says otherwise. Completed events preserve the profile and configuration version used at the time.

A Tenant may require different evidence for different products, process stages, locations, customers or supplier arrangements. Do not make a high-assurance profile mandatory for every workflow merely because the platform supports it.

The following minimum invariant applies to every custody event that the Tenant has chosen to track: identify **who**, **what**, **where**, **when**, **why/action**, and the resulting custody/status outcome. The applicable subject-specific fields and evidence requirements are validated by the configured profile.

## 7. Handover lifecycle

The default controlled handover lifecycle is:

**Draft → Offered/Prepared → Dispatched/Handed Over → In Transit or Handover Pending → Received Pending Verification → Accepted / Accepted with Discrepancy / Rejected or Disputed → Closed or Exceptioned.**

Not every step is required for every workflow. A same-site handover may move directly from handover to receipt pending verification; a transport workflow uses an in-transit stage.

### 7.1 Initiation and offer
- Identify the exact subject, source custody party, intended receiving party, origin/destination and applicable Branch/StockLocation.
- Capture expected quantity/UOM, lot/batch/container/asset identity and condition.
- Check that the initiating actor has the required permission, task assignment and, for cross-Branch stock handling, a valid approved StockTaskAccessGrant.
- Freeze the handover version when dispatched; material changes require a controlled amendment or cancellation before dispatch, not silent editing.

### 7.2 Dispatch and transit
- Record actual dispatch/hand-over time, operator, carrier/vehicle/route where applicable, container/seal references, observed quantity and required condition/readings.
- Record the party that explicitly accepts custody at dispatch. Do not assume the carrier accepted custody merely because a vehicle was selected.
- If goods move through more than one carrier or holding point, record each actual handover as a separate linked custody event.
- Keep stock in the stock ledger's appropriate in-transit state when the inventory transfer policy requires it; custody location and stock accounting state remain linked but separate.

### 7.3 Receipt and acceptance
- The receiving party records actual receipt time, quantity, UOM, lot/container identity, condition and required evidence.
- Compare expected and actual quantities/condition without silently altering the dispatched values.
- Acceptance completes the custody handover only for the quantity/subjects actually acknowledged.
- Partial receipt creates accepted and outstanding/disputed portions; it must not falsely close the whole handover.
- A receiver's rejection records a reason and the actual current custodian or controlled holding status. It does not presume an automatic return to the sender.

### 7.4 No acknowledgement or disputed handover
- No-response, timeout, missing device connectivity or failed notification MUST NOT imply acceptance.
- Keep the handover pending/disputed and preserve the last verified custodian and the known physical location. Do not create a custody gap.
- A separately permissioned authorized user may record an alternative resolution only with evidence and a reason. The alternative resolution is not presented as the recipient's acknowledgement.
- A handover may be administratively closed as an exception only when the actual custody situation is documented and an accountable party/reconciliation path is recorded.

## 8. Stock, lot and container custody

A custody subject may refer to:
- An entire lot/batch or a measured quantity slice of it.
- An identified container/can/crate/tanker compartment holding one or more lots.
- A packaged item/serialised asset.
- A bulk quantity held in a tank, silo or other controlled location where exact physical separation may not exist.

The system must preserve the relationship between goods and their container/holding context. If a container carries more than one lot or a lot is split across containers, the recorded lineage must preserve the quantities and aggregation/disaggregation relationships without double-counting stock.

Returnable cans, reusable crates, pallets and seals are tracked as containers/custody subjects with their own condition/return/deposit lifecycle where needed; they are not automatically treated as saleable stock. A vehicle is a transport/custody resource by default and becomes a StockLocation only where it physically holds controlled stock and the Tenant's inventory policy explicitly models it that way.

Physical custody changes never silently change economic ownership, stock-owning Branch, inventory valuation or financial posting. Any ownership transfer, stock adjustment, write-off or cost allocation must use its separate approved domain workflow and permissions.

## 9. Perishable goods, milk chain and quality evidence

For raw milk and other perishable goods, custody evidence may need to follow the full chain: collection/producer or supplier → collection point → can/container → bulk milk cooler/tank/compartment → carrier/tanker → receiving facility → accepted lot/storage → processing or disposition.

Where applicable, capture:
- Collection, loading, dispatch, arrival and unloading times.
- Quantity, UOM, lot/collection-session/producer references and container/tanker compartment.
- Temperature at defined control points, source device/manual method, calibration/verification reference and reading time.
- Seal/container identity and integrity, cleaning/sanitation evidence where required by the workflow.
- Sampling/testing references, quality status, contamination/damage/temperature excursion and acceptance/quarantine/rejection decision.
- The rule/profile version under which the reading and disposition were assessed.

Temperature, cooling-time, sanitation, sampling and shelf-life rules MUST be supplied by an effective-dated product/process/tenant handling profile and verified legal/customer standard where applicable. Do not hardcode one universal temperature or elapsed-time limit for all dairy products, milk types, ambient-stable goods, transport modes and legal contexts. Preserve actual readings and source evidence; do not replace a measured out-of-range value with a corrected or assumed value.

If required evidence is missing, invalid or outside the configured limits, the configured policy may block acceptance, place the material on hold/quarantine, require retest or escalate for authorized review. The system must not automatically convert the exception into a stock write-off, supplier claim, payment deduction or employee liability.

## 10. Shared facilities, equipment and other resources

Shared physical resources may include a Bulk Milk Cooler, chilling tank, milk analyser, weighing scale, tanker, loading bay, cold room, generator, cleaning equipment, POS register, delivery vehicle, reusable containers or maintenance tools.

For each tracked resource distinguish:
- Legal/business owner and asset-register owner.
- Current physical location and availability.
- Authorized Branches/capabilities allowed to use it.
- Named custodian or operational steward, where applicable.
- Booking/reservation or usage allocation, where simultaneous use is constrained.
- Actual use event: actor, Branch/capability, start/end, purpose, meter/reading or quantity where relevant.
- Cleaning, sanitation, calibration, inspection, repair, maintenance and downtime state as applicable.
- Any approved charge/cost allocation and its separate accounting treatment.

Shared resource availability does not grant access to another Branch's stock, records, staff, finance or customers. Resource use does not transfer asset ownership. A custody record does not authorize maintenance, repair, calibration, disposal or write-off; those require the relevant asset/work-order permissions.

For shared utilities or costs (electricity, water, refrigeration, common labour or transport), a Tenant may configure a supported allocation basis such as meter readings, elapsed usage, quantity, agreed proportion or reviewed manual allocation. The basis, period, source readings, formula/version, affected Branches and approval must be recorded. The allocation is an operational calculation only until the appropriate financial workflow approves and posts it. Do not infer contractual liability or automatically debit a Branch/employee.

## 11. Incidents, discrepancy and disposition workflow

A custody incident may cover quantity mismatch, missing goods, wrong lot/container, damaged packaging, contamination suspicion, temperature excursion, broken seal, missing acknowledgement, unapproved access, equipment failure, unsanitary condition, loss, theft allegation or disputed responsibility.

Recommended lifecycle:

**Reported → Contained (where needed) → Evidence Captured → Review/Investigation → Disposition Decision → Approved Correction/Compensation (if required) → Closed.**

Rules:
- Preserve original dispatch, receipt, reading, custody and stock events.
- Separate factual observation from allegation, assessment, decision and final disposition.
- Allow immediate protective action such as isolation/quarantine where the actor has the specific permission.
- Require an authorized reviewer and independent approval where the risk/amount/domain policy requires it.
- Record accepted quantity, rejected/damaged quantity, missing quantity and unverified quantity separately.
- Reconcile physical facts with stock movements and accounting effects through linked, separately permissioned correction/claim/refund/write-off workflows.
- Do not auto-adjust stock, create a supplier deduction, issue a customer credit, post a loss, write off an asset or deduct payroll merely because a custody discrepancy was reported.
- Record the final outcome, responsible decision authority, supporting evidence and any unresolved legal/contractual dispute.
- A reported incident can be closed operationally while a distinct insurance, legal or commercial claim remains open, provided the relationship and unresolved status are clear.

## 12. Permission, privacy and Branch isolation

Every read and action uses the permission-first model and current resource scope. Relevant permission families may include:
- custody.view / custody.view_sensitive_evidence
- custody.assignment.create / custody.assignment.revoke
- custody.event.capture
- custody.handover.offer / dispatch / receive / accept / dispute / resolve
- custody.incident.report / review / resolve
- shared_resource.view / reserve / release / use.capture
- asset.cleaning.capture / calibration.capture / maintenance.submit / maintenance.approve

These are candidate semantic families; the canonical stable keys and complete catalogue entries are governed by ARC-IAM-CAT-001 and ODR-005. Job titles and account categories never grant them.

A worker or carrier sees only the minimum subject/task/evidence information needed for the assigned custody action. Shared-site visibility must not expose unrelated customer, supplier, employee, price, financial or inventory information belonging to another Branch. Sensitive evidence and attachments require separate read/download/export permissions.

Custody permission, stock permission, workforce assignment, physical custody assignment and stock-task grant are independent controls. A valid custody handover does not grant stock adjustment rights. A stock task grant does not itself assign a worker to a shift or prove physical receipt.

## 13. Grant expiry, revocation and in-progress physical custody

The approved ODR-002 zero-grace rule remains in force: expiry or revocation immediately blocks new tasks and further stock-affecting actions under the grant. It does not pretend that goods physically held by a person have disappeared or automatically transferred to someone else.

Where a worker still physically holds goods or equipment after access is revoked:
1. Preserve the last verified custodian and actual location.
2. Block new stock mutations and unrelated data access; keep the task under access/custody hold.
3. Permit only minimum safety/incident reporting and evidence capture where a separately valid permission/policy allows it.
4. Require a real handover to another currently authorized custodian, a safe return/secure holding action, or a controlled incident resolution.
5. Do not automatically reassign physical custody, complete/rollback the task, release a stock hold, or write off missing quantities.
6. The stock-owning Branch retains stock ownership and the approved grant/reconciliation authority; any further stock movement requires current permission and authorization.

The recorded custody chain therefore remains continuous even when the right to perform further business operations has ended.

## 14. Configuration, customization and extensibility classification

| Area | Classification | Rule |
|---|---|---|
| Tenant/Branch/stock ownership, immutable source events, authorization, audit and no silent custody transfer | Fixed invariant | Cannot be weakened by plan, user preference or customer extension |
| Required assurance profile by workflow/risk, evidence fields, quantity/condition tolerances, temperature limits, acknowledgement steps and escalation thresholds | Configurable policy | Versioned, effective-dated, scoped and validated; must not contradict verified legal/product safety constraints |
| Labels, local terminology, forms, handover templates, checklists, dashboards and evidence display | Customizable presentation | Upgrade-safe and must retain canonical event semantics |
| Sensor, weighing-scale, milk analyser, barcode, tanker, GPS, Tally, notification and external logistics adapters | Extensible capability | Versioned contract, own identity, limited permissions, tenant binding, resource limits and revocation |
| Legal ownership, insurance, employment liability, statutory retention and contractual responsibility | External/legal dependency | Must not be guessed; record relevant external agreement/evidence and obtain qualified review when applicable |

Configuration keys must follow ARC-PV-001: owner, stable key, type/range/unit, default, applicable scopes, inheritance/lock behavior, permission, validation, effective dating, versioning, audit, impact, rollback and historical snapshot behavior.

## 15. Audit, historical integrity and reporting

Every custody assignment, event, handover, acknowledgement, dispute, incident, decision, resource booking/use, evidence attachment and correction must record, as applicable:
- Actor/handler, actual custodian, sender, receiver, reviewer/approver and external party separately.
- Tenant, source/target Branch, location/StockLocation, resource/subject, task/grant and relevant permission decision/version.
- Event/action, quantity/UOM, lot/container/asset, condition/status, business timestamp, server-recorded timestamp and source/device.
- Reason, evidence references, configuration/assurance profile version, approval and exception outcome.
- Correlation/idempotency identifiers and links to source stock movement, purchase/collection/transfer, work order, incident or financial correction.

Events are append-oriented. Corrections add a linked correcting event; they do not overwrite the original custody history. Preserve the distinction between observed fact, user-entered assertion, sensor/device reading and later reviewed conclusion.

Custody reports may include current custodian/location, overdue/pending handovers, in-transit items, unacknowledged discrepancies, cold-chain/equipment exceptions, resource utilization/downtime, returnable-container balances and custody incident aging. Reports, exports and drill-downs remain permission- and Branch-scope aware. Do not present unresolved assertions as confirmed facts.

## 16. Acceptance scenarios

1. A one-person retailer records a simple stock receipt/sale without being forced into a two-person handover.
2. A small dairy uses one shared location while retaining separate stock ownership and scoped access for each Branch.
3. A cross-Branch worker has a valid StockTaskAccessGrant but cannot claim receipt until the required custody evidence/acknowledgement is recorded.
4. A sender dispatches goods to a carrier; custody does not transfer until the carrier explicitly accepts under the selected assurance profile.
5. A partial receipt records accepted and outstanding quantities separately and does not silently close the handover.
6. A receiver rejects damaged goods; the system records actual custodian/location and a review path without assuming an automatic return.
7. Raw milk custody retains collection, container, chilling, tanker, temperature/sample evidence and receiving acceptance lineage where the Tenant enables that workflow.
8. A temperature excursion creates the configured hold/review path and never automatically posts a stock write-off or supplier/employee deduction.
9. A custody event occurs without a stock movement; the stock ledger remains unchanged.
10. A stock location movement occurs and the stock ledger and custody chain are linked without double-counting quantity.
11. A shared analyser/chiller is used by two Branches; usage, cleaning/calibration/downtime and any approved cost allocation remain separate from asset ownership and stock access.
12. A grant expires while a worker physically holds stock; the task is held, current custody remains recorded, and further stock mutation is denied until valid reauthorization/reassignment.
13. A missing acknowledgement, stale device or failed notification does not imply acceptance or create a custody gap.
14. Revoked permissions, expired grants and cross-Branch scope limits are enforced across API, mobile/barcode, reports, attachments, exports, jobs and integrations.
15. A correction preserves the original event and adds linked evidence/decision.
16. High-assurance workflows require the configured second-party/device/witness evidence; low-risk workflows remain proportionate and usable.
17. A custody discrepancy never directly causes an unapproved financial journal, supplier deduction, payroll deduction, write-off or legal-liability conclusion.
18. Historical custody reports reproduce the configuration and assurance profile used at the time of the recorded event.

## 17. Evidence and research notes

The design uses event-based traceability: GS1 describes Critical Tracking Events (such as receiving, packing, shipping and transporting) and Key Data Elements describing the actual event instance. A practical event record should preserve who/what/where/when/why and link the relevant subject, quantity, location and transaction. [GS1 Global Traceability Standard overview](https://support.gs1.org/support/solutions/articles/43000734535-what-is-the-gs1-traceability-standard) and [GS1 Traceability Checklist](https://www.gs1.org/docs/traceability/GS1_Traceability_Checklist_Assessors_Guide_V1.0.pdf).

NDDB describes prompt chilling of raw milk, use/cleaning of bulk milk coolers and appropriate insulated transport; it also describes testing again on receipt at the processing unit. These are useful operational design inputs, not a universal product rule for every dairy or a substitute for applicability review. [NDDB — Storage of Raw Milk and Transportation](https://nddb.coop/ccnddb/node/30).

FSSAI's 2019 Milk and Milk Products FSMS guidance addresses village collection, MCC/BMC, clean equipment/containers, chilling and transport integrity. It is guidance based on the food-safety framework and must not be presented by this domain document as a final legal applicability opinion for every Tenant. [FSSAI — Food Safety Management System Guidance Documents](https://www.fssai.gov.in/business/guidance) and [Milk and Milk Products Guidance PDF](https://mail.fssai.gov.in/upload/uploadfiles/files/Guidance_Document_Milk_14_03_2019.pdf).

Codex CXC 57-2004 provides a milk-specific hygienic practice reference. Product transport should use time/temperature combinations that preserve safety and suitability; exact control values depend on product and applicable requirements. [Codex Code of Hygienic Practice for Milk and Milk Products — reference copy](https://iawg-fsq.org/docs/be9afa1c-00f2-467e-b68c-46da3b2336ab) and [FAO text](https://www.fao.org/4/j2308e/j2308e02.htm).

These sources inform workflow evidence and risk-aware design. Statutory applicability, current amendments, mandatory limits, record-retention periods, contractual liability and insurance obligations remain subject to separate authoritative/legal review under the project scope.

## 18. Related documents

- Open Decisions Register — ODR-002 section 16.36.
- Entity Relationship Specification — custody and shared-resource conceptual relationships.
- Core Domain Entity Catalogue and Canonical Business Glossary.
- Inventory, Stock Ledger and Reconciliation.
- Batch, Lot, Expiry and Traceability.
- Milk Collection, Quality and Farmer Settlement.
- Dairy Processing, Packaging and Yield.
- Asset/Maintenance requirements in FRS and ERS.
- Exception, Reversal and Recovery Matrix.
- Business Rules Catalogue.
- Permission Catalogue and Assignment Governance.
- Product Configurability, Customization, Extensibility and Scale Policy.
