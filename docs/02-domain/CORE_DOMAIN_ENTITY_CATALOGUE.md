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
| Delivery Route | Reusable or planned route | Each Route has exactly one owning Branch; a Branch may own multiple Routes. Route coverage must stay within that Branch's effective ServiceArea assignments; another Branch uses its own Route |
| Delivery Run | Dated execution of a route/shift | References one Branch and a Route owned by that same Branch; preserves the route/plan version and operational context used at execution |
| Delivery Stop | Customer/location stop in a run |
| Fulfilment Record | Actual delivered/handed-over quantity and result |
| Delivery Exception | Failed, partial, delayed, refused, or otherwise exceptional outcome |
| Return / Adjustment | Controlled return, correction, or credit-triggering event |

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
| Stock Movement | Receipt, issue, transfer, return, loss, or adjustment; retains stock-owning Branch and acting worker/grant attribution when a cross-branch stock-task grant is used |
| Stock-Task Access Grant / Approval | Explicit, limited, effective-dated authorization for a worker to perform specified stock actions for another Branch at a shared site; activation requires approval by an authorized user holding the required stock-owning-Branch approval permission, or a currently valid delegate under ODR-002, with exact scope/version and approval evidence retained. Revocation/expiry immediately blocks new tasks and further stock mutations; a stock-task grant does not replace any workforce assignment required by tenant policy; the recommended Home-Branch boundary is in ODR-002 section 16.6 |
| Stock Task Hold / Reconciliation | Holds an in-progress task after grant revocation/expiry, preserves completed steps and custody evidence, and records authorized reauthorization, reassignment or controlled cancellation/reconciliation without rewriting original stock events |
| Emergency Override / Break-Glass Grant | Temporary, narrowly scoped and auditable authority for protective stock actions during a qualifying emergency; requires explicit permission, defined actor/scope/actions/reason, 60-minute initial validity, one independently authorized extension up to a 120-minute total maximum, and immediate expiry/revocation enforcement |
| Emergency Incident / Retrospective Review | Records the emergency reason/evidence, activation mode, affected stock and locations, actions/quantities/custody, independent review, findings and corrective/reconciliation actions |
| Stock Count | Physical count exercise |
| Stock Reservation | Quantity reserved for order/delivery |
| Waste / Loss Record | Controlled quantity loss and reason |

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
| Asset | Owned/maintained business asset |
| Maintenance Work Order | Planned/actual maintenance activity |

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