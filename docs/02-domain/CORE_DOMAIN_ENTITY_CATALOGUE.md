# Core Domain Entity Catalogue

**Status:** Conceptual baseline — not a database schema  
**Purpose:** Establish entity boundaries and ownership before ERD/table design.

## 1. Modelling rules

- This catalogue describes conceptual records, not final tables, columns, ORM models, or service boundaries.
- Every tenant-owned business record must carry an unambiguous tenant ownership path.
- Branch/location attribution is required where the business event occurs at or belongs to a specific operational location; do not force branch attribution on inherently tenant-wide records.
- Master data, agreements, dated occurrences, execution records, financial postings, and analytical projections are different record classes.
- Preserve historical meaning: transactions must retain the effective product, rate, tax, unit, schedule, and policy context used at the time.
- Do not store a mutable balance as the only evidence of financial or stock history.
- Define identifiers, uniqueness, deletion, archival, and retention in the detailed data architecture.

## 2. Platform and tenancy

| Conceptual entity | Responsibility | Key relationships / constraints |
|---|---|---|
| Platform Account | Platform operator identity | Separate platform roles from tenant membership |
| Tenant | SaaS customer workspace | Owns tenant configuration and tenant business data |
| Tenant Subscription | Commercial plan, billing period, status | Separate from dairy customer product subscriptions |
| Plan / Feature Entitlement | Feature availability and usage limits | Entitlement checks must not grant data access |
| Tenant Business Profile | Operating/legal/business profile | May hold multiple registrations only if approved by ODR-003 |
| Branch | Management/operating unit within the tenant | Tenant-owned; may be associated with multiple OperatingLocations and may serve multiple ServiceAreas through explicit effective-dated mappings |
| OperatingLocation | Actual physical/operational site or premises | Tenant-owned; may be unassigned or used by one or more Branches through explicit governed, effective-dated associations; one site may support multiple capabilities; shared physical use does not merge branch stock or grant cross-branch access |
| Operational Capability | Function performed/enabled at an OperatingLocation, e.g. retail sales, milk collection, storage, processing or packaging | Multiple capabilities may coexist at one OperatingLocation; capability is not itself a physical location |
| Business Calendar / Shift | Business date, shift, cut-off settings | Effective-dated and tenant-configurable |

## 3. Identity and access

| Conceptual entity | Responsibility |
|---|---|
| Person | Human identity/contact concept |
| User Account | Authentication identity |
| Tenant Membership | User's relationship to tenant |
| Employee Profile | HR/workforce record; may exist without login |
| Role / PermissionBundle | Optional Tenant-configurable named bundle of explicit atomic permissions; bundle label itself grants no authority |
| Permission | Atomic action/data capability evaluated with explicit scope, validity and policy; missing permission defaults to deny |
| Permission Assignment / Role Assignment | Explicit principal/group permission or optional bundle assignment with scope and effective period; assignments are grantable, restrictable, changeable, expirable and revocable within delegated authority; explicit deny takes precedence; cross-Branch stock-task grants remain separate |
| Access Group | Tenant-scoped collection of eligible Tenant memberships/principals used as a governed permission-assignment target | Group membership alone grants no access; each permission remains explicitly assigned and scoped |
| Group Membership | Effective-dated, auditable relationship between one Tenant membership and one Access Group | Does not confer data visibility, action authority or cross-Branch access by itself |
| Permission Catalogue Entry | Platform-governed stable atomic permission key with resource/action semantics, scope, risk, data-sensitivity, delegation, approval, audit and lifecycle metadata | Unknown/retired/incompatible keys fail closed; labels/translations do not alter semantics |
| Permission Bundle Version | Immutable published snapshot of registered permission keys included in a Tenant bundle or platform starter | RoleAssignment references an explicit version; no silent permission widening |
| Extension Permission Registration | Versioned namespaced permission definition for declared extension resources/actions and data contract | Registration does not grant access; installation/activation remains separate from Tenant assignment and host grants |
| Approval Policy | Conditions, limits, and required approver |
| Approval Delegation | Temporary, explicit authorization for a named delegate to approve specified categories/actions within a Tenant/Branch/resource scope and limits; requires the delegator's approval permission plus `approval.delegate`; max 30 calendar days, no renewal/subdelegation, revocable and automatically invalidated when underlying authority or membership ceases |
| Device / Session | Authentication and session lifecycle |
| Support Access Grant | Explicit platform-to-tenant support authorization |
| Audit Event | Who did what, when, from which context, and outcome |

A person, employee profile, user account, and role assignment must not be collapsed into one entity.

## 4. Customer, supplier, and catalogue

| Conceptual entity | Responsibility |
|---|---|
| Party / Party Role | Shared tenant-owned counterparty identity with one or more role assignments; branch-specific relationship visibility is separately enforced (DBR-003) |
| Customer Account | Customer-specific commercial relationship linked to Party; pricing, credit, billing and status |
| Customer Contact / Address | Contact people and purpose/effective-dated addresses linked to Party/account; not login identities |
| Supplier Account | Supplier-specific commercial/procurement relationship linked to Party; terms and status |
| Farmer Profile | Optional producer-specific extension of SupplierAccount; collection attributes and producer workflow data |
| Party Relationship | Explicit household, representative, cooperative or other party-to-party relationship; authority is not inferred |
| Product | Product definition and classification |
| Product Variant | Distinct sellable/procurable variation |
| SKU | Inventory/commercial identity |
| Unit of Measure | Measurement unit |
| Unit Conversion | Validated conversion between units |
| Product Pack | Package definition and declared quantity |
| Price List / Price Rule | Effective-dated selling rates |
| Supplier Rate Rule | Effective-dated procurement rate |
| Tax Profile | Applicable tax classification/rules, subject to verified applicability |

## 5. Subscription, order, and fulfilment

| Conceptual entity | Responsibility |
|---|---|
| Customer Subscription | Recurring supply agreement |
| Subscription Line | Product, quantity, unit, and line-specific terms |
| Schedule Definition | Recurrence pattern and applicable shifts |
| Schedule Version | Effective-dated schedule changes |
| Schedule Exception | Skip, pause, holiday, extra supply, substitution, or date-specific override |
| Schedule Occurrence | One planned dated supply event |
| Order | Non-recurring or customer/staff-requested supply |
| Order Line | Requested product and quantity |
| Allocation | Reservation of available stock for demand |
| ServiceArea | Geographic customer/service coverage area | Many-to-many with Branch through an explicit effective-dated service assignment; geography does not grant cross-branch access |
| Route / Manual Route | Branch-owned reusable or one-off manual stop sequence and service-area coverage | Each Route has exactly one owning Branch; a Branch may own multiple Routes. Route coverage must stay within that Branch's effective ServiceArea assignments; a small Tenant need not maintain a reusable template |
| Delivery Plan | Branch/date/shift planning container for demand allocations and one or more runs | A simple one-run flow may create the plan implicitly |
| Delivery Demand Allocation | Explicit source demand quantity allocated to a plan/run/stop | Prevents duplicate or excess allocation across concurrent runs |
| Delivery Run | Actual execution unit for one Branch-owned route/plan with a versioned manifest | Branch/Route/Plan must match; personnel and vehicle assignments are separate |
| Delivery Run Personnel Assignment | Effective-dated participant and operational duty for a run | Duty label grants no permission; owner may self-assign without employee profile |
| Delivery Run Transport Assignment | Optional time-bounded vehicle/carrier/transport resource assigned to a run segment | Sequential replacements are recorded; concurrent vehicle split uses separate runs |
| Run Execution Event | Append-oriented actual route/dispatch/departure/arrival/deviation/hold/replacement event | Never rewrites the dispatched manifest |
| Delivery Stop / Attempt | Customer/location stop and each actual delivery attempt | Multiple attempts/outcomes are retained with actor, time, quantities and reason |
| Fulfilment Record / Line | Actual delivered/returned/undelivered quantity and result linked to source demand | Planned, dispatched, delivered, returned and billable quantities remain distinct |
| Delivery Evidence | Run/stop/attempt proof or required handling evidence | Configurable and permission-controlled; preserve source/profile version |
| Delivery Exception | Failed, partial, delayed, refused, route/vehicle or otherwise exceptional outcome | Linked to original run/stop and corrective action |
| Return / Adjustment | Controlled return, correction, or credit-triggering event | Financial/stock effects are separate approved workflows |

A generated schedule occurrence must be uniquely traceable to its source subscription line and schedule version. Re-running generation must not create duplicate occurrences.

## 6. Procurement, quality, processing, inventory

| Conceptual entity | Responsibility |
|---|---|
| Purchase Order | Planned purchase commitment, if used |
| Goods Receipt | Physical receipt against purchase |
| Milk Collection Entry | Farmer-specific raw milk receipt |
| Milk Sample / Test Result | Quality evidence for a collection or lot |
| Procurement Settlement | Calculated amount due to supplier |
| Processing Batch | Transformation run with input/output/loss records |
| Packaging Run | Pack/label operation and output lots |
| Stock Item | Inventory-managed identity |
| Stock Location | Inventory-control holding position/subdivision (e.g. refrigerator, room, shelf, bin or zone) within or associated with an OperatingLocation; distinct from the physical site itself; stock ownership, balances and control remain attributable to the relevant Branch/inventory scope even when multiple Branches use one physical OperatingLocation |
| Stock Lot / Batch | Traceable stock quantity group |
| Stock Movement | Receipt, issue, transfer, return, loss, or adjustment; authoritative quantity/location/status effect |
| Stock-Task Access Grant / Approval | Explicit, limited, effective-dated authorization for a worker to perform specified stock actions for another Branch at a shared site; activation requires approval by an authorized user holding the required stock-owning-Branch approval permission, or a currently valid delegate under ODR-002, with exact scope/version and approval evidence retained. Revocation/expiry immediately blocks new tasks and further stock mutations; a stock-task grant does not replace any workforce assignment required by tenant policy; the recommended Home-Branch boundary is in ODR-002 section 16.6 |
| Stock-Task Grant Version | Immutable authorization envelope for one named worker, stock-owning Branch, stock locations, actions, task/item scope, limits and effective period | Approval is bound to the exact version/payload; material amendment requires a new version |
| Stock Task | Operational work item that references the grant version used for an authorized cross-Branch stock action | Task state and grant authorization state remain separate |
| Stock Task Hold | Hold on an in-progress task after its grant expires, is revoked or becomes invalid | Preserves posted effects, custody, completed steps and outstanding work; blocks further stock mutation |
| Stock Task Reconciliation | Append-oriented decision/evidence for reauthorization, reassignment, correction/compensation, controlled closure or escalation | Original stock/custody events are never erased or silently rewritten |
| Emergency Override / Break-Glass Grant | Temporary, narrowly scoped and auditable authority for protective stock actions during a qualifying emergency; requires explicit permission, defined actor/scope/actions/reason, 60-minute initial validity, one independently authorized extension up to a 120-minute total maximum, and immediate expiry/revocation enforcement |
| Emergency Incident / Retrospective Review | Records the emergency reason/evidence, activation mode, affected stock and locations, actions/quantities/custody, independent review, findings and corrective/reconciliation actions |
| Stock Count | Physical count exercise |
| Stock Reservation | Quantity reserved for order/delivery |
| Waste / Loss Record | Controlled quantity loss and reason; requires separate approved stock disposition |
| Custody Subject | Goods, lot/quantity, container, asset or resource whose physical custody is tracked | Ownership, stock attribution and custodian are distinct |
| Custody Assignment | Effective-dated named custodian assignment for a subject/scope | Does not grant business permissions or change stock ownership |
| Custody Event | Append-oriented fact recording receive, hold, inspect, dispatch, use, transfer or other custody action | Records who/what/where/when/why and links to stock movement only when both occur |
| Custody Handover | Offer, dispatch, transit, receipt, acceptance/dispute and closure between custody parties | No automatic acceptance; partial/rejected receipt remains explicit |
| Custody Evidence | Structured quantity, condition, temperature, seal, scan, signature, device or document evidence | Retains source, timestamp, profile and verification provenance |
| Custody Incident | Discrepancy, damage, loss, contamination, temperature excursion or missing acknowledgement | Does not automatically post an adjustment, deduction or liability finding |
| Shared Resource Use | Approved/recorded use of shared equipment/facility by Branch/capability | Ownership, permitted use, custody, maintenance and cost allocation remain separate |

## 7. Finance and workforce

| Conceptual entity | Responsibility |
|---|---|
| Sales Transaction | Commercial sale event |
| Billing Document | Invoice/debit/credit document under policy |
| Customer Statement | Period account summary |
| Receivable Entry | Customer amount due |
| Payment Record | Money received/paid or acknowledged |
| Payment Allocation | Application of receipt/payment to open items |
| Bank/Cash Account | Account or custody point |
| Reconciliation Session | Match internal records with bank/provider evidence |
| Supplier Payable | Amount owed to supplier |
| Expense Claim / Expense Entry | Business expense |
| Journal / Ledger Posting | Accounting effect of a source event |
| Chart of Accounts | Account classification |
| Employee | Workforce identity/profile |
| Attendance / Shift Record | Time and attendance evidence |
| Leave Request | Absence request and decision |
| Compensation Structure | Salary/wage terms |
| Payroll Run / Payroll Line | Calculated payroll result and components |
| Asset | Owned/maintained business asset; may be explicitly shared for authorized use while ownership remains distinct |
| Maintenance Work Order | Planned/actual maintenance activity, including cleaning/calibration/inspection where applicable |

## 8. Analytics and governance

| Conceptual entity | Responsibility |
|---|---|
| KPI Definition | Name, formula, grain, dimensions, owner, version |
| Report Definition | Parameters, columns, filters, permissions |
| Forecast Run | Versioned forecast input, model, assumptions, output |
| Budget Version | Approved or draft budget snapshot |
| Scenario | What-if assumptions and outputs |
| Data Quality Issue | Missing, inconsistent, or late source data |
| Import Job | External data ingestion and validation result |
| Export Job | Controlled data extraction |
| Integration Endpoint / Credential Reference | External system configuration without exposing secrets |
| Notification | Message intent, recipient, channel, delivery state |
| Attachment / Document | Evidence with access and retention policy |

## 9. Critical relationship and integrity questions

1. Is a tenant allowed multiple legal entities/GST registrations? See ODR-003.
2. Can one customer have multiple active subscriptions and different service branches? Define explicit ownership and service assignment.
3. Can one schedule line generate multiple products/shifts on the same date? Model line-level occurrences.
4. How are subscription changes effective-dated when future occurrences already exist?
5. Can an occurrence be fulfilled in multiple partial deliveries?
6. How are supplier milk collection entries linked to tests, accepted quantities, and settlement lines?
7. How are processing input lots linked to output lots and losses?
8. What is the authoritative source for stock on hand and customer/supplier balances?
9. How are financial corrections represented without rewriting posted history?
10. What data is retained, archived, exported, or deleted at tenant offboarding?

## 10. Not yet decided

The catalogue does not determine physical database design, primary key format, tenancy schema strategy, event store, microservices, or exact branch hierarchy. Those require architecture decisions after detailed workflows, scale assumptions, security requirements, and technology evaluation.


**Authorization invariant:** User accounts authenticate actors but do not confer business permissions. No role title, job title, seniority or account type independently grants visibility, action, approval, export or administration. Customer-configurable permission assignments are the sole source of business authority, subject to fixed platform invariants and scoped authorization checks. See `../04-architecture/CROSS_BRANCH_PERMISSION_MATRIX.md`.


## Membership and workforce assignment concepts

| Concept | Responsibility | Boundary |
|---|---|---|
| Tenant Membership | Independent association between a global User Account and one Tenant, with its own effective status and lifecycle | One current membership per user/Tenant pair; multiple Tenants are independent security contexts |
| Membership Branch Assignment | Records workforce/operating context for a Tenant member at one or more Branches, optionally identifying one current Home/Primary Branch | Never grants read/write/approval permission; access is controlled separately by Permission Assignments and scope |
| Permission Assignment | Grants explicit atomic capabilities to a principal/group within declared scope, validity and limits | Sole source of business access authority; role bundles are optional packaging only |


**Catalogue reference:** Stable permission key semantics, action families, sensitive-data permissions, scope vocabulary, direct/group assignment and precedence are governed by [ARC-IAM-CAT-001](../04-architecture/PERMISSION_CATALOGUE_AND_ASSIGNMENT_GOVERNANCE.md).

**Custody invariant:** Stock ownership, stock-ledger attribution, physical location, physical custody, actual handling, workforce assignment and permission are separate. A CustodyEvent records physical custody; a StockMovement records quantity/location/status effect. One does not imply or duplicate the other. A missing acknowledgement never implies acceptance; unresolved custody preserves the last verified custodian/location. Custody incidents do not by themselves authorize adjustments, write-offs, financial postings, payroll deductions or legal-liability conclusions. See `../02-domain/SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md`.
