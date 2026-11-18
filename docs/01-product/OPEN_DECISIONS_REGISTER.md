# Open Decisions Register

**Status:** Initial register  
**Rule:** Do not leave a decision generically open. Every item must be classified as (a) resolved by an explicit project constraint, (b) recommended engineering baseline, (c) owner approval required, (d) external evidence/legal applicability required, or (e) intentionally deferred with a trigger and review date. Never label a recommendation as user-approved. For owner/evidence dependencies, document the safe interim baseline and the exact decision, owner, evidence, impact and latest decision date.

| ID | Area | Open decision / question | Why it matters | Required evidence / investigation | Status |
|---|---|---|---|---|---|
| ODR-001 | Business identity and operating model | A tenant represents one Dairy Business Workspace. May that workspace support multiple dairy operating models and multiple branches? | Tenant onboarding, capability configuration, module availability and future extensibility | Product-owner decision recorded below; legal form and regulatory applicability are explicitly outside the current development scope and will be reviewed after project completion | **Resolved — user-approved project decision (2026-11-18): Option B.** One tenant supports multiple operating models and multiple branches. |
| ODR-002 | Branch and operating-location model | Define Branch, OperatingLocation, its operational capabilities, StockLocation, ServiceArea and their relationships. | Avoids conflating management units, physical sites, operational functions, inventory sublocations and geographic coverage | Approved: one physical site may support multiple capabilities; StockLocation is separate; Branch is a management/operating unit; one Branch may be associated with multiple OperatingLocations; OperatingLocation may be unassigned or used by multiple Branches through explicit governed associations; one Branch may serve multiple ServiceAreas and one ServiceArea may be served by multiple Branches through explicit effective-dated mapping; each Route belongs to exactly one Branch and DeliveryRun must use a Route belonging to its Branch; shared physical premises may be used by multiple Branches while each Branch retains separately attributable StockLocations, stock ownership, stock control and transaction records. Physical sharing grants no cross-branch stock/data access. A shared-site worker may perform specifically authorized stock-handling actions for more than one Branch only through an explicit, limited, effective-dated and auditable grant; this does not grant general branch membership or visibility into the other Branch's full operational data. The authorized user holding the required approval permission of the stock-owning Branch, or a currently valid delegate under the approved ODR-002 delegated-approval policy, must approve each cross-branch stock-task grant before it becomes effective; a no-blanket-consent baseline with separate workforce-assignment authorization is recommended in section 16.6. Grant-expiry notification timing and zero-grace baseline are recommended in section 16.5; notification channel/provider delivery remains under ODR-021. The separate workforce-assignment boundary is recommended in section 16.6; detailed shared-site accountability and exception-handling workflows remain open; the complete permission matrix is resolved in section 16.7. Delegated approval is resolved by the separately approved policy below | **Partially resolved — core physical-site, Branch/OperatingLocation, ServiceArea/Branch, Route/Branch, shared-site/stock-segregation, limited cross-Branch stock-task grant, delegated approval, grant lifecycle, expiry and workforce-assignment boundaries are resolved; the complete cross-Branch permission matrix and permission-first customer configurability are approved in section 16.7 (2026-11-19). Detailed shared-use custody/accountability and exception-handling workflows remain open.** |
| ODR-003 | Tenant hierarchy | Can one tenant contain multiple legal entities or GST registrations? | Tax, accounting, ownership | Legal/tax research and business scenarios | Open |
| ODR-004 | User membership | Define multi-Tenant membership and within-Tenant Branch assignments. | Identity, authorization, session context and workforce operations | Apply the permission-first membership model in section 16.9; retain independent Tenant isolation and explicit Branch permissions | **Resolved — recommended membership baseline selected under the product owner's delegated decision mandate (2026-11-19); see section 16.9.** |
| ODR-005 | Authorization | Define the exhaustive atomic permission catalogue across all dairy modules and remaining domain approval/scope policies. The permission-first model, tenant configurability, fail-closed precedence and cross-Branch stock-task matrix are approved under ODR-002 section 16.7 and ARC-PV-001. | Cross-module access and segregation of duties | Complete module-by-module permission inventory, sensitive-field permissions, domain approval policies and automated coverage | **Partially resolved — cross-cutting model and cross-Branch baseline resolved; module-level permission catalogue and domain-specific approval rules remain in progress.** |
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
| ODR-019 | Mobile apps | ARC-MOB-001 selects one unified, permission-, task- and context-aware mobile app as the initial reversible baseline; separate task-focused apps require evidence of material security, usability, distribution or release-management benefit. | Avoids fragmented releases and duplicated client behavior while preserving permission-, task- and context-specific workspaces | Validate representative owner, branch-operations, delivery, POS, procurement, inventory and finance journeys before UX freeze; compare total lifecycle cost if separate apps are proposed | Recommended baseline; validate role journeys before UX freeze |
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
| ODR-035 | Product variability and architecture evolution | Establish one product-wide policy for fixed invariants, customer configuration/customization, extensibility, module boundaries and scale from small to enterprise. | Prevents customer-specific forks, uncontrolled customization, premature microservices and future migration barriers | Product-owner direction and ARC-PV-001; validate through module, NFR, compatibility and scale reviews | **Resolved product direction — owner instructed independent decisions and approved the stability/configurability/extensibility principle on 2026-11-19; engineering baseline selected in ARC-PV-001.** |

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
- This approval does not define detailed shared-site operational responsibility, stock custody hand-off or shared-equipment accountability; those remain open for subsequent specification. The complete cross-Branch permission matrix is separately approved under ODR-002 section 16.7.

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
- This approval does not settle general employee multi-branch membership, all-branch visibility or complete HR/payroll access rules. Emergency Override and delegated approval are resolved by their separately approved ODR-002 policies below.

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
- A no-blanket-Home-Branch-consent baseline, with separate workforce-assignment authorization when scheduling or duty allocation changes, is recommended in section 16.6. This does not alter the stock-owning Branch's mandatory grant approval.
- The approving authorized user must hold an explicitly authorized approval scope for the stock-owning Branch. A job title alone is insufficient.
- Approval authority does not expand the grant's action scope or expose unrelated data belonging to the stock-owning Branch.

**Consequences**

- Grant creation/activation must retain the approving actor, approval time, approved grant version/scope and relevant authorization evidence.
- Revocation and expiry follow the approved ODR-002 lifecycle subdecision: stop new tasks and further stock mutations immediately, preserve completed history, and hold in-progress tasks for controlled review/reconciliation. Scope changes require approval before use. Emergency Override and delegated approval are approved separately below.
- The worker's home-Branch membership and the stock-owning Branch's stock ownership remain distinct.

**Affected areas:** ERS-001; Core Domain Entity Catalogue; DBR-002; authorization/approval workflow; shared-site stock operations; audit trail and acceptance tests.

**Verification:** Confirm that a grant cannot be activated without approval from an authorized user holding the required approval permission of the stock-owning Branch; that the approval is bound to the exact grant scope/version; that the recommended separation in section 16.6 is enforced; and that the grant cannot authorize actions or data beyond its approved least-privilege scope.

**Review trigger:** Reopen only on explicit product-owner change or when reviewing the recommended workforce-assignment separation in section 16.6 or defining detailed shared-site accountability.


### ODR-002 — approved shared-site stock-task grant revocation, expiry and in-progress task handling

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for the complete permission matrix and detailed shared-site accountability. The Home-Branch/workforce-assignment boundary is recommended in section 16.6. A recommended expiry-notification/zero-grace baseline is specified in section 16.5; channel/provider delivery remains under ODR-021.

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
- This approval does not decide notification provider/channel delivery, which remains under ODR-021. The no-blanket-Home-Branch-consent and separate workforce-assignment baseline is recommended in section 16.6. Delegated approval is resolved by the separately approved ODR-002 policy below.

**Affected areas:** ERS-001; Core Domain Entity Catalogue; DBR-002; grant lifecycle; stock task state machine; stock movement integrity; concurrency/idempotency; audit and acceptance tests.

**Verification:** Test immediate denial of new work after revocation/expiry; denial of all further stock mutations under the ended grant; immutable completed history; preservation and hold of partial work; safe reauthorization/reassignment/cancellation; auditable reconciliation; and a race test where revocation overlaps a stock-affecting commit.

**Review trigger:** Reopen only on explicit product-owner change or when specifying notification provider/channel delivery under ODR-021 or reviewing the recommended workforce-assignment boundary in section 16.6 and detailed shared-site accountability.


### ODR-002 — approved emergency override for shared-site stock-handling tasks

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. This closes the emergency-override policy question within ODR-002. The grant-expiry notification/zero-grace baseline is recommended in section 16.5; the Home-Branch/workforce-assignment boundary is recommended in section 16.6; detailed shared-site custody/accountability and exception-handling workflows remain open; the complete cross-Branch permission matrix is approved in section 16.7.

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

**Review trigger:** Reopen only upon explicit product-owner change or evidence from operational testing that the approved emergency safeguards are unsafe or insufficient. The Home-Branch/workforce-assignment boundary is recommended in section 16.6; the complete permission matrix and detailed shared-site accountability remain separate ODR-002 work items.


### ODR-002 — approved permission-based delegated approval

**Status:** Approved subdecision — explicit product-owner approval in the conversation on 2026-11-19. This resolves the delegated-approval policy question for the ODR-002 shared-site stock-task grant workflow. The grant-expiry notification/zero-grace baseline is recommended in section 16.5; the Home-Branch/workforce-assignment boundary is recommended in section 16.6; detailed shared-site custody/accountability and exception-handling workflows remain open; the complete cross-Branch permission matrix is approved in section 16.7. General tenant-wide approval policy and the broader ODR-004/005 permission/delegation matrix remain subject to their own scope.

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

**Review trigger:** Reopen only upon explicit product-owner change or evidence that the approved constraints are unsafe or insufficient. Notification provider/channel delivery remains under ODR-021; the Home-Branch/workforce-assignment boundary is recommended in section 16.6; the complete permission matrix and detailed shared-site accountability remain separate ODR-002 work items.


### ODR-002 — recommended stock-task grant expiry notification baseline and zero-grace rule

**Status:** Recommended engineering baseline — selected on 2026-11-19 under the product owner's instruction to decide independently where a safe, non-conflicting decision is possible. This is not represented as an explicit product-owner approval. The immediate expiry/revocation enforcement and hold of in-progress tasks are already approved; the notification timing below is the recommended operational baseline. Provider/channel configuration and delivery mechanics remain governed by ODR-021.

**Purpose and fixed expiry rule**

A shared-site stock-task grant expires at its exact effective end timestamp. The effective end is exclusive. There is **no grace period** for starting new tasks or performing further stock-affecting actions after expiry or revocation. This follows the already approved ODR-002 rule that expiry/revocation immediately blocks further stock mutation. A notification delay, failure, unread message, offline device or worker unavailability must never extend authorization.

An in-progress task is held at expiry/revocation under the approved task-hold and reconciliation policy. It is not automatically completed or rolled back. Continuity must be arranged before expiry through a newly approved grant/version or reassignment to another currently authorized user.

**Notification events and default timing**

- **Grant activation/approval:** Send an immediate confirmation to the assigned worker and the user who created/activated the grant. State the approved scope, permitted actions and exact expiry.
- **24-hour reminder:** For grants whose approved duration is greater than 24 hours, send one reminder 24 hours before expiry.
- **1-hour reminder:** For grants whose approved duration is greater than one hour, send one reminder one hour before expiry. For grants longer than 24 hours, this is in addition to the 24-hour reminder.
- **Short grants:** For grants with a duration of one hour or less, the activation confirmation and expiry event are sufficient; do not send a misleading reminder at or after the expiry boundary.
- **Expiry:** Send an immediate expiry notice when the grant becomes inactive. If assigned tasks remain incomplete, identify that the task(s) are held and direct the user to the approved reauthorization/reassignment process.
- **Revocation or automatic invalidation:** Send an immediate notice when a grant is revoked or becomes invalid because a required user, membership, permission or scope is no longer valid.
- **Approved extension/version change:** Send a new confirmation with the newly approved scope and expiry. Until the new approved version becomes effective, the existing expiry remains in force.

A scheduled reminder must be suppressed if the grant has already been revoked, invalidated, superseded or expired. Do not send a late reminder after the expiry boundary.

**Recipients and information minimization**

- Activation and pre-expiry reminders go to the assigned worker and the grant creator/activating user.
- Expiry, revocation and automatic-invalidation notices go to the assigned worker, the grant creator and only those users who currently hold the relevant grant-review/reconciliation permission for the stock-owning Branch.
- Do not broadcast to all Branch users or notify the worker's home Branch by default. A stock-grant notification does not imply Home-Branch consent or workforce assignment; any required workforce notice belongs to the separate workforce workflow.
- Before sending, validate each recipient's active account/Tenant membership and permission to receive the notification category. If a recipient no longer has the required access, suppress Branch, stock and task details; send only a generic access-status notice if separately permitted. Include only the minimum information needed to identify the grant, its expiry/status and any held task. External channels must not expose unrelated customer, supplier, employee, financial or stock data.

**Channels and delivery failure**

- An in-app notification is the mandatory baseline.
- Push, email or SMS may be used only where the relevant channel is configured, permitted and consented to under the tenant/user notification policy. No provider is selected by this decision.
- Notification delivery is informational and is never an authorization token or a prerequisite for expiry/revocation enforcement.
- Delivery failure must not extend the grant, restore access, prevent task hold or reverse a stock transaction. Delivery attempts and final outcomes are auditable; provider routing, retry/backoff and channel preferences remain governed by ODR-021.
- A user who does not receive or read a notification remains subject to the exact effective end timestamp.

**Extension and operational continuity**

A grant's expiry may be changed only through a fresh, explicitly approved grant version before the current version expires. A request or pending approval for an extension does not extend the active grant. If no new version is approved and effective before expiry, the existing grant expires normally and in-progress work is held. No grace period, silent extension or automatic renewal is permitted.

**Audit and verification**

Retain scheduled event type/time, recipient identity and permission basis, channel selected, delivery attempts/outcome, grant/version and expiry, revocation/invalidation/supersession state, reminder suppression reason and any held-task references. Test the 24-hour/1-hour timing boundaries, short-grant behavior, expiry/revocation suppression, automatic invalidation, delivery failure, privacy-filtered recipients, no-grace enforcement and extension approval racing with expiry.

**Affected areas:** ODR-021 notification architecture/configuration; ERS-001; audit/observability; Core Domain Entity Catalogue; Canonical Business Glossary; stock-task lifecycle and acceptance tests.

**Review trigger:** Reopen only on explicit product-owner change or operational evidence that the reminder schedule creates missed work or excessive noise. Notification providers, consent, templates, retry/backoff and channel preferences remain part of ODR-021.


### ODR-002 — recommended separation of stock-task authorization and Home-Branch workforce assignment

**Status:** Recommended engineering baseline — selected on 2026-11-19 under the product owner's instruction to decide independently where a safe, non-conflicting decision is possible. This is not represented as an explicit product-owner approval. It separates the already approved stock-owning-Branch approval requirement from workforce scheduling/assignment authority.

**Recommended decision**

Home-Branch consent is **not a blanket prerequisite** for every cross-Branch shared-site stock-task grant. A stock-task grant authorizes only the named worker to perform the specifically approved inventory actions for the stock-owning Branch within the exact StockLocation/task scope and effective period. It does not assign the worker to a shift, change their work schedule, attendance, employment/engagement terms, compensation, cost allocation or other workforce conditions.

**Separate workforce assignment control**

- Where the cross-Branch activity requires a workforce assignment, roster/shift change, attendance allocation, duty reassignment or cost allocation, that action must be separately authorized through the applicable workforce workflow by an authorized user holding the specific workforce-assignment permission for the relevant scope.
- A stock-owning Branch approval or ApprovalDelegation cannot substitute for workforce-assignment authorization and cannot alter the worker's Home-Branch employment/engagement or scheduling records.
- Before the worker performs the stock task, any workforce assignment required by the tenant's active workforce policy must be valid for the relevant worker, site/Branch and time period. A stock-task grant alone is insufficient.
- If the task is already within a valid workforce assignment and does not change the worker's schedule, attendance or other workforce allocation, no additional Home-Branch consent is required solely because the stock belongs to another Branch.
- The Home Branch retains control of workforce decisions only through explicit workforce permissions and workflows. It does not receive stock ownership, general visibility into the stock-owning Branch or an automatic veto over each stock-task grant.
- The stock-owning Branch retains mandatory approval authority for its stock-task grant, including approval by a currently valid delegate under the approved ODR-002 delegated-approval policy.
- Workforce assignment notices, if required, are sent only through the separate workforce workflow to recipients with the relevant permission. They are not implied by stock-grant notifications.

**Rationale and safeguards**

This avoids imposing duplicate consent on every inventory task while preserving the distinction between (a) authority to handle another Branch's stock and (b) authority to schedule or allocate a worker's time. It prevents a stock grant from becoming an indirect workforce assignment and avoids granting either Branch unintended access to the other's data.

**Affected areas:** ODR-002; ERS-001; Actor and Access Model; Business Rules Catalogue; workforce scheduling/attendance; stock-task grant approval and audit.

**Verification:** Confirm that a stock grant cannot create or modify workforce assignments, shifts, attendance, pay or cost allocation; that a separate valid workforce assignment is enforced whenever workforce policy requires one; that no extra Home-Branch consent is demanded for stock-only authorization when the workforce assignment is already valid; and that stock-owning-Branch approval remains mandatory.

**Review trigger:** Reopen only on explicit product-owner change or operational evidence that the separation causes unsafe staffing, accountability or attendance outcomes. Any statutory or contractual workforce requirements are reviewed separately and do not change the stock authorization boundary by implication.


### 16.7 — Complete Cross-Branch Permission Matrix and configurable permission-first authorization

**Status:** Resolved — product-owner approval (2026-11-19). The product owner approved the proposed cross-Branch action boundaries and explicitly confirmed that access to data and ability to perform actions must not be determined by role titles or user-account types. Permissions must be configurable for each Tenant/customer and explicitly grantable, restrictable and revocable.

**Mandatory authorization principle**

Authorization is permission-first and scope-bound. The authenticated account establishes the identity of the actor; it does not itself confer business access. A role is an optional, tenant-configurable permission bundle, not an authority level. Role names, job titles, seniority, account category, employment category, client type and the fact that a person is a Tenant Owner MUST NOT independently grant any read, write, approval, export or administration capability. The effective permission set and applicable resource scope determine every decision.

Tenants must be able to configure permission bundles and assign/revoke permissions for their own authorized principals. The authorization model must support explicit permission assignment through configurable roles/groups and, where policy permits, direct principal grants or scoped temporary grants. It must also support removal, expiry, suspension and revocation. No assignment mechanism may let an assigning actor grant more authority than they currently hold and are explicitly permitted to delegate. Tenant configuration cannot weaken fixed security, tenant isolation, audit, segregation-of-duties, stock ownership or integrity invariants.

**Effective decision requirements**
- Evaluate authenticated principal, active Tenant membership, security boundary, explicit permission, resource and data scope, ownership, resource state, assignment/grant, limits, separation-of-duties and applicable policy.
- A permission is atomic and action/resource specific. `view` does not imply create/update/delete/approve/post/export; `count.capture` does not imply `variance.approve`; quarantine does not imply release; receiving does not imply transfer or adjustment.
- Scope may be Tenant, Branch, OperatingLocation, StockLocation, assigned task/resource, record, field/sensitive-data category, quantity/value limit and effective time window, as relevant.
- Default deny applies when a permission, scope, assignment, membership, approval, or policy condition is absent, expired, revoked, ambiguous or cannot be validated.
- Read access, mutation, approval, configuration/access administration, reporting and export are distinct permission families.
- A role/group bundle is only one configurable way to assemble explicit permissions. Direct grants, if enabled by tenant policy, remain explicit, scoped, time-bound where appropriate and audited. A role assignment never grants unlisted actions or implicit all-branch scope.
- Tenant Owner, account owner, administrator, employee, contractor, service account, mobile/POS account or any other account type receives no automatic business permission. Any required bootstrap/owner recovery authority must be a separately specified, narrowly scoped and auditable permission policy.
- Permission removal/revocation must take effect on the next authoritative server-side decision and invalidate/recheck affected sessions, caches, queued jobs, exports, mobile/offline actions and integrations. No stale client or cached grant may authorize a later write.
- Permission administration itself is protected by dedicated permissions, scope, delegation limits, audit and separation-of-duties controls. A principal cannot grant/revoke their own authority unless a separate approved policy expressly permits the exact operation; no self-escalation.

**Cross-Branch stock-task action matrix**

The matrix applies to a named worker handling stock owned by another Branch at a shared OperatingLocation. Every permitted action remains constrained by the approved grant, independently effective permission, task, stock scope, location, quantity and validity period. The grant and the worker's effective permissions must both authorize the exact action.

| Action / data category | Cross-Branch baseline | Boundary |
|---|---|---|
| View assigned task | Allow only with explicit permission | Minimum task data; no unrelated task queues or Branch dashboards |
| View stock needed for task | Allow only with explicit permission | Minimum SKU/lot/quantity/location; no general inventory or valuation browsing |
| Receive | Allow only when explicitly granted | Exact receipt/task, location, item and quantity limits |
| Put-away | Allow only when explicitly granted | Approved destination StockLocations and assigned task |
| Pick | Allow only for assigned task and explicit permission | No unrestricted picking or unrelated demand visibility |
| Issue stock to task | Allow only for assigned task and explicit permission | Preserve stock-owning Branch attribution and custody evidence |
| Internal transfer within same stock-owning Branch | Allow only with explicit permission | Explicit source/destination StockLocations |
| Physical stock count capture | Allow only with explicit count-capture permission | Observation only; no variance approval or adjustment |
| Quarantine / isolate | Allow only with explicit protective-action permission | Does not authorize release, disposal, write-off or valuation change |
| Release from quarantine | Deny by default | Separate explicit release permission and required independent approval |
| Stock reservation / allocation | Deny by default | Only separate task-bound reservation permission may allow a narrow reservation |
| Stock ownership transfer to another Branch | Deny under stock-task grant | Separate inter-Branch workflow, permissions and approvals |
| Stock adjustment / correction | Deny under stock-task grant | Separate authorized workflow |
| Write-off / wastage posting | Deny under stock-task grant | Separate permission and independent approval |
| Stock variance approval | Deny under count-capture permission | Separate variance-review/approval permission and maker-checker |
| Stock valuation, costing or price change | Deny | Separate inventory/finance authority |
| Purchase, supplier/farmer, sales, customer or financial records | Deny unrelated access | No access merely because stock handling needs limited item/task context |
| Financial posting, payment, settlement or period close | Deny | Separate financial permissions and workflows |
| Full Branch reports, analytics or bulk search | Deny | Separate report permissions and data scopes |
| Export, bulk download or data sharing | Deny | Separate export permission, dataset scope, purpose and audit |
| Permission, role, grant or workforce administration | Deny | Separate access-administration/workforce permissions |
| Safety/incident report and audit evidence | Allow minimum non-mutating reporting where explicitly permitted | No stock mutation or unrelated data access |

**Cross-channel enforcement**

The same matrix applies to web, mobile, POS, barcode/scanner, API, reports, search, files/attachments, exports, analytics, scheduled/background jobs, webhooks and third-party integrations. Every read and state-changing transition is checked server-side at the resource boundary. Client hiding is not security. Export/download access is checked at request time. Jobs and integrations use named least-privilege service identities. Offline/delayed operations are revalidated before commit. Stock authorization and stock commit remain concurrency-safe with respect to grant expiry/revocation and duplicate retries.

**Non-negotiable controls**

Tenant isolation, Branch ownership, stock ownership, server-side authorization, auditability and concurrency integrity cannot be disabled by customer configuration or subscription plan. Feature entitlement never grants permission or data scope. No role/account-type shortcut, implicit seniority rule or hidden UI behavior may bypass permission evaluation. Cross-Branch visibility and mutation remain independently configurable. Emergency Override and delegated approval retain their separately approved ODR-002 controls.

**Verification scenarios**
1. Two users with different job titles but identical effective permissions and scope receive identical outcomes.
2. Two users with the same role title but different explicit permissions receive different outcomes as configured.
3. Changing an account type or role label alone changes no authorization outcome.
4. Tenant can configure, assign and revoke a permission; the revoked action is denied while unrelated permissions remain unchanged.
5. No grant can exceed the assigning actor's delegation authority.
6. Read-only access cannot mutate through alternate endpoints, jobs, exports, mobile/barcode or integrations.
7. Count capture cannot approve variance or post adjustment; quarantine cannot release stock.
8. Same-Branch internal transfer cannot transfer ownership to another Branch.
9. Revoked/expired permissions and grants deny subsequent operations; stale clients cannot commit unauthorized work.
10. Every grant, change, revocation, denial and sensitive access is auditable.

The exhaustive atomic permission catalogue for every dairy module, permission-management UX and customer starter templates are elaborated in their respective specifications without weakening this approved baseline. Detailed shared-site custody/accountability and exception workflows remain separate ODR-002 work items.


**Permission precedence baseline (included in the 2026-11-19 approval):** Fixed platform/security invariants and applicable prohibitions are non-overridable; active membership, security boundary and resource ownership are prerequisites; an applicable explicit deny overrides an allow; applicable allows contribute only within their own valid scopes/limits/periods; no implicit inheritance or title/account-type elevation is permitted; conflicts or unverifiable authorization fail closed. This precedence applies to role bundles, group assignments, direct grants and temporary grants.


### ODR-002 / ODR-035 — Product-wide variability and architecture evolution baseline (ARC-PV-001)

**Status:** Product direction accepted by the product owner on 2026-11-19. The owner explicitly directed that stable rules remain fixed, variable business policies be configurable, and new capabilities be added through extensible architecture; the owner delegated routine and technically safe decisions to the assistant, reserving only indispensable matters for consultation. The concrete engineering baseline below is selected under that mandate; it is not represented as a separate explicit approval of every implementation detail.

**Decision statement**

The product is one comprehensive Dairy ERP SaaS platform for the smallest owner-operated business through large multi-branch/enterprise operations. It must scale down in setup complexity and usage cost and scale up in capabilities, workload, governance and infrastructure without separate customer code forks or divergent product semantics.

All material rules and variation points are classified as:
- Fixed invariant — non-overridable security, ownership, accounting, audit, transaction, stock and integrity rules.
- Configurable policy — supported customer-controlled settings/workflow choices with declared scope and validation.
- Customizable presentation — bounded branding, labels, layouts, templates, forms, reports and metadata.
- Extensible capability — new modules/integrations/workflow actions via versioned governed contracts.
- External/legal dependency — requires authoritative evidence or named human accountability.

**Selected engineering baseline**
1. Use a modular monolith with explicit bounded-context/module ownership initially; extract services only when measurable domain, workload, security, availability or organizational benefits exceed distributed-system complexity.
2. Keep one product/control plane/release lifecycle. Avoid customer-specific code forks.
3. Keep capability existence, plan entitlement, tenant activation, configuration, principal permission and extension grant as independent dimensions.
4. Use platform defaults, Tenant settings and only explicitly allowed Branch/site overrides; fixed invariants always win. User preferences affect presentation only. Transaction-specific exceptions require a governed workflow.
5. Configuration keys must have types, defaults, allowed values/scopes, validation, owner, permission, version, effective dates, audit, compatibility and rollback semantics.
6. Material configuration follows draft, validation, preview/simulation, approval where required, publication and versioned effective lifecycle. Completed/posted history retains the configuration version used.
7. Customer customization is configuration-first. Use supported forms/templates/reports/custom metadata; do not permit arbitrary SQL/DDL, unsafe scripts or core-runtime code injection.
8. Extensions use documented APIs, events, webhooks and adapters; each has its own Tenant binding, identity, permissions, scope, rate/resource limits, lifecycle and revocation.
9. Extensions never write directly to another module's private persistence and cannot block or corrupt unrelated core business transactions.
10. Use pooled resources as the standard efficiency candidate with a supported path to bridge/hybrid or silo isolation for selected Tenants where measured, contractual, verified regulatory, security or recovery needs justify it. Exact database/provider topology remains subject to PoC/ADR evidence.
11. Tenant-aware quotas, noisy-neighbour controls, stateless scale-out, independently scalable worker/report workloads and tested migration/restore paths are mandatory architectural considerations.
12. Feature flags govern rollout, entitlements govern commercial availability/usage, permissions govern access, and configuration governs behavior; these mechanisms never substitute for one another.

The complete rules and verification requirements are authoritative in `docs/04-architecture/PRODUCT_CONFIGURABILITY_CUSTOMIZATION_EXTENSIBILITY_AND_SCALE_POLICY.md`. Domain documents own their settings and business invariants. The module map and traceability matrix must be updated as those details are completed.

**Open boundaries:** final physical database/tenant-isolation topology, provider, numeric workload/SLO/RPO/RTO commitments, legal applicability and any extension sandbox runtime remain subject to their specific evidence/approval gates.

### ODR-004 — User and Tenant membership baseline (section 16.9)

**Status:** Recommended membership baseline selected under the product owner's 2026-11-19 delegated decision mandate. The model resolves the general cardinality question while keeping business access permission-based.

**Decision statement**
- One global human user identity may hold separate memberships in multiple independent Tenants where the person has a legitimate business relationship with each. This supports consultants, bookkeepers, shared-service personnel and business groups without combining Tenant data.
- A given human identity has at most one current membership record for a particular Tenant at a time; membership lifecycle changes are versioned/effective-dated rather than represented by duplicate active memberships.
- Each Tenant membership is independent, explicitly activated, suspendable and revocable. Membership in Tenant A never creates access to Tenant B.
- A Tenant membership may have zero or more Branch/workforce assignments. Where applicable, exactly one current Home/Primary Branch may be designated for workforce context; it is not a permission and grants no data visibility by itself.
- A principal may be authorized for more than one Branch only by explicit, scoped permissions/assignments. The system must not infer all-Branch visibility from membership, Home Branch, job title, role title, seniority or account type.
- Tenant context is selected/resolved for each request/session and must be validated server-side. A single operation is bound to one Tenant context unless an explicitly governed platform-level process is designed; no cross-Tenant business query or transaction is implicit.
- Platform principals and service/integration identities remain distinct from human Tenant memberships. A service identity receives only named, Tenant-bound and resource-scoped permissions.
- Tenant onboarding/bootstrap must issue a narrow, explicit and auditable initial permission assignment to the first authorized principal through a controlled provisioning workflow. No hidden "owner account" bypass or automatic global authority is permitted.
- Suspending/revoking membership invalidates permission assignments, delegated approvals and sessions for that membership according to the immediate security revocation policy; historical records/audit remain intact.

**Consequences**
- User account, Person, TenantMembership, workforce/personnel record, Home Branch, Branch assignment, Permission Assignment and Role bundle remain distinct concepts.
- Branch assignments describe organizational/workforce context; only explicit effective permissions authorize data disclosure or actions.
- The model supports one person performing duties for multiple dairy Tenants without merging data, but each Tenant controls its own memberships and permissions.

**Verification**
1. Same user can access two separate Tenants only through two independent active memberships and explicit permissions; changing Tenant context cannot cross-read or cross-write.
2. Revoking one Tenant membership does not revoke a separate valid membership in another Tenant, but it immediately blocks the revoked Tenant context.
3. Home Branch assignment alone grants no Branch read or mutation permission.
4. Multi-Branch assignment without corresponding permissions grants no additional data access.
5. A permission grant cannot exceed the assigning principal's own delegated authority.
6. A platform or service identity has no default human Tenant membership or unrestricted data access.

