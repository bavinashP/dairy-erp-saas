# Open Decisions Register

**Status:** Initial register  
**Rule:** Do not leave a decision generically open. Every item must be classified as (a) resolved by an explicit project constraint, (b) recommended engineering baseline, (c) owner approval required, (d) external evidence/legal applicability required, or (e) intentionally deferred with a trigger and review date. Never label a recommendation as user-approved. For owner/evidence dependencies, document the safe interim baseline and the exact decision, owner, evidence, impact and latest decision date.

| ID | Area | Open decision / question | Why it matters | Required evidence / investigation | Status |
|---|---|---|---|---|---|
| ODR-001 | Business identity and operating model | A tenant represents one Dairy Business Workspace. May that workspace support multiple dairy operating models and multiple branches? | Tenant onboarding, capability configuration, module availability and future extensibility | Product-owner decision recorded below; legal form and regulatory applicability are explicitly outside the current development scope and will be reviewed after project completion | **Resolved — user-approved project decision (2026-11-18): Option B.** One tenant supports multiple operating models and multiple branches. |
| ODR-002 | Branch and operating-location model | Define Branch, OperatingLocation, its operational capabilities, StockLocation, ServiceArea and their relationships. | Avoids conflating management units, physical sites, operational functions, inventory sublocations and geographic coverage | Approved: one physical site may support multiple capabilities; StockLocation is separate; Branch is a management/operating unit; one Branch may be associated with multiple OperatingLocations; OperatingLocation may be unassigned or used by multiple Branches through explicit governed associations; one Branch may serve multiple ServiceAreas and one ServiceArea may be served by multiple Branches through explicit effective-dated mapping; each Route belongs to exactly one Branch and DeliveryRun must use a Route belonging to its Branch; shared physical premises may be used by multiple Branches while each Branch retains separately attributable StockLocations, stock ownership, stock control and transaction records. Physical sharing grants no cross-branch stock/data access. A shared-site worker may perform specifically authorized stock-handling actions for more than one Branch only through an explicit, limited, effective-dated and auditable grant; this does not grant general branch membership or visibility into the other Branch's full operational data. Detailed grant lifecycle, accountability and complete permission matrix remain to be specified | **Partially resolved — core physical-site, Branch/OperatingLocation, ServiceArea/Branch, Route/Branch and shared-site/stock-segregation baselines approved by product owner (2026-11-18); detailed shared-use governance and access policies remain open.** |
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
| ODR-019 | Mobile apps | ARC-MOB-001 selects one unified, role-aware mobile app as the initial reversible baseline; separate role-focused apps require evidence of material security, usability, distribution or release-management benefit. | Avoids fragmented releases and duplicated client behavior while preserving role-specific workspaces | Validate representative owner, manager, delivery, POS, procurement, inventory and finance journeys before UX freeze; compare total lifecycle cost if separate apps are proposed | Recommended baseline; validate role journeys before UX freeze |
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

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for detailed shared-use/access policies.

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

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for detailed shared-use/access policies.

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
- A shared physical location, shared shift, shared manager or physical proximity alone never creates a permission grant.

**Illustrative application**

A warehouse worker normally assigned to Branch A is also authorized to pick and receive stock for Branch B at their shared warehouse. The worker can see the relevant Branch B stock positions and task lines needed for those assigned tasks and can record the permitted actions. The worker cannot browse Branch B's customer list, supplier terms, employee records, sales history, financial reports or perform stock adjustments/approvals unless separate permissions explicitly authorize those actions.

**Consequences**

- Cross-branch workforce access is a scoped operational delegation, not automatic multi-branch membership.
- Authorization checks must include actor, target Branch, shared-site/StockLocation context, action, effective time and any required approval state.
- Audit events must preserve actor identity, actor's home/assigned Branch, target stock-owning Branch, target StockLocation, action, source task/document and grant reference.
- APIs, search, mobile workflows, barcode scanning, reports, exports, background jobs, integrations and caches must enforce the same grant boundaries; UI hiding alone is insufficient.
- This approval does not settle general employee multi-branch membership, managerial all-branch visibility, emergency override, approval delegation, or complete HR/payroll access rules.

**Verification:** Confirm that a worker with a Branch A home assignment can execute only the specifically granted stock tasks for Branch B at the approved shared site; cannot browse unrelated Branch B records; cannot perform ungranted stock mutations; and every permitted action is attributable to the worker, stock-owning Branch and grant.

**Review trigger:** Reopen only upon explicit product-owner change or a material change to the shared-site operating model. Continue ODR-002 for complete permission matrix, shared-site accountability and exception/revocation workflows.

---

### ODR-002 — Approved Route and Branch relationship

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 as a whole remains partially open for detailed shared-use/access policies.

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
- This approval does not decide detailed dispatch assignment, driver/vehicle allocation, route optimization, multi-shift route reuse, shared-site governance or employee cross-branch permissions.

**Affected areas:** Canonical Business Glossary; Core Domain Entity Catalogue; ERS-001; DBR-002; route planning; delivery dispatch; delivery-run history; branch authorization.

**Verification:** Confirm one Branch can own multiple Routes, each Route has one Branch owner, a Route can cover multiple ServiceAreas only within that Branch's authorized service assignments, DeliveryRun Branch and Route ownership always match, and another Branch must use its own Route.

**Review trigger:** Reopen only upon explicit product-owner change or a materially different route operating model. Continue ODR-002 separately for detailed shared-use/access policies.

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
