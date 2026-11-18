# Open Decisions Register

**Status:** Initial register  
**Rule:** Do not leave a decision generically open. Every item must be classified as (a) resolved by an explicit project constraint, (b) recommended engineering baseline, (c) owner approval required, (d) external evidence/legal applicability required, or (e) intentionally deferred with a trigger and review date. Never label a recommendation as user-approved. For owner/evidence dependencies, document the safe interim baseline and the exact decision, owner, evidence, impact and latest decision date.

| ID | Area | Open decision / question | Why it matters | Required evidence / investigation | Status |
|---|---|---|---|---|---|
| ODR-001 | Business identity and operating model | A tenant represents one Dairy Business Workspace. May that workspace support multiple dairy operating models and multiple branches? | Tenant onboarding, capability configuration, module availability and future extensibility | Product-owner decision recorded below; legal form and regulatory applicability are explicitly outside the current development scope and will be reviewed after project completion | **Resolved — user-approved project decision (2026-11-18): Option B.** One tenant supports multiple operating models and multiple branches. |
| ODR-002 | Branch and operating-location model | Define Branch, OperatingLocation, its operational capabilities, StockLocation, ServiceArea and their relationships. | Avoids conflating management units, physical sites, operational functions, inventory sublocations and geographic coverage | Approved: one physical site may support multiple capabilities; StockLocation is separate; Branch is a management/operating unit; one Branch may be associated with multiple OperatingLocations; OperatingLocation may be unassigned or used by multiple Branches through explicit governed associations; one Branch may serve multiple ServiceAreas and one ServiceArea may be served by multiple Branches through explicit effective-dated mapping; each Route belongs to exactly one Branch and DeliveryRun must use a Route belonging to its Branch; shared physical premises may be used by multiple Branches while each Branch retains separately attributable StockLocations, stock ownership, stock control and transaction records. Physical sharing grants no cross-branch stock/data access. A shared-site worker may perform specifically authorized stock-handling actions for more than one Branch only through an explicit, limited, effective-dated and auditable grant; this does not grant general branch membership or visibility into the other Branch's full operational data. The authorized user holding the required approval permission of the stock-owning Branch must approve each cross-branch stock-task grant before it becomes effective; home-Branch consent is not yet decided. Detailed grant lifecycle, accountability and complete permission matrix remain to be specified | **Partially resolved — core physical-site, Branch/OperatingLocation, ServiceArea/Branch, Route/Branch and shared-site/stock-segregation and limited cross-branch stock-task grant baselines approved by product owner (2026-11-18); detailed shared-use accountability and complete permission-matrix policies remain open.** |
| ODR-003 | Tenant hierarchy | Can one tenant contain multiple legal entities or GST registrations? | Tax, accounting, ownership | Legal/tax research and business scenarios | Open |
| ODR-004 | User membership | Can a user belong to multiple tenants or branches? What is the default assignment? Ordinary employees must remain limited to their own assigned branch unless a specific audited cross-branch grant applies. | Identity, authorization, session context | Access scenarios and threat analysis; preserve the explicit branch-isolation constraint in DBR-003 and ARC-TEN-001 | Open |
| ODR-005 | Authorization | What roles, permission actions, scopes, delegation, and approval limits are needed? Tenant Owner is the explicit example of an all-branch visibility role; role titles alone do not grant this scope, and cross-branch read must remain distinct from mutation. | Cross-module access and segregation of duties | Actor catalogue and permission matrix; define auditable scope grants, cross-branch read/mutate separation and sensitive-field permissions | Open |
| ODR-006 | Subscription lifecycle | Define pause, resume, skip, holiday, temporary change, substitution, cancellation. | Delivery, billing, stock, balances | Customer lifecycle scenarios and exceptions | Open |
| ODR-007 | Subscription schedule | Define cut-offs, schedule changes, alternate-day cycles, branch/route calendars. | Delivery planning and correctness | Schedule examples and exception analysis | Open |
| ODR-008 | Units and precision | Which units, conversions, precision, rounding, and pack sizes are supported? | Procurement, inventory, pricing, billing | DBR-004 precision-layer analysis; product/UOM catalogue, device-resolution evidence, workflow test vectors and owner-approved commercial/accounting rounding rules | Open |
| ODR-009 | Milk quality | Which quality parameters, sampling, acceptance/rejection, and traceability are required? | Procurement settlement, processing, compliance | Dairy operations and food-safety research | Open |
| ODR-010 | Procurement settlement | Define rates, quality incentives/penalties, deductions, advances, settlement cycles. | Supplier balances and accounting | Real procurement models and worked calculations | Open |
| ODR-011 | Processing | Define stages, batches, yields, losses, packaging, and traceability. | Manufacturing, stock, cost, recall | Process mapping for target dairy sizes | Open |
| ODR-012 | Inventory | Define stock ownership, warehouses, transfers, expiry, batch, wastage, adjustments. | Stock accuracy and audit | Inventory lifecycle and exception analysis | Open |
| ODR-013 | Pricing | Define price lists, customer rates, effective dates, discounts, deposits, approvals. | POS, subscriptions, profitability | Pricing scenarios and policy analysis | Open |
| ODR-014 | Billing | Define billing cycles, invoice/statement model, advances, credit limits, proration. | Receivables and financial reporting | End-to-end scenarios and legal review | Open |
| ODR-015 | Payment verification | Which payment sources can be reconciled automatically versus manually recorded? | Avoids false verification claims | Bank/provider integration capability research | Open |
| ODR-016 | GST and invoicing | Determine tax treatments, invoice formats, e-invoicing/e-way bill applicability, retention. | Legal and financial correctness | Current authoritative Indian tax sources and applicability matrix | Open |
| ODR-017 | Accounting | Define ledgers, vouchers, chart of accounts, tax, bank reconciliation, statements, integrations. | Finance domain and integration boundaries | Accountant workflows and statutory research | Open |
| ODR-018 | HR/payroll | Define attendance, shifts, leave, salary, deductions, statutory payroll, contractor workflows. | Scope and legal obligations vary | Indian labour/payroll research and scenarios | Open |
| ODR-019 | Mobile apps | ARC-MOB-001 selects one unified, role-aware mobile app as the initial reversible baseline; separate role-focused apps require evidence of material security, usability, distribution or release-management benefit. | Avoids fragmented releases and duplicated client behavior while preserving role-specific workspaces | Validate representative owner, branch-operations, delivery, POS, procurement, inventory and finance journeys before UX freeze; compare total lifecycle cost if separate apps are proposed | Recommended baseline; validate role journeys before UX freeze |
| ODR-020 | Offline operation | ARC-MOB-001 defines a narrow initial offline baseline for assigned delivery-run snapshots and delivery outcome capture with server revalidation, idempotent sync and explicit conflict handling. Offline POS, financial posting, payment settlement, stock posting and master-data changes are not initially guaranteed. | Preserves delivery continuity without allowing stale offline state to corrupt shared financial, stock or schedule records | Field trial; device/data-protection threat review; determine maximum offline duration, cache TTL, supported device matrix and sync support policy; test duplicate, stale, revoked and conflicting operations | Recommended delivery-offline baseline; duration/device policy requires evidence and owner approval; other workflows deferred with triggers |
| ODR-021 | Notifications | Define channels, templates, consent, retries, delivery tracking, provider fallback. | Reliability, cost, privacy | Provider and legal research | Open |
| ODR-022 | Analytics | ARC-BI-001 defines analytics layers, core report families, fact grains, dimensions, KPI governance, security, refresh and acceptance tests. Remaining owner decision: approve official KPI definitions/owners and SaaS plan report entitlements. | Business target ownership and commercial packaging | Management KPI workshop using documented catalogue; formula test vectors and plan-entitlement approval | Recommended architecture baseline; KPI/plan approval required before launch |
| ODR-023 | Forecasting | ARC-BI-001 establishes transparent baseline/statistical forecasting, forecast-run governance, accuracy checks and candidate forecast domains. Initial default is short-horizon daily/weekly forecasting where sufficient history exists; advanced ML is optional. Remaining owner decision: prioritize forecast outputs/horizons by customer value. | Avoids unvalidated ML and unsupported long-range claims | Pilot use cases, minimum-history criteria, backtesting against simple baselines and owner prioritization | Recommended engineering baseline; product prioritization required before release scope is frozen |
| ODR-024 | Budgeting | ARC-BI-001 establishes versioned budgets, draft-to-approved lifecycle, preserved revisions, scenario separation and actual-versus-budget comparison. Remaining owner decision: tenant-selectable approval thresholds and role ownership; default design supports configurable maker-checker controls. | Prevents overwriting approved plans and separates hypothetical scenarios from ledger postings | Worked budget lifecycle, permission matrix and owner approval of default templates/thresholds | Recommended architecture baseline; tenant policy templates require product approval |
| ODR-025 | SaaS plans | Define plans, entitlements, limits, trials, upgrades, downgrades, grace, suspension. | SaaS commercial operations | Platform billing and entitlement scenarios | Open |
| ODR-026 | Data lifecycle | Define retention, archival, export, deletion, offboarding, legal holds. | Privacy, compliance, storage | Applicable law and customer-contract research | Open |
| ODR-027 | Scale targets | ARC-SCALE-001 defines the workload dimensions, three capacity envelopes, scaling path and verification strategy. Remaining input: product owner/business team supplies target adoption and tenant-profile assumptions; engineering converts these into measurable transaction/concurrency/storage estimates using pilot telemetry before production sizing. | Avoids invented capacity figures and undersized/overbuilt production infrastructure | Conservative/expected/stress capacity worksheet, pilot measurements, load/soak/stress test evidence and cost model | Recommended engineering baseline; owner workload input required before production sizing |
| ODR-028 | Availability/recovery | Recovery architecture and an initial RPO/RTO/backup baseline are specified in ARC-DR-001. Remaining approval: accept or amend the proposed service objectives and name operational responders before production. | Business commitment, staffing and recovery cost | Monthly restore evidence, quarterly DR exercise, business impact review and named incident/restore/key-custody owners | Recommended baseline; owner approval required before production |
| ODR-029 | Integrations | Which external systems and APIs are priority? | API, event, adapter boundaries | Integration inventory and prioritization | Open |
| ODR-030 | Technology | ADR-TECH-001 narrows backend, database and mobile candidates and defines weighted criteria plus repeatable PoC gates. Preferred PoC directions: ASP.NET Core/.NET 10 LTS, PostgreSQL 18 and Flutter; Laravel 13, MySQL 8.4 and React Native remain evaluated alternatives. No final stack is approved until team capability, compatibility, integrity, operational and cost evidence is reviewed. | Long-term maintainability, transactional integrity, security patching, deployment and lifecycle cost | Execute identical backend/database vertical slice; mobile offline field PoC; web framework slice; Linux VPS deployment/restore; team capability review; reproducible results and explicit owner approval | Recommended PoC shortlist; final technology approval required after evidence and owner review |
| ODR-031 | Deployment topology | ARC-DEP-001 defines Linux VPS-compatible logical zones, Docker-optional deployment, compact pilot/separated-data/horizontally-scalable profiles, security controls, release process, monitoring, backup and production gates. Preferred direction is to evaluate a separated database tier for production; a single-VPS profile is conditional pilot-only with explicit single-host risk acceptance. | Security, resilience, scale, operational recovery and total cost | Compare current VPS/provider/region terms, data location, network/security features, support, cost, database/backup capability; use workload/capacity model, PoC and restore evidence; obtain explicit provider/topology approval | Recommended deployment baseline; provider, region, sizing and production risk acceptance require evidence and owner approval |
| ODR-032 | Localization | Which languages, translation workflow, number formats, localized templates are needed? | Consistent UX and compliance | User research and terminology glossary | Open |
| ODR-033 | Security governance | Approve MFA and session/recovery policies, privileged access governance, incident response ownership, vulnerability remediation targets and security risk-acceptance authority. | Account takeover, privileged misuse, incident impact and production risk | Threat model, risk assessment, operational/legal review and explicit policy approval | Open |
| ODR-034 | Audit and observability governance | Approve durable audit event catalogue, audit/telemetry retention classes, access governance, alert ownership, escalation and incident evidence policy. | Accountability, privacy, investigation readiness, storage cost and operational response | Threat model, data inventory, workload baseline, legal/contract review and incident exercises | Open |

## Approved business decisions

### ODR-001 — One tenant, multiple dairy operating models and branches

**Status:** Resolved — explicit product-owner approval  
**Decision date:** 2026-11-18  
**Approval evidence:** User selected Option B and confirmed no additional changes in the conversation on 2026-11-18.

**Approved decision statement**

One Tenant represents one independent Dairy Business Workspace. A single workspace may operate multiple dairy business models and multiple branches. The operating models are capability descriptions, not mutually exclusive tenant types.

The supported operating-model examples include:
- Milk buying and selling.
- Recurring milk distribution and doorstep delivery.
- Retail/counter sales.
- Farmer milk collection and procurement.
- Milk processing and packaging.
- Dairy-product resale and distribution.
- Combined operations that use multiple models together.

**Conceptual separation**
- Tenant: independent SaaS workspace and isolation boundary.
- Business Profile: descriptive and configurable profile for the workspace.
- Operating-model capabilities: one or more enabled business activities; they must not force a tenant split.
- Branch: operational unit within the tenant.
- OperatingLocation: typed physical/operational place, with its taxonomy and relationships handled separately under ODR-002.

**Alternatives considered**
- Option A — one operating model per tenant: rejected because it unnecessarily constrains businesses that combine or expand operations.
- Option B — multiple operating models and multiple branches per tenant: approved.
- Option C — separate tenant for each operating model: rejected as the default because it fragments a single business workspace and complicates shared operations/data.

**Consequences**
- Product capability configuration must allow multiple operating models in one tenant.
- Enabling an additional operating model must not implicitly create a new tenant or branch.
- Branch count and operating-model count remain independent dimensions.
- The model must support a business evolving from one operating model to a combined model without losing historical records.
- This approval does not decide legal-entity cardinality, tax registrations, accounting-book cardinality, or any legal/regulatory applicability.

**Scope boundary:** Legal form, legal identity verification and Indian legal/regulatory applicability are excluded from the current project-development decision process. They are intended for a separate review after project completion and must not be represented as current development blockers.

**Affected areas:** Project Vision and Scope; tenant/business profile; capability and module configuration; branch model; onboarding; ERS-001; SaaS tenancy architecture; reporting and entitlement design.

**Verification:** Confirm that one tenant can enable multiple operating-model capabilities and contain multiple branches, without treating these dimensions as equivalent or forcing tenant duplication.

**Review trigger:** Reopen only if product scope changes to support multiple independent business workspaces under one tenant, or if an explicit product-owner change is made.

---

### ODR-002 — Accepted conceptual baseline: physical site, operational capabilities and stock locations

**Status:** Partially resolved — the product owner accepted the physical-site/capability baseline, Branch/OperatingLocation relationship, ServiceArea/Branch relationship, Route/Branch relationship and shared-physical-site/branch-specific-stock-segregation baseline on 2026-11-18. Detailed shared-use governance, accountability and access policies remain open.

**Approval evidence:** The product owner explicitly accepted the revised concept after reviewing the single-premises example of a small, sole-owner dairy business.

**Accepted decision statement**

A single physical premises may perform several operational functions. The model must not require a separate physical OperatingLocation for every function or capability.

- **OperatingLocation** represents the actual physical/operational site or premises.
- **Operational Capabilities** represent the functions enabled/performed at that OperatingLocation. Examples include retail/counter sales (Outlet), milk receiving (Collection Centre), warehousing/storage, processing and packaging. These are capabilities/functional classifications, not mandatory separate physical sites.
- **StockLocation** is a separate inventory-control concept representing a controlled stock-holding position, zone, room, shelf, refrigerator, bin or other inventory subdivision. It is not automatically a separate OperatingLocation.
- **ServiceArea** remains a geographic coverage area, not a physical OperatingLocation.
- **Branch** remains a management/operating unit; its exact relationship to OperatingLocation is not yet approved by this partial decision.

**Illustrative application**

A small dairy business may have one Branch and one OperatingLocation at a single address. That same OperatingLocation may have retail, milk collection, storage and processing/packaging capabilities. Inventory may be divided into one or more StockLocations within that premises according to operational need. The system must not force the owner to create multiple physical sites merely to enable those functions.

A larger business may configure multiple OperatingLocations under its approved Branch/tenant structure, with each site having its own combination of capabilities and StockLocations.

**Alternatives rejected for this accepted subdecision**
- Creating a separate physical OperatingLocation for every operational function, even when all functions occur at one premises.
- Treating StockLocation as the same kind of entity as a physical OperatingLocation.

**Consequences**
- One OperatingLocation must support multiple operational-capability assignments without creating separate physical sites.
- A capability being enabled does not by itself create another Branch or OperatingLocation.
- Inventory-control subdivisions must be modelled separately from physical premises.
- Small single-premises businesses and multi-site businesses must both be supported without different conceptual models.
- At the time of this initial subdecision, Branch-to-OperatingLocation cardinality, unassigned locations, shared-site governance, ServiceArea mapping, route relationships and employee access were not yet decided. Subsequent ODR-002 subdecisions in this register approve the Branch/OperatingLocation, ServiceArea/Branch, Route/Branch and shared-site/stock-segregation baselines; detailed governance and access policies remain open.

**Affected areas:** Canonical Business Glossary; Core Domain Entity Catalogue; ERS-001; DBR-002; inventory/location modelling; onboarding and capability configuration.

**Verification:** Confirm that one OperatingLocation can enable multiple operational capabilities, that StockLocation is distinct from OperatingLocation, and that a single-premises dairy workflow does not require fictitious physical sites.

**Review trigger:** Reopen this accepted baseline only if the product owner changes the single-premises/multi-capability requirement. Continue ODR-002 separately for the remaining branch/location relationships and access rules.

---

### ODR-002 — Approved Branch and OperatingLocation relationship

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for detailed shared-use accountability and complete permission-matrix policies.

**Approval evidence:** After reviewing the recommended relationship, the product owner stated: “संकल्पना योग्य असून मला मान्य आहे.”

**Approved decision statement**

- **Branch** is a tenant-owned management/operating unit, not necessarily a physical premises.
- A Branch may be associated with multiple OperatingLocations.
- An OperatingLocation is tenant-owned and may be associated with a Branch; a separate Branch is not mandatory for every physical OperatingLocation.
- A tenant-level/unassigned OperatingLocation is therefore permitted in the conceptual model, subject to explicit operational ownership and access policy.
- If one physical OperatingLocation is used by multiple Branches, that use must be represented through an explicit governed shared-use association. Physical sharing alone does not grant unrestricted access to the other Branch's operational data.
- The one-Branch/many-OperatingLocations model and optional Branch association do not override tenant isolation or existing branch-level authorization constraints.

**Alternatives rejected**
- Requiring every OperatingLocation to be a Branch.
- Requiring every OperatingLocation to be assigned to exactly one Branch with no tenant-level/unassigned option.
- Treating a shared physical site as automatic permission for all users of every associated Branch to see or mutate all data there.

**Consequences**
- Conceptual modelling must distinguish the Branch management boundary from the physical site.
- A tenant may operate with one Branch and one OperatingLocation, or one Branch and multiple OperatingLocations.
- The model must allow tenant-owned OperatingLocations without mandatory Branch assignment.
- Shared use by multiple Branches requires explicit association, authorization, attribution and audit rules; these detailed policies remain to be specified.
- This approval does not finalize ServiceArea-to-Branch cardinality, Route relationships, employee membership multiplicity, or detailed cross-branch read/mutation permissions.

**Affected areas:** DBR-002; Canonical Business Glossary; Core Domain Entity Catalogue; ERS-001; onboarding; location management; operational authorization and reporting.

**Verification:** Confirm that a Branch can be associated with multiple OperatingLocations, an OperatingLocation can exist without mandatory Branch assignment, and shared physical use does not bypass branch data isolation.

**Review trigger:** Reopen this approved relationship only upon explicit product-owner change. Continue ODR-002 for detailed shared-use/access rules.

---

### ODR-002 — Approved ServiceArea and Branch relationship

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for detailed shared-use accountability and complete permission-matrix policies.

**Approval evidence:** The product owner confirmed: “संकल्पना योग्य असून मला मान्य आहे.”

**Approved decision statement**

- A Branch may serve multiple ServiceAreas.
- A ServiceArea may be served by one or more Branches.
- The relationship must be represented through an explicit Branch-to-ServiceArea association; it must not be inferred from physical proximity, OperatingLocation assignment or route membership.
- The association must support effective dates/history so that service-area reassignment does not rewrite historical delivery runs, stops, fulfilments or customer service records.
- A ServiceArea remains a geographic coverage concept, not a Branch or OperatingLocation.
- ServiceArea overlap or multi-branch service does not grant employees cross-branch access. Existing branch-isolation and explicit-scope-grant rules continue to apply.

**Illustrative application**

A Pune West Branch may serve both Kothrud and Bavdhan ServiceAreas. If capacity or demand changes, another Branch may also be authorized to serve Bavdhan. The mapping change applies prospectively from its effective date; completed historical delivery records retain their original branch, route/run and service context.

**Consequences**

- The conceptual relationship is many-to-many, implemented through an explicit governed association rather than a direct single-branch field on ServiceArea.
- The association needs effective-from/effective-to semantics or an equivalent immutable version/history mechanism.
- Operational authorization remains separately enforced at Branch and record level.
- This approval does not decide Route execution/dispatch responsibility, shared physical-site governance, or detailed cross-branch read/mutation permissions.

**Affected areas:** Canonical Business Glossary; Core Domain Entity Catalogue; ERS-001; DBR-002; service-area administration; delivery planning; historical reporting and branch authorization.

**Verification:** Confirm one Branch can serve multiple ServiceAreas, one ServiceArea can be served by multiple Branches, effective-dated reassignment preserves historical records, and shared geography does not bypass branch isolation.

**Review trigger:** Reopen only upon explicit product-owner change or a materially different service-allocation business requirement. Continue ODR-002 separately for Route execution/dispatch responsibility and detailed shared-use/access rules.

---

### ODR-002 — Approved shared OperatingLocation and branch-specific stock segregation

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for detailed shared-use governance and access policies.

**Approval evidence:** The product owner explicitly accepted the arrangement in which multiple Branches can use one physical warehouse/site while their stock remains independently controlled.

**Approved decision statement**

- One actual physical premises remains one OperatingLocation even when multiple Branches use it; do not create duplicate OperatingLocations solely to represent each Branch's use of the same physical space.
- Each Branch using that shared OperatingLocation must have an explicit governed association to it. The association must identify the permitted operational use/capabilities and have an effective period; the detailed authorization and accountability policy remains to be specified.
- StockLocation records/positions must support Branch-specific stock segregation within the same physical OperatingLocation. Separate Branch stock may occupy the same premises without pretending that the physical premises are separate sites.
- Stock ownership, stock balances, reservations, movements, adjustments, counts and transaction histories remain attributable to the appropriate Branch/authorized inventory scope. A shared address or shared room does not merge stock ownership.
- A Branch must not automatically see, reserve, move, adjust, count or otherwise control another Branch's stock merely because both use the same OperatingLocation. Such access requires a separate explicit authorization decision and auditable grant.
- Physical sharing does not grant general access to another Branch's employees, customers, suppliers, transactions, reports or other operational data.
- Historical stock and operational records must preserve the Branch, StockLocation and relevant shared-site context applicable when the transaction occurred; later association changes must not rewrite history.

**Illustrative application**

Branch A and Branch B use the same warehouse building. The building is represented once as an OperatingLocation. Within that premises, Branch A and Branch B maintain distinct StockLocations or otherwise explicitly segregated inventory-control positions. Both may physically store goods in the same room if the inventory positions and custody rules can reliably distinguish ownership. Branch A's staff cannot view or move Branch B's stock by default.

**Consequences**

- One physical OperatingLocation may be associated with multiple Branches through explicit governed shared-use associations.
- Physical-location identity and inventory ownership/control are separate dimensions.
- StockLocation must carry or resolve an explicit Branch/inventory-ownership scope; the exact logical association pattern remains for detailed ERS refinement.
- Shared physical infrastructure must not weaken tenant isolation or branch-level authorization.
- This approval does not define shared-site operational responsibility, staff access, approval authority, stock custody hand-off, shared equipment use, or the complete permission matrix. Those remain open for subsequent specification.

**Affected areas:** Canonical Business Glossary; Core Domain Entity Catalogue; ERS-001; DBR-002; inventory and stock ledger; branch authorization; shared warehouse operations; stock counts, transfers and audit history.

**Verification:** Confirm that two Branches can use one OperatingLocation, maintain separate branch-attributed StockLocations and stock ledgers within the same premises, and cannot access or mutate one another's stock without an explicit grant.

**Review trigger:** Reopen only upon explicit product-owner change or evidence that the shared-site/stock-segregation baseline cannot safely support a required operating workflow. Continue ODR-002 for detailed shared-use governance and access policies.

---

### ODR-002 — Approved limited cross-branch stock-handling access for shared-site workers

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for detailed shared-use governance, accountability and permission-matrix design.

**Approval evidence:** The product owner accepted that workers operating at a shared physical site may handle stock for more than one Branch where needed, provided access is explicit, limited and auditable and does not expose the other Branch's complete data.

**Approved decision statement**

- A worker may be authorized to perform stock-handling work for more than one Branch at a shared OperatingLocation without becoming a general member of every such Branch or receiving unrestricted cross-branch access.
- Each cross-branch grant must explicitly identify the worker/principal, target Branch, applicable shared OperatingLocation and/or Branch-specific StockLocation, permitted operational actions, effective period and granting authority.
- Permissions must be action-specific and least-privilege. Permission to receive, put away, pick, transfer, count or otherwise handle stock must not imply permission for unrelated actions such as price changes, purchasing, stock adjustments, write-offs, approvals, exports or financial postings unless separately granted.
- The worker may see only the minimum stock and task information needed to perform the authorized action. The grant does not expose the target Branch's general customer, supplier, employee, sales, purchasing, financial, reporting or other operational records.
- Stock ownership and transaction attribution remain with the owning Branch even when a worker from another Branch performs an authorized physical task. The system must retain both the stock-owning Branch and the acting worker's identity/normal branch context in the audit trail.
- Grants must be revocable, effective-dated and auditable. Expiry or revocation must prevent subsequent actions without rewriting already completed stock movements or historical records.
- A shared physical location, shared shift, shared-site user or physical proximity alone never creates a permission grant.

**Illustrative application**

A warehouse worker normally assigned to Branch A is also authorized to pick and receive stock for Branch B at their shared warehouse. The worker can see the relevant Branch B stock positions and task lines needed for those assigned tasks and can record the permitted actions. The worker cannot browse Branch B's customer list, supplier terms, employee records, sales history, financial reports or perform stock adjustments/approvals unless separate permissions explicitly authorize those actions.

**Consequences**

- Cross-branch workforce access is a scoped operational delegation, not automatic multi-branch membership.
- Authorization checks must include actor, target Branch, shared-site/StockLocation context, action, effective time and any required approval state.
- Audit events must preserve actor identity, actor's home/assigned Branch, target stock-owning Branch, target StockLocation, action, source task/document and grant reference.
- APIs, search, mobile workflows, barcode scanning, reports, exports, background jobs, integrations and caches must enforce the same grant boundaries; UI hiding alone is insufficient.
- This approval does not settle general employee multi-branch membership, all-branch visibility, approval delegation, or complete HR/payroll access rules. Emergency Override is resolved by the separately approved ODR-002 policy below.

**Verification:** Confirm that a worker with a Branch A home assignment can execute only the specifically granted stock tasks for Branch B at the approved shared site; cannot browse unrelated Branch B records; cannot perform ungranted stock mutations; and every permitted action is attributable to the worker, stock-owning Branch and grant.

**Review trigger:** Reopen only upon explicit product-owner change or a material change to the shared-site operating model. Continue ODR-002 for complete permission matrix, shared-site accountability and exception/revocation workflows.

---

### ODR-002 — Approved Route and Branch relationship

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for detailed shared-use accountability and complete permission-matrix policies.

**Approval evidence:** The product owner explicitly confirmed that the proposed Route/Branch structure was acceptable.

**Approved decision statement**

- Each reusable/planned Route belongs to exactly one Branch.
- A Branch may own multiple Routes.
- A Route may include delivery stops across one or more ServiceAreas, but only where its owning Branch has an effective Branch-ServiceArea assignment for each relevant ServiceArea.
- A DeliveryRun is associated with one Branch and a Route owned by that same Branch; the Branch/Route combination must be validated as consistent.
- A Route is not implicitly shared with another Branch. If another Branch serves the same ServiceArea, it must use a Route of its own.
- Route revisions and service-allocation changes must not rewrite completed DeliveryRuns, DeliveryStops, fulfilments or their historical operational context. Execution records retain the route/plan version and relevant context used at dispatch.

**Illustrative application**

Pune West Branch may own separate Kothrud and Bavdhan Routes, or a combined route covering both areas if it is operationally appropriate and the Branch is authorized to serve both. If another Branch also serves Bavdhan, that Branch creates and operates its own Route; it does not run the Pune West Route merely because the geographic ServiceArea overlaps.

**Consequences**

- Conceptual cardinality: Branch 1:N Route.
- Each Route has one owning Branch; Route-to-ServiceArea coverage may be 1:N through the Branch's effective service assignments.
- DeliveryRun must reference a Branch and a Route whose ownership matches that Branch.
- Geographic overlap does not grant route ownership, dispatch authority or cross-branch data access.
- This approval does not decide detailed dispatch assignment, driver/vehicle allocation, route optimization, multi-shift route reuse, shared-site accountability or the complete employee cross-branch permission matrix. A limited shared-site stock-task grant is approved separately under ODR-002; it does not create general cross-branch membership or access.

**Affected areas:** Canonical Business Glossary; Core Domain Entity Catalogue; ERS-001; DBR-002; route planning; delivery dispatch; delivery-run history; branch authorization.

**Verification:** Confirm one Branch can own multiple Routes, each Route has one Branch owner, a Route can cover multiple ServiceAreas only within that Branch's authorized service assignments, DeliveryRun Branch and Route ownership always match, and another Branch must use its own Route.

**Review trigger:** Reopen only upon explicit product-owner change or a materially different route operating model. Continue ODR-002 separately for detailed shared-use accountability and complete permission-matrix policies.

---

## Decision closure protocol
For every item, record the decision statement; classification; chosen baseline; alternatives; evidence/source and date; rationale; consequences/risks; affected documents/modules; verification method; decision owner; approval requirement; and review trigger/date.

1. Close matters already determined by explicit product-owner constraints immediately and propagate them to dependent documents.
2. For engineering choices that do not require a business/legal commitment, select and document a conservative, reversible, evidence-based recommended baseline. Do not leave a generic question merely because several technologies exist.
3. For business policy, pricing/plan, staffing, legal applicability, contractual commitments, production provider/account, or named accountability, do not impersonate owner approval. Provide a safe interim default and a precise approval item with options and consequences.
4. For legal/tax/security/provider claims, use current authoritative sources and record jurisdiction, effective date, applicability and confidence. If evidence is incomplete, isolate the uncertainty and specify what must be verified.
5. An item may remain open only when a specific external fact or accountable human approval is genuinely indispensable. Assign an owner and due date/production gate; otherwise close or reclassify it.
6. Update every dependent requirement, workflow, architecture, test and index in the same change set where possible. Run a cross-document contradiction and broken-link review before declaring completion.


## Decision status classification
- **Resolved — project constraint:** Explicitly settled by the product owner or fixed project boundary; propagate as a requirement.
- **Recommended baseline:** Engineering default selected from available evidence for design and implementation planning; not a contractual SLA or owner-approved commercial/legal policy.
- **Owner approval required:** A named business owner must approve a commercial, operational, risk-acceptance or accountability commitment before production.
- **Evidence required:** A current external fact, provider capability or legal applicability question must be verified from authoritative evidence.
- **Deferred with trigger:** Intentionally postponed with a specific trigger, owner and review date; it is not an unresolved design question.


### ODR-002 — approved authority for cross-branch shared-site stock-task grants

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for the remaining shared-site accountability, grant lifecycle/exception handling and complete permission-matrix policies.

**Approval evidence:** The product owner explicitly accepted that the authorized user holding the required approval permission of the stock-owning Branch must approve a grant allowing a worker from another Branch to handle that stock at a shared OperatingLocation.

**Approved decision statement**

- Approval by an authorized user holding the required approval permission of the Branch that owns the target stock is mandatory before a cross-branch shared-site stock-task grant becomes effective.
- The approval is specific to the grant's worker, target/stock-owning Branch, shared OperatingLocation and/or StockLocation, permitted actions and effective period. A materially changed grant requires approval of the changed scope before it is used.
- The grant must not become effective merely because a worker's home Branch, a shared-site user, or another party has approved it.
- Whether the worker's home Branch must also provide consent is not decided by this approval. It remains a separately configurable/policy decision to be specified; home-Branch consent must not be represented as universally mandatory or universally unnecessary.
- The approving authorized user must hold an explicitly authorized approval scope for the stock-owning Branch. A job title alone is insufficient.
- Approval authority does not expand the grant's action scope or expose unrelated data belonging to the stock-owning Branch.

**Consequences**

- Grant creation/activation must retain the approving actor, approval time, approved grant version/scope and relevant authorization evidence.
- Revocation and expiry follow the approved ODR-002 lifecycle subdecision: stop new tasks and further stock mutations immediately, preserve completed history, and hold in-progress tasks for controlled review/reconciliation. Scope changes require approval before use. Emergency Override and delegated approval are approved separately below.
- The worker's home-Branch membership and the stock-owning Branch's stock ownership remain distinct.

**Affected areas:** ERS-001; Core Domain Entity Catalogue; DBR-002; authorization/approval workflow; shared-site stock operations; audit trail and acceptance tests.

**Verification:** Confirm that a grant cannot be activated without approval from an authorized user holding the required approval permission of the stock-owning Branch; that the approval is bound to the exact grant scope/version; that home-Branch consent is not assumed; and that the grant cannot authorize actions or data beyond its approved least-privilege scope.

**Review trigger:** Reopen only on explicit product-owner change or when defining the complete grant lifecycle, delegated approvals, grant-expiry notifications/grace periods or home-Branch consent policy.


### ODR-002 — approved shared-site stock-task grant revocation, expiry and in-progress task handling

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for grant-expiry notifications/grace periods, home-Branch consent, complete permission matrix and detailed shared-site accountability.

**Approval evidence:** The product owner accepted the recommended lifecycle baseline: revocation/expiry stops new work, completed history remains immutable, and in-progress work is held for controlled review rather than silently completed or rolled back.

**Approved decision statement**

- A grant becomes unusable for new stock tasks immediately when it is revoked or reaches its effective end/expiry. Authorization checks must use the current grant state and effective period, not a stale UI/session/cache copy.
- Revocation or expiry also prevents further stock-affecting actions under that grant. No new receipt, put-away, pick, issue, transfer, count adjustment, reservation or other stock mutation may be authorized solely by the ended grant.
- Tasks already completed remain historically intact. Their stock movements, actor attribution, owning-Branch attribution, grant reference and audit history must not be deleted, silently rewritten or automatically reversed because the grant later ended.
- An in-progress task must not be auto-completed, auto-rolled-back or treated as successfully completed. It enters a held/review-required state, preserving completed steps, quantities, custody evidence and outstanding work.
- Further stock-affecting steps on a held task require a currently valid grant that covers the exact worker, target stock scope and action, or reassignment to another currently authorized worker. The authorized user holding the required approval permission of the stock-owning Branch, or a currently valid delegate under the approved ODR-002 delegated-approval policy, decides whether to reauthorize through a new/valid grant, reassign the remaining work, or cancel/close it through a controlled reconciliation process.
- Cancellation/closure must reconcile any partial physical handling and stock effects through explicit compensating/correction records where required; it must never erase the original events or fabricate a rollback. Any residual custody/quantity discrepancy remains visible for resolution.
- Non-mutating safety/incident reporting and audit capture may continue while a task is held, but this does not authorize stock movement or changes to stock balances.
- Grant revocation/expiry, task hold, review decision, reassignment, reauthorization and closure must be timestamped and attributable, with the reason and related grant/task references retained.

**Consequences and controls**

- Authorization must be rechecked server-side at each stock-affecting transition, including API, mobile/barcode, background jobs, retries and integrations. Cached authorization must not permit post-expiry/revocation writes.
- Task state transitions and stock effects must be concurrency-safe and idempotent. A revocation racing with a stock operation must not allow an operation to commit after the grant is no longer valid; the transaction boundary must revalidate authorization atomically with the stock-affecting commit.
- No automatic distributed rollback or exactly-once guarantee is implied. Recovery uses explicit reconciliation and append-only correction/compensation evidence.
- This approval does not decide grant-expiry notification timing/channels, grace periods or the separate home-Branch consent policy. Delegated approval is resolved by the separately approved ODR-002 policy below.

**Affected areas:** ERS-001; Core Domain Entity Catalogue; DBR-002; grant lifecycle; stock task state machine; stock movement integrity; concurrency/idempotency; audit and acceptance tests.

**Verification:** Test immediate denial of new work after revocation/expiry; denial of all further stock mutations under the ended grant; immutable completed history; preservation and hold of partial work; safe reauthorization/reassignment/cancellation; auditable reconciliation; and a race test where revocation overlaps a stock-affecting commit.

**Review trigger:** Reopen only on explicit product-owner change or when specifying notification/grace-period policy, home-Branch consent or detailed shared-site accountability.


### ODR-002 — approved emergency override for shared-site stock-handling tasks

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. This closes the emergency-override policy question within ODR-002; it does not close grant-expiry notifications/grace periods, home-Branch consent, the complete permission matrix or detailed shared-site accountability.

**Approval evidence:** The product owner approved the recommended emergency-override safeguards and confirmed that authorization is permission-based: authority follows explicit permissions, not job titles or named roles.

**Purpose and activation threshold**

An Emergency Override is a narrowly scoped, temporary exception for an immediate threat to human safety, milk/product quality, refrigeration integrity, contamination control, physical safety or material stock loss where waiting for the ordinary grant-approval path would create material harm. It is not a convenience path for workload pressure, routine absence, delayed administration or ordinary operational urgency.

**Initiation and authorization**

- Any authenticated user may report an emergency and request intervention. Reporting an incident does not itself grant access or authorize a stock action.
- Under the normal path, an authorized user holding the required approval permission for the stock-owning Branch must approve the exact emergency scope before activation. The initiator must not approve their own request where independent approval is required.
- If the relevant ordinary approver is unavailable and delay would create material harm, a predesignated Emergency Authority may activate a restricted self-activation override only where that user has an explicit, current `emergency_override.activate` permission (or an equivalently explicit permission in the final permission catalogue) covering the relevant tenant/Branch scope.
- The Emergency Authority designation is an authorization assignment, not a job title. No title, seniority, physical presence or general administrator status is sufficient by itself.
- If no appropriately scoped Emergency Authority is available, the system must not silently permit an override. Users may take immediate physical safety actions outside the software, report the incident and preserve evidence, but unauthorized stock mutations remain prohibited.

**Minimum permitted scope**

An override must be bound to the named actor, stock-owning Branch, affected OperatingLocation and StockLocation, affected stock/batch/task references, permitted actions and a short effective period. It may allow only the minimum actions needed to protect or secure the affected stock, such as viewing relevant task/stock details, protective movement to a safe controlled location, emergency receiving/put-away where necessary to prevent harm, and quarantine/isolation.

It does not grant general Branch membership or unrelated Branch data access. It must not authorize stock adjustments, write-offs, valuation changes, price changes, purchasing, sales, customer/supplier access, financial postings, exports, permission changes, grant administration or approval of the actor's own actions. Any additional action requires a separate valid permission and approval path. Stock ownership remains with the stock-owning Branch.

**Validity and extension**

- Initial validity is limited to 60 minutes from activation.
- One explicit extension of up to a further 60 minutes is permitted; total validity must never exceed 120 minutes from initial activation.
- There is no automatic renewal. Extension requires a recorded reason and fresh authorization by a different authorized user holding the relevant emergency-extension permission. A self-activating Emergency Authority cannot approve their own extension.
- If the required extension authorization is unavailable, the override expires and the task follows the approved hold/reconciliation process.

**Misuse prevention and review**

- Activation requires a specific emergency reason, incident reference where available, affected Branch/location/stock/task, exact action scope and expiry.
- The system must enforce least privilege, server-side checks, no privilege inheritance from job titles, no self-approval outside the narrowly defined self-activation exception, and immediate revocation capability.
- Every activation, denied attempt, use, extension, revocation and expiry must be auditable.
- Every self-activated override requires retrospective review by a different authorized user holding the relevant review permission. Review must assess necessity, scope, actions, quantities, evidence and any policy breach; findings and corrective actions remain linked to the incident and override.
- The review should be completed within one business day after the override ends. A suspected misuse, unexplained stock discrepancy, safety incident or material control failure must be escalated immediately under the incident process.

**Mandatory audit evidence**

Retain the override and incident identifiers; initiator/actor identity and normal Branch context; stock-owning Branch; OperatingLocation and StockLocation; affected stock, batch and task references; emergency reason and supporting evidence; activation mode (ordinary approval or restricted self-activation); approver or Emergency Authority identity and permission evidence; approval/activation timestamps; exact approved actions and scope/version; start, expiry and extension details; each attempted and completed action; quantities and custody/location changes; request/correlation/idempotency references; revocation/expiry; retrospective reviewer, review time, findings and corrective actions; and all reconciliation records. Never log credentials, tokens or secret values.

**Expiry, partial work and stock reconciliation**

Expiry or revocation immediately blocks new tasks and further stock-affecting actions under the override. Completed transactions and audit history remain immutable. In-progress tasks are held for controlled review; they are not automatically completed or rolled back. Completed steps, quantities, physical custody evidence and outstanding work must be preserved. Further stock mutations require a valid ordinary grant or authorized reassignment. The stock-owning Branch's authorized user holding the relevant review/reconciliation permission determines reauthorization, reassignment or controlled closure. Physical/system discrepancies and partial handling must be resolved through explicit correction/compensation records; original events must never be erased or silently rewritten. Non-mutating safety/incident reporting and evidence capture may continue.

**Concurrency and enforcement**

Authorization must be revalidated server-side at every stock-affecting transition across APIs, mobile/barcode clients, jobs, retries and integrations. Validation of the active override, its scope and expiry must be coordinated atomically with the stock-affecting commit. Expiry/revocation racing with an in-flight operation must not permit a write to commit after authorization has ceased. Idempotency must prevent duplicate emergency actions; stale UI/session/cache state and offline clients must never authorize a write. No automatic distributed rollback or exactly-once guarantee is implied; uncertain outcomes require explicit reconciliation.

**Affected areas:** ARC-IAM-001; audit/observability; ERS-001; Core Domain Entity Catalogue; Canonical Business Glossary; shared-site stock workflows; concurrency/idempotency; incident review and acceptance tests.

**Verification:** Test ordinary approval, restricted self-activation only by a specifically authorized Emergency Authority, denial when no such authority exists, exact least-privilege scope, no unrelated data access, 60-minute expiry, one independently authorized extension with a 120-minute hard maximum, immediate revocation, immutable completed records, held partial tasks, review evidence, discrepancy reconciliation, API/mobile/job/integration enforcement, idempotency and expiry/revocation race conditions.

**Review trigger:** Reopen only upon explicit product-owner change or evidence from operational testing that the approved emergency safeguards are unsafe or insufficient. Notifications/grace periods, home-Branch consent, complete permission matrix and detailed shared-site accountability remain separate ODR-002 work items.


### ODR-002 — approved permission-based delegated approval

**Status:** Approved subdecision — explicit product-owner approval in the conversation on 2026-11-19. This resolves the delegated-approval policy question for the ODR-002 shared-site stock-task grant workflow. It does not close grant-expiry notifications/grace periods, home-Branch consent, the complete permission matrix or detailed shared-site accountability. General tenant-wide approval policy and the broader ODR-004/005 permission/delegation matrix remain subject to their own scope.

**Approval evidence:** The product owner explicitly approved all recommendations: separate delegation permission, exact scoped authority, maximum 30-day validity, no automatic renewal or onward delegation, non-delegable high-risk actions, immediate invalidation when the delegator's underlying authority ceases, auditability and concurrency safeguards.

**Purpose and governing principle**

Delegated Approval allows an authorized user to temporarily assign a narrowly defined approval capability to another eligible user when continuity of an approved workflow requires it. It is not a transfer of the delegator's identity, role, general permissions, data access or unrestricted approval authority. Authority is determined by effective permissions and explicit scope, never by job title, seniority or role label.

**Who may delegate and who may receive delegation**

- The delegator must hold both the effective approval permission for the exact approval category/resource and scope, and a separate explicit `approval.delegate` permission covering that same scope.
- The delegate must be an active, authenticated user within the same Tenant and must be eligible to act within the delegated Tenant/Branch/resource scope. A delegation does not create general Branch membership or grant unrelated data visibility.
- The delegation record itself is the explicit, temporary authorization for the named delegate to perform only the specified approval action(s). Any information needed to decide must be exposed through the delegate's independently permitted access or a minimum, permission-filtered approval packet; the delegation must not silently widen read access.
- A delegator cannot delegate an authority they do not currently hold, delegate more broadly than their own scope, or delegate to themselves.

**Required delegation scope**

Every delegation must explicitly identify:
- Delegation identifier and status.
- Delegator and delegate identities.
- Tenant and, where applicable, Branch/resource scope.
- Approval category and exact action(s) that may be approved.
- Applicable amount, quantity, variance, risk or other approval limits; the effective limit is the lowest applicable limit among the delegator's current authority, the delegation record and the governing approval policy.
- Effective start and end timestamps.
- Reason and creation/revocation evidence.

A delegation authorizes only the approve action within the stated scope. It does not confer submit, create, modify, execute, export, permission administration or general record-access rights unless those are independently granted. It cannot waive required multi-person approval or separation-of-duties checks.

**Validity, revocation and automatic invalidation**

- Maximum validity is 30 calendar days from its effective start. The effective end is exclusive.
- There is no automatic renewal and no extension of an existing delegation. A new delegation record, with fresh authorization and audit evidence, is required for any further period.
- The delegator or an authorized user holding a specific delegation-revocation permission may revoke it immediately.
- The delegation becomes unusable immediately if the delegator's underlying approval permission, `approval.delegate` permission, relevant scope assignment or Tenant membership is revoked, suspended, expires or is narrowed so that the delegated scope is no longer covered.
- It also becomes unusable if the delegate's account or Tenant membership is suspended, revoked or expires, or if the delegation itself expires or is revoked.
- Revocation/expiry prevents new approval decisions. An approval already committed while the delegation was valid remains an immutable historical event; an uncommitted/in-flight approval must be revalidated and rejected if the delegation is no longer valid.

**Non-delegable actions and safeguards**

The following actions are non-delegable under this baseline: permission/role/access administration; Emergency Override activation, extension and retrospective review; stock adjustments and write-offs; financial-period close/reopen; and sensitive data deletion. No onward delegation, subdelegation, circular delegation or self-delegation is permitted. A delegated approver cannot approve an action they initiated, and cannot approve the creation, amendment or approval of their own delegation. Delegation does not bypass maker-checker, independent approval, policy thresholds or any other required approval stage.

Other approval categories are delegable only where the governing approval policy explicitly marks the category/action as delegable. Absence from the non-delegable list does not by itself make an action delegable.

**Audit requirements**

Record delegation creation, change attempt, approval/authorization evidence, activation, use, denied use, revocation, automatic invalidation and expiry. Each delegated approval must retain the actual delegate actor, delegator, delegation identifier/version, approval category/resource, exact payload/version/hash and policy version approved, applicable limits, decision, timestamp, reason/comment and request/correlation/idempotency references. Audit history must distinguish the actual actor from the person who delegated the authority. Never log credentials, tokens or secret values.

**Concurrency and enforcement**

Delegation validity, current delegator authority, delegate membership, scope, action, limits, approval payload version and policy version must be revalidated server-side at the approval commit boundary. The validity check and approval commit must be coordinated atomically so a revocation, expiry, membership suspension or authority reduction racing with an approval cannot authorize a decision after the delegation ceased to be valid. Retries must be idempotent and cannot create duplicate approval decisions. Stale UI/session/cache state and offline clients cannot authorize an approval.

**Affected areas:** ERS-001; ARC-IAM-001; audit/observability; concurrency/transaction integrity/idempotency; Actor and Access Model; Core Domain Entity Catalogue; Canonical Business Glossary; approval workflow policy and acceptance tests.

**Verification:** Test creation without `approval.delegate`; delegation exceeding delegator scope/limits; inactive or cross-Tenant delegate; exact Branch/resource/action scoping; 30-day hard maximum; no renewal/extension; immediate revocation; automatic invalidation on either party's membership or relevant permission/scope loss; all non-delegable actions; self-approval and subdelegation denial; multi-stage approval preservation; payload/policy changes; audit attribution; duplicate retries; and revocation/expiry races at commit.

**Review trigger:** Reopen only upon explicit product-owner change or evidence that the approved constraints are unsafe or insufficient. Grant-expiry notifications/grace periods, home-Branch consent, complete permission matrix and detailed shared-site accountability remain separate ODR-002 work items.
