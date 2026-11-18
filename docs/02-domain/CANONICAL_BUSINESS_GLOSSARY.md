# Canonical Business Glossary

**Status:** Working baseline — terminology control  
**Authority:** Product Vision and Scope governs product intent. This glossary standardizes terms; it does not silently settle open decisions.

| Term | Working definition | Distinction / rule |
|---|---|---|
| SaaS Platform | The software service operated by the platform owner | Separate from each dairy tenant's business |
| Platform Owner | Organization/person operating the SaaS product | Platform-level authority; not a dairy tenant owner |
| Tenant | Isolated customer/business workspace within the SaaS | Tenant is a technical and commercial boundary; it is not automatically a legal entity |
| Dairy Business | The real-world dairy operation using the product | May have one or multiple operating locations |
| Tenant Owner | Person accountable for a tenant's dairy business | May personally perform operational duties |
| Branch | A tenant-owned management/operating unit within a dairy business | May be associated with multiple OperatingLocations; it is not itself necessarily a physical premises |
| OperatingLocation | An actual physical/operational site or premises owned by one Tenant | May be unassigned or used by one or more Branches through explicit governed, effective-dated associations; one Branch may use multiple OperatingLocations; sharing a site does not merge Branch stock or grant cross-branch data access |
| Operational Capability | A function enabled/performed at an OperatingLocation, such as retail sales, milk collection, storage, processing or packaging | Multiple capabilities may coexist at one physical site; capability labels do not imply separate premises |
| Outlet / Retail Counter | Retail/counter-sales capability or the relevant counter area at an OperatingLocation | Does not require a separate OperatingLocation when retail occurs at a shared premises |
| Collection Centre | Milk-receiving capability or designated receiving area at an OperatingLocation | Does not require a separate OperatingLocation when collection occurs at a shared premises |
| Warehouse / Storage | Storage capability or designated storage area at an OperatingLocation | A physical site may also support retail, collection or processing |
| Stock Location | Inventory-control holding position or subdivision, such as a refrigerator, room, shelf, bin or zone | Separate from OperatingLocation; it need not be an independently addressable physical site. When Branches share a physical site, stock positions, ownership and control remain separately attributable to each Branch; physical co-location does not grant cross-branch access |
| Processing / Packaging | Processing or packaging capability at an OperatingLocation | Does not require a separate OperatingLocation when performed at a shared premises |
| Delivery Area / Service Area | Geographic area in which one or more authorized branches serve customers | A Branch may serve multiple ServiceAreas and a ServiceArea may be served by multiple Branches through an explicit effective-dated association; reassignment must not rewrite historical delivery context. Geographic coverage is not a physical OperatingLocation or Branch |
| Route / Manual Route | Branch-owned reusable or one-off manual ordered stop sequence and ServiceArea coverage | Each Route belongs to exactly one Branch; it may cover only ServiceAreas effectively assigned to its Branch. Another Branch serving the same area uses its own Route. Route/DeliveryRun Branch ownership must match; a small Tenant need not maintain reusable templates or an optimizer |
| Delivery Run | Actual execution of one Branch-owned Route/route plan for a date/shift | References one Branch and a DeliveryPlan; the Route/Plan ownership must match; route, manifest, participants and resource versions are preserved at dispatch |
| Party | Tenant-owned counterparty identity root for a person or organization that may hold one or more business roles | Shared identity does not imply unrestricted branch visibility; see DBR-003 |
| Party Role | Effective business role assigned to a Party, such as customer, supplier, farmer/milk producer or vendor | Role-specific accounts, balances, terms and lifecycle remain distinct |
| Customer | Party acting as a buyer under a customer relationship/account | No customer self-service login is in current product scope |
| Supplier | Party providing goods/services under a supplier relationship/account | A supplier may also hold customer or farmer roles |
| Farmer / Milk Producer | Party acting as a raw-milk producer/supplier | May be an individual or organization; collection and settlement use measured quantity and approved quality/rate rules |
| Product | Sellable or procurable good/service definition | Product identity is distinct from a stock lot or package instance |
| Variant | Defined product option such as size, fat category, flavour, or pack | Only use when it has a distinct operational/commercial identity |
| SKU | Stock-keeping identity used to distinguish inventory-managed items | SKU policy and uniqueness scope require final design |
| Unit of Measure (UOM) | Standard measurement such as litre, kilogram, piece, or pack | Conversion and precision must be explicit and controlled |
| Pack Size | Declared quantity/content of a packaged product | Not interchangeable with selling unit or stock base unit |
| Subscription | Customer agreement/configuration for recurring supply | Not itself a delivery, sale, invoice, or payment |
| Subscription Schedule | Rule describing when and how much is planned for supply | Generates dated occurrences; must be versioned/effective-dated |
| Schedule Occurrence | One dated, shift-specific planned supply instance | Can be changed, skipped, fulfilled, or exceptioned under policy |
| Shift | Operational time window, e.g. morning or evening | Tenant-configurable; avoid hardcoding only two shifts |
| Skip / Pause | Customer-authorized or staff-recorded suppression of planned supply | Must record effective dates, reason/source, and cut-off outcome |
| Fulfilment | Recorded outcome of supplying an order/occurrence | Actual quantity can differ from planned quantity |
| Delivery Attempt | An attempt to complete a delivery stop | Not equal to successful fulfilment |
| POS Sale | Sale recorded at a counter/register | May be immediate-paid or account/credit sale subject to policy |
| Order | Request to supply specified goods at a future or defined time | Distinct from recurring subscription schedule |
| Invoice | Tax/commercial document issued under applicable policy | Not every internal monthly statement is necessarily a tax invoice |
| Customer Statement | Period summary of transactions, charges, credits, and receipts | Statement and invoice have different legal/accounting meanings |
| Receivable | Amount owed to the dairy by a customer | Derived from posted financial events and adjustments |
| Receipt | Record that money was received or acknowledged | Receipt capture does not by itself prove bank settlement |
| Payment Method | Cash, UPI QR, bank transfer, card, or other means used to pay | Method is distinct from payment gateway/provider |
| Payment Gateway | Third-party service processing online payments | UPI QR or direct bank transfer does not imply gateway integration |
| Reconciliation | Matching recorded transactions with external evidence/settlement | Manual reconciliation must remain supported |
| Procurement | Acquisition of milk, products, services, or materials | Milk collection may require a specialized workflow |
| Milk Collection | Recording raw milk received from a farmer/producer | Capture quantity, time, source, and relevant quality evidence |
| Quality Test | Measurement/assessment of a sample or product | Parameters and acceptance policy depend on product/process |
| Milk Collection Entry | Evidence record of raw milk received from a producer | Distinguish submitted, measured, accepted and rejected quantities |
| Sample | Material portion taken for testing | Must retain linkage to source collection/lot |
| Quality Result | Recorded value and outcome for a test parameter | Not tested, missing, zero and failed are distinct states |
| Rate Chart | Versioned rule for calculating supplier/milk procurement rates | Effective-dated; historical calculations retain applied version |
| Farmer Settlement | Periodic calculation of amount payable to a farmer/producer | Separate from individual collection and payment transaction |
| Quarantine | Restricted stock status pending review or test | Not available for normal allocation until released |
| Raw Milk Lot | Traceable group of accepted raw milk | May aggregate multiple collection entries under lineage rules |
| Raw Milk | Milk received before the tenant's processing/packing step | Distinct from packaged finished goods |
| Batch / Lot | Traceable group of material or products sharing production/receipt identity | Traceability depth depends on activity profile and applicable rules |
| Processing | Transformation of raw materials into another product | Optional capability; requires yield, loss, and traceability controls |
| Stock Movement | Authoritative posted change in stock quantity/location/status in the inventory ledger | Distinct from physical custody; link a CustodyEvent where both facts occur, without duplicate stock effects |
| Stock Ledger | Authoritative record of posted stock movements | Stock balances are derived from controlled movements |
| Stock Status | Availability classification of inventory | Available, reserved, quarantined, damaged, expired and in-transit must remain distinct |
| Stock Reservation | Commitment of available stock to planned demand | Not automatically a physical issue or sale |
| Stock Transfer | Controlled inventory transaction moving stock between authorized stock locations or ownership scopes | Not a sale or purchase; stock-ledger dispatch/receipt and physical custody handover are linked but distinct events |
| Physical Stock Count | Recorded physical quantity verification | Variances require controlled adjustment and audit |
| Supplier Batch | Original batch reference assigned by supplier/manufacturer | Not assumed globally unique |
| Production Batch | Traceable execution of a defined production process | Retains process version, inputs, outputs and deviations |
| Finished Goods Lot | Traceable quantity produced/packed as a lot | Retains source and production lineage |
| FEFO | First Expiry, First Out allocation strategy | Candidate strategy; product and tenant policy may vary |
| FIFO | First In, First Out allocation strategy | Receipt chronology-based strategy; not equivalent to FEFO |
| Yield | Output quantity relative to defined input quantity | Formula must state units, process stage and by-product treatment |
| Work in Progress (WIP) | Material currently within a production process | Distinct from raw material and released finished goods |
| By-product | Secondary output generated by a process | Requires explicit quantity and cost treatment |
| Process Loss | Input quantity not recovered as saleable output/by-product | Must be measured or estimated under a defined method and reason |
| Stock Adjustment | Controlled correction for count variance, damage, loss, or other reason | Must not silently overwrite stock balance |
| Accounts Payable | Amount owed to suppliers | Distinct from supplier master and payment record |
| Chart of Accounts (CoA) | Structured hierarchy of ledger accounts | Tenant/books-specific and version-controlled |
| General Ledger (GL) | Accounting record of posted financial effects | Operational subledgers should reconcile to GL under defined rules |
| Journal Entry | Balanced set of debit and credit ledger lines | Posted entries are immutable; corrections use reversal/adjustment |
| Voucher | Business/accounting document representing a transaction | Voucher type and numbering are configurable and mapped externally |
| Cost Centre | Analytical dimension for attributing income/cost | Not automatically a legal entity or branch |
| Trial Balance | Period-end listing of ledger balances | Total debits must equal total credits |
| Financial Period | Configured accounting interval | Closed-period posting requires controlled authorization |
| Reversal | Linked accounting entry that negates a prior posting | Does not delete or rewrite the original journal |
| Tally Mapping | Versioned mapping between SaaS identity and Tally master/voucher identity | External mapping; never primary SaaS identity |
| Synchronization Batch | Auditable set of records exchanged with an external system | Must support idempotency, checksums, results and retry |
| General Ledger (GL) | Accounting record of posted financial effects | Operational subledgers should reconcile to GL under defined rules |
| Business Date | Tenant-defined operational date used for shifts/cut-offs | May differ from server timestamp/calendar date |
| Effective Date | Date/time from which a rate, rule, or configuration applies | Historical transactions retain the applied version |
| Entitlement | Feature/limit granted by a SaaS plan or approved override | Entitlement does not replace authorization |
| Configurability | Ability to change supported settings without code changes | Must be bounded by validated rules and safe defaults |
| Extensibility | Ability to add capabilities/integrations without destabilizing core contracts | Not permission to create arbitrary inconsistent business rules |
| Projection / Forecast | Estimated future outcome based on data and assumptions | Never present as actual or guaranteed value |
| KPI | Defined measurable indicator with formula, grain, and owner | Every KPI needs consistent calculation semantics |

| Expense Claim | Request to reimburse an employee or authorized person for business spending | Distinct from vendor bill and actual payment |
| Petty Cash Float | Controlled small cash balance assigned to a custodian/location | Balance is derived from recorded cash movements |
| Cash Custodian | Authorized person responsible for a cash float/drawer | Custody and accounting ownership are distinct |
| Bank Statement Line | External bank transaction evidence | Distinct from an internally recorded receipt/payment |
| UTR | Unique Transaction Reference supplied for a bank transfer | User-entered reference is not proof of settlement until verified |
| Bank Reconciliation | Matching internal cash/bank records with external statement evidence | Matching must not rewrite source transactions |
| Payroll Period | Defined interval for calculating employee compensation | Finalized periods require controlled correction |
| Payroll Component | Configurable earning, deduction, contribution or reimbursement element | Effective-dated with calculation and ledger mapping |
| Statutory Rule | Versioned rule with applicability, source and effective date | Requires authoritative verification and review |
| Tax Classification | Effective-dated tax treatment metadata for a product or transaction | Must not be inferred solely from product name |
| Budget Version | Approved or draft plan for a defined period and dimension set | Revisions retain previous approved versions |
| Forecast | Estimated future outcome based on historical/current data and assumptions | Distinct from actual and approved budget |
| Scenario | Alternative set of assumptions used to compare possible outcomes | Must not create or alter posted transactions |
| KPI Definition | Versioned formula and metadata for a key performance indicator | Includes source, grain, date basis and inclusion rules |
| Reconciliation Exception | Unmatched, duplicate, inconsistent or unsupported item requiring review | Must retain evidence and resolution history |


| Authorized User | User whose effective permission and scope assignments authorize the specific action being evaluated | A job title, seniority, role label, physical presence or general administrator status alone does not confer the permission |
| Emergency Authority | User explicitly designated through a current, scoped permission assignment to activate the restricted ODR-002 Emergency Override when the ordinary approver is unavailable | This is an authorization assignment, not a job title or general role; self-activation remains limited to the approved emergency policy |
| Emergency Override | Temporary, narrowly scoped break-glass authorization for minimum protective stock actions during a qualifying emergency | Initial validity is 60 minutes; one independently authorized extension may add up to 60 minutes; hard maximum 120 minutes; expiry/revocation blocks further stock mutations |
| Emergency Incident / Retrospective Review | Auditable record of the emergency, activation, affected stock/location, actions, evidence, independent review and reconciliation | Self-activated overrides require retrospective review by a different authorized user |
| Approval Delegation | A temporary, explicit authorization allowing a named delegate to perform specified approval actions within a defined scope and limits | Requires the delegator's approval permission and `approval.delegate`; maximum 30 calendar days; non-transferable, non-renewing, revocable and automatically invalidated when underlying authority ceases |
| Delegator | The authorized user who creates a scoped Approval Delegation | Must hold the underlying approval permission and separate delegation permission for the same scope |
| Delegate | The named active user who receives the temporary approval capability under an Approval Delegation | May perform only the specified approval action(s); cannot subdelegate or approve their own initiated actions |
| Grant Expiry Reminder | An informational notice sent before a shared-site stock-task grant's effective end | Default reminders are 24 hours and 1 hour before expiry when the grant duration permits; it does not extend or preserve authorization |
| Grant Expiry / Revocation Notice | An immediate notice that a stock-task grant has expired, been revoked or automatically invalidated | Notification delivery is not an authorization condition; zero grace applies and further stock mutations are denied immediately |
| Home Branch | The Branch to which a worker is ordinarily assigned for workforce administration or scheduling purposes | Does not automatically approve or veto stock-task access to another Branch's inventory; workforce assignment authority is separate from stock authorization |
| Workforce Assignment | An authorized allocation of a worker to a Branch, OperatingLocation, shift, duty or time period | Must be separately authorized where required by tenant workforce policy; a stock-task grant does not create or modify it |

## Terminology rules

1. Use Tenant for the SaaS isolation boundary and Dairy Business for the real-world operation.
2. Do not use Branch, Outlet, Warehouse, Collection Centre, Processing Unit, and Delivery Area interchangeably.
3. Distinguish plan, schedule, occurrence, order, fulfilment, invoice, statement, receipt, and reconciliation.
4. Distinguish payment capture/recording from provider confirmation and bank settlement.
5. Distinguish actual, target, budget, forecast, and projection in every report.
6. Where a term has legal meaning, verify it against applicable Indian law and official guidance before using it in customer-facing documents.
7. New domain terms must be added here before being independently redefined in module documents.


## Authorization terms

| Term | Canonical meaning | Boundary |
|---|---|---|
| Permission | An atomic capability to view data or perform a specific action on a defined resource | Evaluated with explicit scope, validity, ownership and policy; never inferred from a title or account type |
| Permission Assignment | An auditable grant of one or more permissions to an eligible principal/group, with applicable scope and effective period | May be granted, restricted, amended, expired or revoked only within the assigning authority's delegated permissions |
| Permission Bundle | A customer-configurable collection of atomic permissions that may be reused for assignment | Optional convenience; bundle name/title has no independent authority |
| Role | An optional named permission bundle used to organize assignments | Role title or role assignment grants no permission beyond the bundle's current explicit contents and scope |
| Effective Permission Set | Permissions that remain valid after evaluating assignments, scope, time, revocation, membership and applicable policy | Sole basis for business authorization; missing or ambiguous permission defaults to deny |
| Default Deny | An action or data disclosure is denied unless all required permissions and scope conditions are affirmatively valid | Applies consistently across every client, API, report, export, job and integration |


| Tenant Membership | A separately managed association between a global user identity and one Tenant | May coexist with memberships in other Tenants; each membership is an independent security context; it does not grant business permissions |
| Membership Branch Assignment | A workforce/operating-context relationship between a Tenant membership and a Branch | May be multiple; optionally one Home/Primary Branch at a time; does not grant data visibility or action authority |
| Home / Primary Branch | The Branch designated as a worker's normal workforce/administrative context | Organizational context only; no automatic access, approval, veto or stock ownership follows from this designation |


## Route execution and dispatch terms

| Term | Canonical meaning | Boundary |
|---|---|---|
| Route | Branch-owned planned sequence/coverage for delivery stops | One Route belongs to one Branch and may serve only its effectively assigned ServiceAreas |
| Manual Route | One-off Branch-owned ordered stop sequence created during run planning | Does not require route optimization or a reusable template |
| Delivery Plan | Branch/date/shift plan grouping source demand allocations and one or more DeliveryRuns | Does not itself prove dispatch or delivery |
| Delivery Demand Allocation | Explicit quantity allocation from a source demand line to a plan/run/stop | Must prevent duplicate allocation and over-fulfilment |

| Accountable Run Assignee | Named person responsible for the run's operational coordination | Assignment is not a permission or job-title authority |
| Run Participant | Named person/principal assigned to a run or stop with a defined operational duty | Access still requires explicit current permissions and scope |
| Transport Resource Assignment | Effective-dated optional assignment of a vehicle/carrier/resource to a run segment | Vehicle ownership and assignment do not grant data or dispatch permission |
| Dispatch Readiness | Validated state that the route, demand, personnel, resource, custody and applicable product controls satisfy dispatch preconditions | Separate from dispatch authorization and actual departure |
| Dispatched Manifest | Versioned snapshot of route/stops, demand quantities, participants, resources and policy at dispatch | Post-dispatch changes are linked exceptions, not silent edits |
| Run Execution Event | Append-oriented evidence of actual departure, stop progression, delay, deviation, hold, replacement or completion | Planned route and actual execution remain distinguishable |

## Shared-site accountability and custody terms

| Term | Canonical meaning | Boundary |
|---|---|---|
| Physical Custody | Recorded physical holding/control of identified goods, a container, asset or resource at a point in time | Not ownership, stock-ledger attribution, permission or automatic legal liability |
| Custodian | Named person, carrier, service identity or external party recorded as holding a custody subject | Custody assignment does not grant general Tenant/Branch access |
| Handling / Execution Actor | Person or service that performed a specific physical or system-recorded action | May differ from the custodian before or after the event |
| Custody Subject | Goods, a quantity/lot, container, asset or resource whose physical custody is tracked | Subject identity and ownership/stock attribution remain distinct |
| Custody Event | Append-oriented evidence that a subject was received, held, inspected, dispatched, used, transferred or otherwise handled | Does not itself create a stock or financial movement |
| Custody Handover | Controlled transfer of recorded physical custody between identified parties | Requires the selected evidence/acknowledgement; no acceptance by silence or timeout |
| Custody Assurance Profile | Versioned policy defining the handover steps and evidence required for a workflow/risk level | Tenant-configurable within fixed safety, audit and ownership invariants |
| Custody Evidence | Readings, scans, seal references, acknowledgements, photographs, documents or device evidence linked to a custody event | Must preserve source, time, context and verification provenance |
| Custody Incident | Reported discrepancy or exception affecting custody, condition, quantity, location or evidence | Report does not automatically establish fault, liability or financial effect |
| Shared Resource | Facility, equipment, vehicle, device or other resource explicitly made available to more than one Branch/capability | Shared use does not transfer ownership or grant unrelated data access |
| Shared Resource Use | Recorded use/booking of a shared resource by an authorized Branch/capability and actor | Usage, maintenance, custody and cost allocation are separately governed |


## Stock-task grant and reconciliation terms

| Term | Canonical meaning | Boundary |
|---|---|---|
| StockTaskAccessGrant | A Tenant-bound cross-Branch authorization exception for exactly one named worker to perform explicitly permitted stock actions for one stock-owning Branch | Not general Branch membership, a workforce assignment, stock ownership or an independent action permission |
| StockTaskAccessGrantVersion / GrantVersion | Immutable snapshot of a submitted/approved grant's worker, stock-owning Branch, locations, actions, stock/task scope, limits and effective period | Approval binds to the exact version/payload; material changes require a new version and fresh approval |
| Grant Authorization State | Current authority lifecycle state such as Draft, Submitted, Pending Approval, Rejected, Withdrawn, Approved/Scheduled, Active, Superseded, Expired, Revoked or Invalidated | Separate from physical custody and the state of a linked stock task |
| Stock Task | Operational work item performed under a specific grant version and independently permissioned stock action | A task assignment does not itself grant permissions or stock ownership |
| Stock Task Hold | Review-required hold on in-progress work after the linked grant expires, is revoked or becomes invalid | Preserves completed steps, posted stock effects, current custody and outstanding work; further stock mutation is blocked |
| Stock Task Reconciliation | Append-oriented, authorized decision and evidence for reauthorization, reassignment, correction/compensation, closure or escalation of a held task | Original stock/custody events are not erased; a replacement worker requires a new grant and actual handover where goods change hands |
| Grant Activation Time | Earliest actual instant at which the exact approved grant version may be used | No retroactive authority before approval commit; effective end is exclusive and zero grace applies |
