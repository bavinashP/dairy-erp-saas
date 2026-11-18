# Open Decisions Register

**Status:** Initial register  
**Rule:** Do not leave a decision generically open. Every item must be classified as (a) resolved by an explicit project constraint, (b) recommended engineering baseline, (c) owner approval required, (d) external evidence/legal applicability required, or (e) intentionally deferred with a trigger and review date. Never label a recommendation as user-approved. For owner/evidence dependencies, document the safe interim baseline and the exact decision, owner, evidence, impact and latest decision date.

| ID | Area | Open decision / question | Why it matters | Required evidence / investigation | Status |
|---|---|---|---|---|---|
| ODR-001 | Business identity and operating model | A tenant represents one Dairy Business Workspace. May that workspace support multiple dairy operating models and multiple branches? | Tenant onboarding, capability configuration, module availability and future extensibility | Product-owner decision recorded below; legal form and regulatory applicability are explicitly outside the current development scope and will be reviewed after project completion | **Resolved — user-approved project decision (2026-11-18): Option B.** One tenant supports multiple operating models and multiple branches. |
| ODR-002 | Branch and operating-location model | Define Branch, OperatingLocation, its operational capabilities, StockLocation, ServiceArea and their relationships. | Avoids conflating management units, physical sites, operational functions, inventory sublocations and geographic coverage | Approved: one physical site may support multiple capabilities; StockLocation is separate; Branch is a management/operating unit; one Branch may be associated with multiple OperatingLocations; OperatingLocation may be unassigned or used by multiple Branches through explicit governed associations; one Branch may serve multiple ServiceAreas and one ServiceArea may be served by multiple Branches through explicit effective-dated mapping; each Route belongs to exactly one Branch and DeliveryRun must use a Route belonging to its Branch; shared physical premises may be used by multiple Branches while each Branch retains separately attributable StockLocations, stock ownership, stock control and transaction records. Physical sharing grants no cross-branch stock/data access. A shared-site worker may perform specifically authorized stock-handling actions for more than one Branch only through an explicit, limited, effective-dated and auditable grant; this does not grant general branch membership or visibility into the other Branch's full operational data. The authorized user holding the required approval permission of the stock-owning Branch, or a currently valid delegate under the approved ODR-002 delegated-approval policy, must approve each cross-branch stock-task grant before it becomes effective; a no-blanket-consent baseline with separate workforce-assignment authorization is recommended in section 16.6. Grant-expiry notification timing and zero-grace baseline are recommended in section 16.5; notification channel/provider delivery remains under ODR-021. The separate workforce-assignment boundary is recommended in section 16.6; the shared-site custody/accountability domain baseline is selected in section 16.36; detailed workflow edge cases remain subject to domain validation, while legal ownership/liability/insurance and contractual allocation remain external-review matters; the complete permission matrix is resolved in section 16.7. Delegated approval is resolved by the separately approved policy below | **Partially resolved — core physical-site, Branch/OperatingLocation, ServiceArea/Branch, Route/Branch, shared-site/stock-segregation, limited cross-Branch stock-task grant, delegated approval, grant lifecycle, expiry, workforce-assignment, permission-matrix, custody/accountability and route/run/dispatch execution baselines are resolved or selected in sections 16.1–16.7 and 16.36–16.37. Remaining dependencies are scenario validation and external legal/contract/insurance/vehicle-qualification applicability review; these do not block the operational domain baseline.** |
| ODR-003 | Tenant legal/accounting identity | Define Tenant-to-LegalEntity, TaxRegistration and AccountingBook cardinalities and applicability. | Legal identity, tax, accounting, Tally mapping and reporting | Preserve conceptual separation now; conduct current official legal/tax and qualified accounting review after project completion as explicitly directed by the product owner | **Deferred — post-project legal/accounting review; not a current development blocker. Do not infer legal cardinality.** |
| ODR-004 | User membership | Define multi-Tenant membership and within-Tenant Branch assignments. | Identity, authorization, session context and workforce operations | Apply the permission-first membership model in section 16.9; retain independent Tenant isolation and explicit Branch permissions | **Resolved — recommended membership baseline selected under the product owner's delegated decision mandate (2026-11-19); see section 16.9.** |
| ODR-005 | Authorization | Define the exhaustive atomic permission catalogue across all dairy modules and remaining domain approval/scope policies. The permission-first model, tenant configurability, fail-closed precedence and cross-Branch stock-task matrix are approved under ODR-002 section 16.7 and ARC-PV-001. | Cross-module access and segregation of duties | Complete module-by-module permission inventory, sensitive-field permissions, domain approval policies and automated coverage | **Partially resolved — cross-cutting model and cross-Branch baseline resolved; module-level permission catalogue and domain-specific approval rules remain in progress.** |
| ODR-006 | Customer subscription lifecycle | Define effective-dated agreement changes, pause/resume, skip, holiday, substitution, cancellation and reinstatement. | Delivery, billing, stock, balances | Subscription state/exception baseline in section 16.10; validate edge cases through worked scenarios | **Recommended baseline selected under delegated decision mandate; domain-specific commercial and cut-off settings remain configurable.** |
| ODR-007 | Recurrence and schedule policy | Define recurrence anchors, intervals, selected weekdays, shifts, calendars, cut-offs, precedence and schedule versioning. | Delivery planning and correctness | Recurrence baseline in section 16.11; validate generation/idempotency and exception scenarios | **Recommended baseline selected under delegated decision mandate; Tenant/Branch service calendars and permitted cut-offs remain configurable.** |
| ODR-008 | Units, precision and rounding | Define canonical unit dimensions, product base UOM, device resolution, workflow precision, conversion and financial rounding boundaries. | Procurement, inventory, pricing, billing and Tally interoperability | Precision architecture selected in QUANTITY_UOM_PRECISION_AND_MONEY_ROUNDING_ANALYSIS.md; finish device/workflow evidence and transaction-specific commercial/accounting rounding test vectors | **Partially resolved — precision architecture selected; measurement catalogue and transaction-specific commercial/statutory policies remain evidence-gated.** |
| ODR-009 | Milk quality and testing | Define quality profiles, sample/test records, methods, result states, acceptance/hold/rejection and traceability. | Procurement settlement, processing and food safety | Configurable quality-profile baseline in section 16.12; validate device methods and post-project legal/food-safety applicability | **Recommended baseline selected under delegated decision mandate; legal thresholds and specific instrument methods remain evidence-gated.** |
| ODR-010 | Procurement rates and farmer settlement | Define effective-dated rate charts, quality-linked calculations, deductions, advances, settlement periods and disputes. | Supplier balances, procurement incentives and accounting | Versioned rate-chart and settlement baseline in section 16.13; validate example calculations with representative dairy workflows | **Recommended baseline selected under delegated decision mandate; tenant-specific commercial formulas and values remain configurable.** |
| ODR-011 | Processing and packaging | Define optional process profiles, versioned recipes, input/output/by-product/loss, batch lineage, quality release and yield tolerance. | Manufacturing, stock, cost and recall | Optional, versioned process baseline in section 16.14; validate representative process families and mass-balance evidence | **Recommended baseline selected under delegated decision mandate; process templates and tolerances remain product/process configurable.** |
| ODR-012 | Inventory and stock control | Define stock ledger, reservations, transfers, lots/expiry profiles, counts, adjustments, negative stock and valuation boundaries. | Stock accuracy, traceability, cost and audit | Inventory baseline in section 16.15; complete valuation and legal/accounting dependencies through ODR-017/003 | **Recommended baseline selected under delegated decision mandate; valuation/costing method and limited exception settings remain evidence/policy gated.** |
| ODR-013 | Pricing and commercial terms | Define versioned price lists, precedence, effective dates, customer/segment terms, discounts, deposits and approval limits. | POS, subscriptions, profitability and billing | Pricing baseline in section 16.16; tenant-owned values and validated override/approval ranges remain configurable | **Recommended baseline selected under delegated decision mandate; actual rates, discounts and credit terms are Tenant business decisions/configuration.** |
| ODR-014 | Billing and receivables | Define configurable billing periods/cut-offs, invoice/statement distinction, advances, credit, proration, disputes and linked corrections. | Receivables, customer balances and financial reporting | Billing baseline in section 16.17; legal document requirements remain subject to ODR-003/016 post-project review | **Recommended baseline selected under delegated decision mandate; billing-cycle and credit settings remain Tenant-configurable within verified rules.** |
| ODR-015 | Payment evidence and verification | Distinguish payment intent, manual receipt entry, provider confirmation, bank statement evidence, reconciliation and settlement. | Avoids false payment verification and duplicate settlement | Evidence-state baseline in section 16.18; specific provider/bank integration capability remains PoC-dependent | **Recommended baseline selected under delegated decision mandate; provider selection remains evidence/cost dependent.** |
| ODR-016 | GST and statutory invoicing | Determine current tax classification, document, e-invoice/e-way bill, filing and retention applicability. | Legal and financial correctness | Keep an effective-dated, source-backed tax rules engine and document capability; qualified current official/legal review is reserved for post-project review under the product owner's scope instruction | **Deferred — post-project authoritative legal/tax review; do not hardcode assumptions or claim compliance now.** |
| ODR-017 | Accounting and financial policy | Define Tenant accounting books, CoA templates, posting recognition, valuation, financial periods, reconciliation and Tally exchange. | Finance integrity, audit, statutory accounts and integrations | Double-entry and immutable-ledger baseline in section 16.19; exact accounting policy and Tally release compatibility require domain/accounting review and PoC | **Recommended engineering baseline selected; exact books/legal mapping and accountant-approved recognition/valuation policies remain evidence-gated.** |
| ODR-018 | Workforce and payroll | Define optional people, engagement, shifts, attendance, leave, compensation, contractor and payroll capabilities. | Operational workforce fit and legal obligations | Workforce capability baseline in section 16.20; statutory labour/payroll applicability is reserved for post-project qualified legal review | **Recommended non-statutory product baseline selected; legal/statutory payroll rules deferred to post-project review.** |
| ODR-019 | Mobile apps | ARC-MOB-001 selects one unified, permission-, task- and context-aware mobile app as the initial reversible baseline; separate task-focused apps require evidence of material security, usability, distribution or release-management benefit. | Avoids fragmented releases and duplicated client behavior while preserving permission-, task- and context-specific workspaces | Validate representative owner, branch-operations, delivery, POS, procurement, inventory and finance journeys before UX freeze; compare total lifecycle cost if separate apps are proposed | Recommended baseline; validate role journeys before UX freeze |
| ODR-020 | Offline operation | ARC-MOB-001 defines a narrow initial offline baseline for assigned delivery-run snapshots and delivery outcome capture with server revalidation, idempotent sync and explicit conflict handling. Offline POS, financial posting, payment settlement, stock posting and master-data changes are not initially guaranteed. | Preserves delivery continuity without allowing stale offline state to corrupt shared financial, stock or schedule records | Field trial; device/data-protection threat review; determine maximum offline duration, cache TTL, supported device matrix and sync support policy; test duplicate, stale, revoked and conflicting operations | Recommended delivery-offline baseline; duration/device policy requires evidence and owner approval; other workflows deferred with triggers |
| ODR-021 | Notifications | Define provider-neutral notification events, templates, preferences, consent, retries, delivery evidence and fallback. | Reliability, cost, privacy | Channel-neutral baseline in section 16.21; provider, pricing and external-channel consent integrations require current capability/cost review | **Recommended baseline selected; provider and external channel configuration remain evidence/cost dependent.** |
| ODR-022 | Analytics | ARC-BI-001 defines analytics layers, core report families, fact grains, dimensions, KPI governance, security, refresh and acceptance tests. Remaining owner decision: approve official KPI definitions/owners and SaaS plan report entitlements. | Business target ownership and commercial packaging | Management KPI workshop using documented catalogue; formula test vectors and plan-entitlement approval | Recommended architecture baseline; KPI/plan approval required before launch |
| ODR-023 | Forecasting | ARC-BI-001 establishes transparent baseline/statistical forecasting, forecast-run governance, accuracy checks and candidate forecast domains. Initial default is short-horizon daily/weekly forecasting where sufficient history exists; advanced ML is optional. Remaining owner decision: prioritize forecast outputs/horizons by customer value. | Avoids unvalidated ML and unsupported long-range claims | Pilot use cases, minimum-history criteria, backtesting against simple baselines and owner prioritization | Recommended engineering baseline; product prioritization required before release scope is frozen |
| ODR-024 | Budgeting | ARC-BI-001 establishes versioned budgets, draft-to-approved lifecycle, preserved revisions, scenario separation and actual-versus-budget comparison. Remaining owner decision: tenant-selectable approval thresholds and role ownership; default design supports configurable maker-checker controls. | Prevents overwriting approved plans and separates hypothetical scenarios from ledger postings | Worked budget lifecycle, permission matrix and owner approval of default templates/thresholds | Recommended architecture baseline; tenant policy templates require product approval |
| ODR-025 | SaaS plans and entitlements | Define capability packages, quotas, trials, upgrade/downgrade, grace, suspension, service tiers and billing. | SaaS commercial viability and fair resource usage | Entitlement/configuration/permission separation and safe quota behavior in section 16.25; commercial prices, packages and contractual commitments require owner approval | **Architecture baseline selected; commercial packaging/pricing and contract terms require owner approval before launch.** |
| ODR-026 | Data lifecycle | Define data classification, purpose, retention, archive, export, deletion, anonymization, legal hold and offboarding. | Privacy, compliance, storage and customer trust | Lifecycle baseline in section 16.26; exact statutory/contract retention periods require post-project legal review and data-owner approval | **Recommended architecture baseline selected; retention periods and legal-hold obligations remain external-evidence dependent.** |
| ODR-027 | Scale targets | ARC-SCALE-001 defines the workload dimensions, three capacity envelopes, scaling path and verification strategy. Remaining input: product owner/business team supplies target adoption and tenant-profile assumptions; engineering converts these into measurable transaction/concurrency/storage estimates using pilot telemetry before production sizing. | Avoids invented capacity figures and undersized/overbuilt production infrastructure | Conservative/expected/stress capacity worksheet, pilot measurements, load/soak/stress test evidence and cost model | Recommended engineering baseline; owner workload input required before production sizing |
| ODR-028 | Availability/recovery | Recovery architecture and an initial RPO/RTO/backup baseline are specified in ARC-DR-001. Remaining approval: accept or amend the proposed service objectives and name operational responders before production. | Business commitment, staffing and recovery cost | Monthly restore evidence, quarterly DR exercise, business impact review and named incident/restore/key-custody owners | Recommended baseline; owner approval required before production |
| ODR-029 | Integrations | Prioritize accounting, banking, payments, messaging, measurement devices, Tally and other partner integrations. | API, event, adapter boundaries and partner dependency | Tally import/export readiness is a product requirement from the product owner; Tally is the first accounting integration priority. Other providers remain capability/contract/cost evidence dependent | **Priority baseline selected under prior explicit Tally requirement; other provider sequencing remains evidence and business-priority dependent.** |
| ODR-030 | Technology | ADR-TECH-001 narrows backend, database and mobile candidates and defines weighted criteria plus repeatable PoC gates. Preferred PoC directions: ASP.NET Core/.NET 10 LTS, PostgreSQL 18 and Flutter; Laravel 13, MySQL 8.4 and React Native remain evaluated alternatives. No final stack is approved until team capability, compatibility, integrity, operational and cost evidence is reviewed. | Long-term maintainability, transactional integrity, security patching, deployment and lifecycle cost | Execute identical backend/database vertical slice; mobile offline field PoC; web framework slice; Linux VPS deployment/restore; team capability review; reproducible results and explicit owner approval | Recommended PoC shortlist; final technology approval required after evidence and owner review |
| ODR-031 | Deployment topology | ARC-DEP-001 defines Linux VPS-compatible logical zones, Docker-optional deployment, compact pilot/separated-data/horizontally-scalable profiles, security controls, release process, monitoring, backup and production gates. Preferred direction is to evaluate a separated database tier for production; a single-VPS profile is conditional pilot-only with explicit single-host risk acceptance. | Security, resilience, scale, operational recovery and total cost | Compare current VPS/provider/region terms, data location, network/security features, support, cost, database/backup capability; use workload/capacity model, PoC and restore evidence; obtain explicit provider/topology approval | Recommended deployment baseline; provider, region, sizing and production risk acceptance require evidence and owner approval |
| ODR-032 | Localization | Define supported language coverage, translation ownership, locale formatting and localized templates. | Consistent UX, accessibility and adoption | English remains primary; the localization architecture must support Marathi, Hindi and additional Indian languages; initial language rollout and translation ownership are detailed in section 16.32 | **Recommended localization baseline selected; rollout priority and translation governance require usage/quality evidence.** |
| ODR-033 | Security governance | Establish MFA, session/recovery, privileged access, incident response, vulnerability remediation and risk-acceptance governance. | Account takeover, privileged misuse, incident impact and production risk | Mandatory technical controls and permission-based privileged access in section 16.33; named operational responders and time-bound risk acceptance require accountable human approval before production | **Recommended security baseline selected; named ownership and risk-acceptance authority remain production governance dependencies.** |
| ODR-034 | Audit and observability governance | Establish audit events, evidence integrity, telemetry retention classes, access, alert ownership, escalation and incident evidence. | Accountability, privacy, investigation readiness, storage cost and operational response | Mandatory durable, permission-controlled audit and observability baseline in section 16.34; retention values and named operational owners require legal/evidence review and human assignment | **Recommended governance baseline selected; retention and accountable operational ownership remain production dependencies.** |
| ODR-035 | Product variability and architecture evolution | Establish one product-wide policy for fixed invariants, customer configuration/customization, extensibility, module boundaries and scale from small to enterprise. | Prevents customer-specific forks, uncontrolled customization, premature microservices and future migration barriers | Product-owner direction and ARC-PV-001; validate through module, NFR, compatibility and scale reviews | **Resolved product direction — owner instructed independent decisions and approved the stability/configurability/extensibility principle on 2026-11-19; engineering baseline selected in ARC-PV-001.** |
| ODR-036 | Party identity resolution | Define duplicate detection, identity linking/merge, approval, reversibility and treatment of posted transactions/open balances. | Prevents mistaken identity consolidation, cross-Branch exposure and financial/history corruption | Same-Tenant scenario tests, verified identifier conflict handling, account/ledger impact review and audit/recovery tests | **Recommended safe baseline selected under the product owner's delegated decision mandate (2026-11-19); see PARTY_CUSTOMER_SUPPLIER_AND_FARMER_DOMAIN_ANALYSIS.md section 7. Exact statutory identity requirements remain evidence-dependent.** |

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
- At the time of this initial subdecision, Branch-to-OperatingLocation cardinality, unassigned locations, shared-site governance, ServiceArea mapping, route relationships and employee access were not yet decided. Subsequent ODR-002 subdecisions in this register establish the Branch/OperatingLocation, ServiceArea/Branch, Route/Branch, shared-site/stock-segregation, permission and custody/accountability baselines; further scenario validation and external legal/contract responsibility review remain separate.

**Affected areas:** Canonical Business Glossary; Core Domain Entity Catalogue; ERS-001; DBR-002; inventory/location modelling; onboarding and capability configuration.

**Verification:** Confirm that one OperatingLocation can enable multiple operational capabilities, that StockLocation is distinct from OperatingLocation, and that a single-premises dairy workflow does not require fictitious physical sites.

**Review trigger:** Reopen this accepted baseline only if the product owner changes the single-premises/multi-capability requirement. Continue ODR-002 separately for the remaining branch/location relationships and access rules.

---

### ODR-002 — Approved Branch and OperatingLocation relationship

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for additional scenario validation and external legal/contract/insurance ownership-liability review; the operational custody/accountability baseline is selected in section 16.36 and the complete cross-Branch permission matrix is approved in section 16.7.

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

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for additional scenario validation and external legal/contract/insurance ownership-liability review; the complete cross-Branch permission matrix is approved in section 16.7 and custody/accountability baseline is selected in section 16.36.

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

**Review trigger:** Reopen only upon explicit product-owner change or a materially different service-allocation business requirement. Continue ODR-002 for additional scenario validation and external legal/contract/vehicle-qualification review; Route execution/dispatch is selected in section 16.37 and shared-use/access baselines are specified in sections 16.7 and 16.36.

---

### ODR-002 — Approved shared OperatingLocation and branch-specific stock segregation

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for additional scenario validation and external legal/contract/insurance ownership-liability review; its permission-first access baseline is approved in section 16.7 and its custody/accountability baseline is selected in section 16.36.

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
- At the time of this approval, detailed operational responsibility, stock custody hand-off and shared-equipment accountability were not yet decided. The operational custody/accountability baseline is now selected under ODR-002 section 16.36; external legal/contract/insurance liability and scenario validation remain separate. The complete cross-Branch permission matrix is approved under ODR-002 section 16.7.

**Affected areas:** Canonical Business Glossary; Core Domain Entity Catalogue; ERS-001; DBR-002; inventory and stock ledger; branch authorization; shared warehouse operations; stock counts, transfers and audit history.

**Verification:** Confirm that two Branches can use one OperatingLocation, maintain separate branch-attributed StockLocations and stock ledgers within the same premises, and cannot access or mutate one another's stock without an explicit grant.

**Review trigger:** Reopen only upon explicit product-owner change or evidence that the shared-site/stock-segregation baseline cannot safely support a required operating workflow. Continue ODR-002 for detailed scenario validation and external legal/contract/insurance responsibility review; operational custody/access baselines are now specified in sections 16.7 and 16.36.

---

### ODR-002 — Approved limited cross-branch stock-handling access for shared-site workers

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for additional scenario validation and external legal/contract/insurance ownership-liability review; the cross-Branch permission matrix is approved in section 16.7 and custody/accountability baseline is selected in section 16.36.

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

**Review trigger:** Reopen only upon explicit product-owner change or a material change to the shared-site operating model. Continue ODR-002 for scenario validation and external ownership/liability/insurance/vehicle-qualification review; permission matrix, custody/accountability and route/run/dispatch baselines are specified in sections 16.7 and 16.36–16.37.

---

### ODR-002 — Approved Route and Branch relationship

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for further scenario validation and external legal/contract/insurance ownership-liability review; the complete cross-Branch permission matrix is approved in section 16.7 and custody/accountability baseline is selected in section 16.36.

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
- At the time of this approval, detailed dispatch assignment, driver/vehicle allocation, route optimization and multi-shift route reuse were not yet decided. Their operational baseline is now selected in ODR-002 section 16.37. Shared-site accountability and the permission matrix are separately specified in sections 16.36 and 16.7. A limited shared-site stock-task grant does not create general cross-branch membership or delivery-run access.

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

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for additional scenario validation and external legal/contract/insurance ownership-liability review; grant lifecycle and permission matrix are specified in sections 16.2–16.7, and custody/accountability baseline in section 16.36.

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

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. ODR-002 remains partially open for detailed shared-site custody/accountability and exception workflows; the complete permission matrix is approved in section 16.7. The Home-Branch/workforce-assignment boundary is recommended in section 16.6. A recommended expiry-notification/zero-grace baseline is specified in section 16.5; channel/provider delivery remains under ODR-021.

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

**Status:** Approved subdecision — explicit product-owner approval on 2026-11-18. This closes the emergency-override policy question within ODR-002. The grant-expiry notification/zero-grace baseline is recommended in section 16.5; the Home-Branch/workforce-assignment boundary is recommended in section 16.6; the custody/accountability baseline is selected in section 16.36; further workflow validation and external legal/contract/insurance review remain separate; the complete cross-Branch permission matrix is approved in section 16.7.

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

**Status:** Approved subdecision — explicit product-owner approval in the conversation on 2026-11-19. This resolves the delegated-approval policy question for the ODR-002 shared-site stock-task grant workflow. The grant-expiry notification/zero-grace baseline is recommended in section 16.5; the Home-Branch/workforce-assignment boundary is recommended in section 16.6; the custody/accountability baseline is selected in section 16.36; further workflow validation and external legal/contract/insurance review remain separate; the complete cross-Branch permission matrix is approved in section 16.7. General tenant-wide approval policy ODR-004 membership cardinality is resolved in section 16.9; ODR-005 remains only for the exhaustive module-level permission catalogue and remaining domain approval rules.

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

The exhaustive atomic permission catalogue for every dairy module, permission-management UX and customer starter templates are elaborated in their respective specifications without weakening this approved baseline. The custody/accountability baseline is selected in ODR-002 section 16.36; remaining ODR-002 work covers further scenario validation, route/dispatch workflow details and external legal/contract/insurance responsibility review.


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



### 16.10 — ODR-006: Customer subscription lifecycle baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19).

A customer subscription is an effective-dated commercial/service agreement, not proof of delivery, invoice or payment. Preserve a versioned subscription agreement and line-level product, quantity, UOM, price basis, branch/service area, service address, recurrence and effective dates.

- Separate the agreement lifecycle from each dated schedule occurrence and from fulfilment/billing/payment.
- Use controlled states such as Draft, Pending Approval where policy requires, Active, Paused, Cancelled and Expired. Do not use one state to represent both subscription and individual occurrence status.
- Pause/resume is a future-effective interval change; it must not delete generated history. Skip applies only to a named occurrence/date/shift. Holiday/closed-day behavior is driven by the applicable calendar policy. Substitution and quantity/product changes create effective-dated line/schedule versions.
- Cancellation ends future entitlement from its effective date; reinstatement creates a new effective version. No hard deletion of an agreement with operational/financial history.
- Late changes after the configured cut-off require a separate exception permission/approval where the Tenant policy requires it. A late edit never silently changes an already dispatched/delivered occurrence.
- Billable quantity derives from authoritative fulfilment and approved adjustment rules, not merely from planned quantity.
- Preserve reason, actor, old/new version and affected future occurrences for every material change.

### 16.11 — ODR-007: Recurrence, calendar and schedule precedence baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19).

Use a recurrence model capable of representing daily, every-N-day with stable anchor, selected weekdays, weekly patterns, multiple service shifts and future-dated effective intervals. Morning/evening may be presets, not hardcoded exhaustive shift values.

- Define recurrence by business calendar/date, anchor, interval/pattern, selected days, shift/service window, start/end, holiday treatment and effective version.
- Use a Tenant's configured business time zone (IST default for the India-focused product); store actual event timestamps separately from the business service date. Branch-specific time zones are not assumed for the initial India product unless later required.
- Calendar/cut-off precedence: fixed platform constraints → Tenant defaults → Branch/route calendar override only where supported → subscription-specific effective schedule → explicitly approved occurrence exception. More specific configuration may override only if the key declares that scope.
- Generate dated occurrences idempotently by subscription line, business date, service shift/window and schedule version. Reprocessing must not duplicate or silently alter already executed occurrences.
- Cut-off time, closed days, lead time and same-day exception permissions are Tenant-configurable within validated bounds. If no applicable rule can be resolved, do not invent an occurrence silently; surface a configuration error for authorized correction.
- Route planning consumes the correct Branch/service-area occurrence set; a Route's Branch ownership rule under ODR-002 remains fixed.

### 16.12 — ODR-009: Milk quality and testing baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19). This does not prescribe legal quality limits or statutory testing frequency.

Use configurable, versioned quality profiles by milk/material type, operating activity and test context. Each profile may define parameter, unit, method, sample requirements, device/manual source, accepted range/decision rule, requiredness, effective dates and approving permission.

- Distinguish Not Tested, Pending, Valid, Failed, Invalid/Rejected Sample and Not Applicable; zero is a measured value, not a missing result.
- Preserve sample identity, collection/source lot, time, collector, method/device, operator, calibration/evidence where available and result provenance.
- Quality hold/quarantine prevents normal allocation or processing release until the required disposition is authorized.
- Partial acceptance records accepted and rejected quantities separately, preserving source measurements.
- Limits/tolerances are versioned and may vary by product/material and verified activity. A Tenant may configure operational thresholds only within its lawful/product-validated bounds; it cannot mark a failed mandatory control as passed by configuration.
- Analyzer/device integration is optional through adapters; manual entry remains supported with source and actor evidence.
- Food-safety and statutory threshold applicability is deferred to current official review after project completion.

### 16.13 — ODR-010: Procurement rate and farmer settlement baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19).

Support both simple flat-rate and configurable quality-linked rate charts. Rate charts are versioned, effective-dated, scoped by Tenant/Branch/collection point, supplier group/product/milk type as applicable, and identify their formula and input basis.

- Store measured quantity, accepted quantity, quality readings and rate-chart version independently.
- Calculation must be explainable/reproducible: disclose input values, formula/version, incentives/penalties, rounding policy and result.
- Advances, deductions, deposits, transport/container charges and other adjustments must be separately typed, authorized and auditable; do not bury them in a net rate.
- Settlement periods and payment methods are Tenant-configurable; partial settlement, carry-forward, dispute and correction are explicit states.
- After settlement/posting, corrections use linked adjustments/reversals and must not rewrite original collection, rate or payment facts.
- No universal Fat/SNF formula, rate table, statutory payment interval or penalty is imposed by the product.

### 16.14 — ODR-011: Processing and packaging baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19).

Processing is an optional, composable capability. Retail-only and simple resale Tenants must not be forced through manufacturing setup. Where enabled, use versioned/effective-dated process/recipe definitions with input materials, outputs, by-products, packaging consumption, process steps, equipment, quality checkpoints, expected yield ranges and measured loss/tolerance.

- Production batch captures actual input/output quantities, lots, time, equipment and responsible actors.
- Material balance and traceability connect consumed inputs to finished lots and by-products.
- Yield tolerances and quality-release requirements are configured per validated process/product profile, not globally invented.
- Failed or held quality state prevents release to ordinary available stock.
- Recipe changes affect future batches only; completed batches retain the version and snapshots used.
- Loss/wastage is recorded as a distinct event and cannot be silently netted away.
- Contract processing may be supported as an explicit workflow when demand is validated; it is not an assumption about all Tenants.

### 16.15 — ODR-012: Inventory and stock-control baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19).

Use an append-oriented stock ledger as the authoritative source for stock quantity; balances are derived from movements, reservations and status. Preserve ownership, Branch, StockLocation, product/base UOM, lot/batch where enabled, stock status and source transaction.

- Basic product/location quantity tracking is sufficient for simple retail; lot, expiry, serial, batch, warehouse and manufacturing traceability are activated according to product risk, shelf life, regulatory applicability and operating model.
- Transfers are two-sided custody events: dispatch creates in-transit quantity; destination receipt confirms received quantity and records shortage/damage/discrepancy. Ownership rules are explicit; inter-Branch transfers never occur by editing a location field.
- Reservations are separate from physical issues and have explicit allocation, expiry/release and priority rules.
- Physical counts record observations separately from variance approval and adjustment. Count capture, variance review/approval and stock adjustment are distinct permissions.
- Negative available stock is denied by default. A Tenant may enable a narrowly scoped, auditable negative-stock exception only for eligible non-serialized/non-lot-controlled products under an explicit policy/permission and reconciliation process. It is never allowed to bypass traceability, expiry, quality hold, stock ownership or financial close controls.
- Adjustments, write-offs and valuation changes require their own permissions and applicable independent approval. Posted movements are immutable; corrections use linked entries.
- Costing/valuation method and accounting recognition remain under ODR-017 and accounting review.

### 16.16 — ODR-013: Pricing and commercial-term baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19).

Use versioned/effective-dated price lists and rate schedules, with explicit scope and deterministic selection precedence. Support standard price, customer/segment terms, branch-specific price where configured, subscription price, retail/POS price, wholesale/contract price, discounts, deposits and credit terms as distinct concepts.

- Price selection must identify selected price-list version, date/time basis, customer/product/channel/Branch scope and reason.
- A transaction snapshots the unit price, discount, tax classification, charge and calculation policy used.
- Discounts/overrides are separate permissions with configurable approval thresholds and audit; a user cannot approve their own action where maker-checker is required.
- New price changes apply prospectively unless a controlled correction/repricing workflow is explicitly approved and preserves prior evidence.
- Precedence rules are explicit; ambiguous overlapping price rules fail closed or require authorized resolution rather than arbitrary first-match.
- Actual prices, commercial margins, credit limits and customer-specific terms remain Tenant business data/configuration.

### 16.17 — ODR-014: Billing and receivables baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19).

Keep source sale/fulfilment, bill/invoice, customer statement, receipt/payment allocation and journal as separate related records. A statement summarizes items; it does not create duplicate revenue/invoice. Billing cycles, periods and cut-offs are Tenant-configurable and versioned.

- Recurring subscription bills are based on authoritative billable fulfilment, adjustments and effective price/tax snapshots.
- POS/counter sale and one-time orders use their own source events; do not force a subscription.
- Support draft/preview, validation, issue/post, partial settlement, credit/debit adjustment, dispute, cancellation/reversal and collection statuses as distinct states.
- Advances remain unapplied until explicitly allocated. Allocation cannot exceed available amount; residuals are traceable.
- Credit limits/overdue holds and approval thresholds are configurable; an unpaid invoice is not silently written off.
- Document type/numbering and tax fields are driven by verified Tenant legal/tax profile; legal statutory requirements are deferred to ODR-003/016 post-project review.
- Issued documents and posted balances are not overwritten; corrections use linked credit/debit or adjustment workflows.

### 16.18 — ODR-015: Payment evidence and verification baseline

**Status:** Recommended domain baseline selected under the product owner's delegated decision mandate (2026-11-19).

The platform must distinguish a payment claim/intent, manual receipt entry, provider-confirmed payment, bank-statement evidence, reconciliation match and final allocation/settlement.

- Cash receipt is recorded by an authorized user and linked to cash custody/shift where used; it is not a bank-settled event.
- Manually recording UPI QR/bank transfer and entering a UTR/reference does not by itself prove that funds were received.
- A screenshot or user-uploaded proof is supporting evidence, not independent provider/bank confirmation.
- Provider callback/query may establish provider-confirmed status only after signature/authenticity/replay/amount/currency/merchant/reference/tenant checks.
- Bank statement import/reconciliation can establish bank-side evidence according to source format, matching confidence and authorized exception review.
- Unmatched, partial, duplicate, reversed, disputed and unknown outcomes remain explicit states; do not blindly retry an unknown external payment.
- Payment providers/bank integrations are optional adapters. Payment recording must remain available without a gateway.
- Refund, chargeback and reversal are linked transactions and require separate permissions/approvals.

### 16.19 — ODR-017: Accounting and financial-control baseline

**Status:** Recommended architecture/domain baseline selected under the product owner's delegated decision mandate (2026-11-19); statutory account-policy review remains external evidence-gated.

Double-entry bookkeeping is the accounting foundation. Every posted journal balances in the same accounting currency; posted journals are immutable. Operational modules produce validated posting instructions and do not directly edit ledger balances.

- Use accounting books/financial periods as explicit concepts independent of Tenant, Branch, GST registration and OperatingLocation. The simple Tenant profile may start with one default book; do not infer that each Branch or GSTIN is a separate book.
- CoA templates are editable/configurable starting points, with stable account identities, effective dates, mapping and audit. Tenant-specific accounts/dimensions do not change canonical posting semantics.
- Branch, location, department, cost centre and project are explicit dimensions where relevant, not substitutes for legal-entity/book boundaries.
- Source event, subledger, GL journal, bank evidence, settlement and Tally exchange are distinct linked facts.
- Period close/reopen, journal posting/reversal, opening balance migration, inventory costing and source-event recognition use explicit permissions, maker-checker and audit.
- Tally is an optional external adapter with versioned company/master/voucher mappings and reconciliation. The product remains the canonical internal ledger; Tally is not silently treated as source-of-truth for all platform workflows.
- Exact legal/books cardinality, statutory accounting policy and production Tally compatibility require qualified review and version-specific proof of concept.

### 16.20 — ODR-018: Workforce and payroll capability baseline

**Status:** Recommended non-statutory product baseline selected under the product owner's delegated decision mandate (2026-11-19); statutory labour/payroll review remains deferred.

Workforce/personnel management is optional and composable. A small Tenant may operate without employee records. Where enabled, distinguish Person, UserAccount, TenantMembership, workforce engagement/personnel record, Branch assignment, shift, attendance, leave, compensation and payroll transaction.

- Store work assignments and shifts as effective-dated records; assignment to a Branch is not a permission.
- Attendance captures actual presence/work evidence; scheduled shift is not proof of attendance.
- Leave requests, approvals, shift swaps, overtime, advances and reimbursements are separate workflows with permissions and audit.
- Different engagement types (employee, contractor, casual/temporary, consultant) must remain distinguishable where the business uses them; do not infer legal classification from a UI label.
- Payroll uses versioned compensation and calculation snapshots, segregated sensitive permissions and immutable posted payroll results; correction is through controlled adjustment.
- Statutory rates, deductions, thresholds, forms and labour-law applicability are reserved for current qualified review after project completion.

### 16.21 — ODR-021: Notification architecture baseline

**Status:** Recommended engineering baseline selected under the product owner's delegated decision mandate (2026-11-19).

Use provider-neutral notification events and adapters. In-app notifications are the mandatory internal channel for critical platform/business events. Push, email, SMS, messaging applications and voice channels are optional and enabled only by Tenant/user policy, permissions, consent and provider capability.

- Separate event trigger, template, recipient resolution, authorization-to-receive, channel preference, delivery attempt and final provider outcome.
- Revalidate recipient membership and notification-category permission before sending; never broadcast sensitive Branch/financial information to all users.
- Templates support language/localization, Tenant branding, approved variables and versioning. User content cannot inject arbitrary secrets or unsafe markup.
- Delivery retries are bounded/idempotent; failure or delay never changes business transaction or permission state.
- Provider secrets, consent, opt-out, quiet-hours and cost controls are managed separately from event semantics.
- No provider or channel vendor is mandated by this architecture.

### 16.22 — ODR-022: KPI and analytics governance baseline

**Status:** Recommended architecture baseline selected; official business KPI ownership and commercial report entitlements require accountable business/owner assignment before launch.

Every official KPI has a stable ID, name, definition, formula/version, unit, grain, date basis, source lineage, exclusions, freshness, owner function and validation tests. Tenant-specific targets and thresholds are configurable, but must not rewrite the official formula of a shared system KPI.

- Separate operational facts, posted financial facts, targets, budgets, forecasts and scenarios.
- Drill-down returns only source records the actor may access; aggregation must not reveal restricted branch/person data through small cohorts or filters.
- Tenant custom dashboards/reports may use governed datasets and declared data scopes. Custom metrics must declare formula, source, unit and owner.
- Export/report access is separately permissioned and rechecked at download.
- Commercial plan entitlement for premium analytics is separate from data permission.
- The business must assign an accountable owner function for each official KPI before using it as a contractual or management target.

### 16.23 — ODR-023: Forecasting and projection baseline

**Status:** Recommended architecture baseline selected; business prioritization is not a blocker for extensible architecture.

Begin with transparent, explainable statistical/baseline methods and simple seasonal/recency approaches where sufficient clean history exists. Advanced ML is optional and justified only by out-of-sample improvement against a simple baseline.

Each forecast run records dataset cutoff, horizon, unit/grain, assumptions, method/model version, exclusions, confidence/uncertainty where defensible, accuracy metrics and limitations. Forecasts are advisory and never directly post stock, purchase, payroll, invoice or journal transactions. Users may configure horizons and scenario inputs where supported; output must not claim accuracy unsupported by history.

### 16.24 — ODR-024: Budget and scenario-planning baseline

**Status:** Recommended architecture baseline selected; Tenant thresholds and approver assignments are configurable.

Budgets are versioned plans with draft, review, approved, superseded and closed lifecycle states as relevant. Actual posted values, budgets, targets, forecasts and hypothetical scenarios remain separate classifications. Editing an approved budget creates a new revision; it does not overwrite the prior approved version.

Tenants may configure budget dimension, period, scenario and approval thresholds within platform constraints. Approval is permission-based, amount/scope-bounded and subject to maker-checker. Scenario planning never posts financial journals or changes operational stock. Actual-versus-budget formulas and dimension mapping are versioned and reproducible.

### 16.25 — ODR-025: SaaS plan and entitlement architecture baseline

**Status:** Recommended product/engineering baseline selected; pricing, plan packaging and contractual service commitments require owner approval before commercial launch.

Plans package capability entitlements, usage quotas, support/service tier and optional dedicated-resource features. Keep separate:
- Plan entitlement — whether a capability/usage is commercially available.
- Tenant capability activation — whether the Tenant has enabled it.
- Tenant configuration — how the enabled capability behaves.
- Principal permission — who may view/perform actions.
- Feature/release flag — controlled rollout or temporary operational disablement.
- Extension grant — what a separately governed extension may access.

No plan may grant user permission, data scope or bypass a security/integrity invariant. Quota enforcement must be tenant-aware and transparent. Do not silently discard accepted transactions when quotas are reached; block new work safely, preserve accepted work and provide a clear remediation path. Trials, upgrades/downgrades and suspension must preserve historical data and define read/export/recovery access. Exact commercial plans, price points and grace periods require explicit owner approval.

### 16.26 — ODR-026: Data lifecycle and privacy architecture baseline

**Status:** Recommended architecture baseline selected; legal retention schedules and contractual periods require post-project evidence and approval.

Classify data by purpose and sensitivity (identity, contact, operational, financial, tax, payroll, security/audit, file/object, analytics and integration secrets). Each class must define collection purpose, access, minimization, retention trigger, archival, legal hold, export, deletion/anonymization, backup treatment, processor/provider boundaries and audit evidence.

A single retention period for all data is prohibited. Legal hold overrides routine deletion. Posted financial/stock/audit records retain integrity according to applicable policy; personal data may require controlled minimization/anonymization that does not falsify statutory history. Tenant offboarding must offer an authorized export process and a controlled lifecycle. Exact durations and lawful bases are reserved for current legal review.

### 16.27 — ODR-027: Scale and capacity methodology baseline

**Status:** Recommended engineering baseline selected; numerical business adoption envelope must be supplied or approved by the business owner before production sizing.

Use conservative, expected and stress workload envelopes across Tenant count/distribution, Branches/locations, users/devices, API traffic, concurrent writes, subscription occurrences, POS/procurement/stock/financial postings, queue volume, report/analytics load, file/object growth and retention.

Do not invent target Tenant counts or promise throughput from development hardware. Convert business adoption assumptions into measurable load profiles using pilot telemetry, model noisy-neighbour/fairness and headroom, and test load/soak/stress. Review capacity after major module launch or measured growth. Service-level budgets must have explicit cost and operational ownership.

### 16.28 — ODR-028: Availability, backup and recovery governance baseline

**Status:** Recommended recovery architecture selected; final business RPO/RTO commitments, named responders and accepted residual risks require owner approval before production.

Maintain tested backups outside the primary failure/administrative boundary, encryption/key recovery, restore validation for database and objects/configuration, documented recovery ordering, idempotent queue/outbox reconciliation and tenant isolation validation. Distinguish a successful backup job from a proven recoverable restore.

Use service tiers based on business criticality and cost. Do not publish or contract numeric RPO/RTO/availability commitments until restore/DR exercises, staffing, provider dependencies and business impact evidence support them. Name incident commander, restore authority and key custodians before production.

### 16.29 — ODR-029: Integration priority baseline

**Status:** Tally accounting import/export priority is established by the product owner's earlier explicit requirement; other provider priorities remain evidence/business-value dependent.

The platform's internal business and accounting model remains canonical. Tally Desktop synchronization and data exchange is the first-priority accounting integration, including controlled import/export, voucher/master mapping, batch identity, idempotency, conflict reports and reconciliation. It must be version-specific and proven with disposable Tally company data before release.

Bank statement, payment gateway, UPI/provider, notification, weighing-scale, milk analyzer, barcode/device and other integrations use provider-neutral adapters. Each adapter declares capabilities, authentication, tenant binding, permission, schema/version, rate limit, retry/unknown-outcome and reconciliation contract. Do not claim availability until tested with the actual provider and account tier.

### 16.30 — ODR-030: Technology selection governance

**Status:** Modular-monolith product architecture is selected under ODR-035; the concrete framework/database/mobile stack remains gated by repeatable PoC and team/operational evidence.

Technology selection must compare identical representative workflows, including tenant/permission isolation, concurrency/locking, idempotency, migration, restore, offline field use, Tally integration, deployment/cost and team maintenance ability. Preferred candidates in ADR-TECH-001 remain candidates, not approvals. Select supported stable releases and define patch/upgrade/deprecation ownership. Do not lock technology on familiarity or current VPS limits.

### 16.31 — ODR-031: Deployment and infrastructure baseline

**Status:** Linux/VPS-compatible logical deployment direction and Docker-optional policy are selected; provider, region, instance sizes and production risk acceptance require PoC/cost/recovery evidence.

Keep deployment profiles for lean pilot, separated data tier and horizontally scalable production. Avoid a pilot single-host layout becoming a hidden production constraint. Separate public edge, API, workers, database, object storage/backup and observability trust boundaries as justified by the workload. Production network isolation, secrets, backup/restore and release rollback must be verified. A provider or infrastructure free tier is not a guaranteed production capacity or backup plan.

### 16.32 — ODR-032: Localization baseline

**Status:** Recommended product baseline selected under the delegated decision mandate.

English is the primary authoring/product language initially. Localization infrastructure must support Marathi and Hindi and be extensible to additional Indian languages. UI labels, help text, templates, notifications and reports are externalized; stable machine identifiers, API enums and canonical keys remain language-neutral. Currency remains INR and IST is the default business timezone for the India-focused initial product. Branch time-zone variation and non-INR currency are not assumed. Translation ownership and rollout priority are reviewed using customer usage and quality evidence.

### 16.33 — ODR-033: Security governance baseline

**Status:** Recommended technical baseline selected; named security risk-acceptance authority and incident responders require human assignment before production.

Require MFA/step-up authentication for privileged access, permission administration, support access, sensitive exports, account recovery and other high-impact actions identified by threat analysis. Session/device revocation, secure recovery, short-lived scoped service credentials, secret rotation, least-privilege operations, secure defaults, abuse protection, dependency scanning, vulnerability response and incident evidence are mandatory controls.

MFA and step-up policy are based on permission sensitivity and risk, never job title alone. Break-glass remains the separate narrowly scoped ODR-002 Emergency Override and cannot bypass Tenant isolation. No critical/high risk may be silently accepted; residual risk requires named accountable authority, documented impact, mitigation, expiry and review before production.

### 16.34 — ODR-034: Audit and observability governance baseline

**Status:** Recommended technical baseline selected; data retention classes, named alert owners and escalation contacts require evidence and human assignment before production.

Maintain separate but correlated:
- Business/audit evidence — who did what, to which resource, in which Tenant/scope, when, why, with what outcome and policy/configuration version.
- Operational telemetry — logs, metrics, traces, queue health, performance and provider outcomes for service operation.

Material business/security/permission/configuration/financial/stock events must be durably recorded and protected against unauthorized alteration/deletion. Audit access is separately permissioned, append-oriented and secret-redacted. Operational telemetry is minimized and must not become a substitute for immutable business evidence. Alerts require severity, owner, escalation path and runbook. Retention of both datasets is classified separately and must be reconciled with legal/contractual review.

### 16.35 — ODR-036: Party identity resolution and duplicate handling baseline

**Status:** Recommended safe baseline selected under the product owner's delegated decision mandate (2026-11-19).

A duplicate candidate is not proof of identity. No automatic merge based only on name, phone, address, bank account or external Tally mapping. Exact same verified identifier from the same issuing authority is a strong candidate but still requires conflict/role/account validation.

Use a non-destructive identity-resolution operation with dedicated `party.identity.resolve` permission, same-Tenant and affected-Branch scope, before/after impact preview, reason/evidence, audit and source-to-survivor mapping. Conflicting legal/business identifiers, different natural/legal persons, cross-Tenant candidates or disputed ownership must not be merged. If posted entries, open balances, settlements, verified IDs, bank/payee data or active subscriptions are involved, require independent authorized review and relevant financial/operational clearance. Preserve original IDs as aliases and preserve all posted transaction snapshots/history. A later unmerge is a compensating identity-map action, not a promise to erase posted history.


### 16.36 — ODR-002: Shared-site accountability and custody management

**Status:** Recommended domain baseline selected on 2026-11-19 under the product owner's delegated decision mandate. This is not represented as an explicit owner approval of legal liability, contractual ownership or insurance allocation.

**Selected principle:** One product must separately record ownership, stock-ledger attribution, physical location, physical custody, handling/execution, workforce assignment, permission and approval. Shared premises or shared equipment do not merge Branch ownership, inventory, access, costs or responsibility.

**Core decision**
- Keep economic/legal ownership, stock-owning Branch, stock ledger control, physical location, physical custody, actual handler, workforce assignment, permission/grant and approval as distinct concepts.
- StockMovement records the authoritative quantity/location/status effect. CustodyEvent records the physical custody fact. They are linked but neither substitutes for the other. A custody handover does not itself create a stock or financial movement.
- The model supports CustodySubject, CustodyAssignment, CustodyEvent, CustodyHandover, CustodyEvidence, CustodyIncident and SharedResourceUse as logical domain concepts. This does not prescribe physical database tables.
- Physical custody may be held by a named person, carrier, external party or service identity. Actual handler, sender, receiver, reviewer and custodian are separately attributable. A task assignment or StockTaskAccessGrant is not proof of physical receipt.
- One-person businesses may use a simple single-party evidence flow. Larger or higher-risk operations can configure sender/receiver acknowledgement and independent device, witness, seal, scan or measurement evidence. The selected assurance profile must be effective-dated and versioned.
- A handover is not complete merely because a notification was sent, time elapsed or receiver did not respond. Pending/unacknowledged handovers retain the last verified custodian and known location; no custody gap or automatic acceptance is allowed.
- Partial receipt, rejected/damaged goods, missing items, seal/temperature/quality exceptions and no-acknowledgement cases are recorded as explicit discrepancies/incidents. Original dispatch/receipt/custody facts are immutable; correction is linked and auditable.
- Shared equipment/resources have separate owner, location, availability, allowed Branch/capability use, custody, booking, actual use, cleaning/calibration/maintenance and cost-allocation concepts. Shared use does not grant ownership, unrelated data access or asset write-off/maintenance authority.
- Tenant-configurable assurance profiles and evidence requirements may differ by workflow, product, Branch/site, resource and risk. Configuration may not disable ownership, permission, audit, event integrity, traceability or required safety/legal constraints.
- Raw milk/perishable workflows may capture time, quantity, container/lot, transfer parties, location, temperature, sampling/quality, cleaning and acceptance evidence at configured control points. Exact limits come from versioned product/process policy and verified applicable requirements, not one universal hardcoded value.
- Custody incidents may trigger quarantine, hold, review or other protective measures. They do not automatically create a stock adjustment, supplier deduction, customer credit, write-off, financial journal, payroll deduction or finding of negligence/liability.
- When a grant or permission expires/revokes while a worker physically holds goods, access to new tasks and stock mutations is blocked, but the actual custody record remains. Work is held for controlled handover/reassignment/incident resolution; no phantom transfer, auto-completion, rollback or disappearance is allowed.

**Configuration and scale**
- The domain supports Basic single-party capture, Acknowledged handover and Verified high-assurance profiles as editable starting templates.
- A Tenant may enable custody tracking only for the subject classes and workflows it actually manages, while a large Tenant can apply more stringent controls to high-risk locations, cold-chain, bulk handling, high-value items and shared resources.
- Site/Branch overrides are allowed only for keys explicitly declared overrideable. Historical transactions retain the configuration/profile version used.
- Integrations with scales, milk analysers, temperature sensors, barcode/RFID, GPS, tanker devices and external logistics systems use versioned contracts, declared identity, permission, resource limits and revocation; device connection does not grant general Tenant access.
- All reads and actions remain subject to the permission-first model; a custody role/title/account type confers no automatic access.

**External review boundary**
Legal title, contractual risk transfer, carrier liability, insurance claims, landlord/facility obligations, employment liability, statutory record retention and payroll consequences cannot be inferred by the software. Record the governing agreement/evidence and obtain qualified review where applicable. These issues do not prevent specifying the operational custody evidence model.

**Verification**
- No shared site/resource relationship merges stock ownership or Branch permissions.
- No handover is accepted without the evidence required by the effective assurance profile.
- Partial/rejected receipt preserves actual custodian and outstanding quantity.
- A custody-only event does not change stock or financial balances.
- A stock movement and custody event are linked where both occur without duplicate quantity.
- Grant revocation blocks further authorized operations but preserves continuous physical-custody history.
- A discrepancy never silently becomes a write-off, employee deduction or legal-liability conclusion.
- Small and Enterprise operating profiles both complete suitable workflows without either forcing unnecessary controls or omitting configured high-risk controls.

**Source and evidence basis:** GS1 Critical Tracking Event/Key Data Element concepts; NDDB raw-milk chilling, equipment cleaning and transport practices; FSSAI Milk and Milk Products FSMS guidance; Codex CXC 57-2004. See the research notes and source links in `docs/02-domain/SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md`. These inform operational design and do not replace current legal applicability review.



### 16.37 — ODR-002: Route execution, workforce/resource assignment and dispatch accountability

**Status:** Recommended domain baseline selected on 2026-11-19 under the product owner's delegated decision mandate. This is not represented as an explicit approval of every operational detail or a legal determination of driver, vehicle, carrier or food-business compliance.

**Selected product principle:** A Route describes an authorized Branch-owned delivery sequence; a DeliveryPlan groups demand and operational runs for a date/shift; a DeliveryRun is the actual execution unit; personnel, transport resources, physical custody and dispatch authorization are separately recorded. No job title, vehicle ownership or route assignment grants business permissions.

**Route and planning**
- Every Route belongs to exactly one Branch. Its ServiceArea coverage must be within that Branch's effective BranchServiceAreaAssignment. Geographic overlap or common premises does not grant another Branch route ownership or access.
- A DeliveryRun uses a Branch-owned Route/route plan whose owning Branch matches the run's Branch. The route/plan version, stop ordering, demand allocation and relevant constraints are snapshotted at dispatch.
- To keep the smallest business simple, a Route may be created as a one-off manual route directly while preparing a run; a reusable saved RouteTemplate, route optimization, maps and GPS are not prerequisites. Manual stop ordering remains a complete workflow.
- A DeliveryPlan/dispatch wave may group one or many DeliveryRuns. A small operator commonly uses one plan and one run; a larger business may split workload across multiple runs/vehicles/crews. A stop or demand quantity cannot be actively allocated/fulfilled by competing runs beyond the authorized split quantity.
- The same reusable Route may support different dates/shifts/runs. Each run keeps its own assignments and historical snapshot. Route revision never rewrites a dispatched/completed run.

**Personnel and workforce assignment**
- The run records one accountable run assignee/operator and supports one or more additional named participants. The same person may plan, dispatch, drive, deliver and close in a small business; a separate employee profile is not mandatory for an owner-operated workflow.
- Operational duties such as driver, delivery handler, helper or accountable run assignee describe what a person is assigned to do on that run. They are not permission roles and do not grant data visibility or action authority.
- Every participant must have an active identity appropriate to the workflow, the exact current permissions/scope and a valid run assignment. A required workforce/shift/duty assignment must be separately authorized under the Tenant's workforce policy. Run assignment and a stock-task grant are independent; a stock-task grant does not authorize delivery work.
- A cross-Branch participant needs explicit run/Branch permissions and the workforce assignment required by applicable Tenant policy. Home/Primary Branch, role title or geographic proximity alone is never sufficient. No blanket Home-Branch consent is introduced; the selected workforce separation in section 16.6 continues to apply.
- A participant's assignment is effective-dated and versioned. Reassignment/revocation immediately removes the former participant's access to further assigned work; physical goods already held remain in that party's custody until a real, evidenced handover or controlled resolution.

**Vehicle and transport resource**
- Vehicle assignment is optional. Walking, cycling, customer pickup, owner-operated transport, and other valid non-vehicle delivery methods must remain supported.
- A Tenant may track vehicles as Assets or use a light-weight transport-resource reference where full asset management is unnecessary. Do not force fleet/maintenance setup on a small business.
- A run may have sequential time-bounded primary vehicle/transport-resource assignments (for example, a vehicle breakdown and replacement). At most one primary transport resource is active for the same indivisible run segment at a time. If deliveries are split across multiple vehicles concurrently, use separate DeliveryRuns under the same DeliveryPlan with non-overlapping stop/quantity allocation.
- Where the Tenant tracks vehicle capacity, assignment validates applicable weight/volume/container/product constraints. If the product requires temperature-controlled or hygienic transport, validate the configured vehicle/equipment readiness and handling profile before dispatch.
- Where exclusive booking is enabled, one vehicle/resource cannot be concurrently assigned to overlapping runs. An assignment cannot bypass existing maintenance, inspection, cleaning, calibration, downtime or availability restrictions.
- Driver qualification, vehicle documents, insurance/fitness, permits or other credentials may be recorded with source, verifier, effective dates and expiry where Tenant policy or verified applicable rules require them. The product must not claim legal compliance merely because a document was uploaded or a checkbox was selected; exact mandatory document types and validation authorities remain subject to current jurisdiction-specific review.

**Dispatch responsibility and lifecycle**
- Planning, personnel/resource assignment, dispatch authorization, dispatch execution, stop outcome capture, exception review and run closure are distinct actions with distinct permissions.
- Dispatch is permitted only after validating: Branch/Route ownership; authorized ServiceArea coverage; current run/manifest version; eligible demand and non-duplicated stop/quantity allocation; stock availability and picked/dispatched quantities; participant identity, permission and assignment; vehicle/resource availability and capacity where used; required qualification/evidence status; configured product hygiene/temperature/packaging controls; custody handover evidence; and unresolved holds/exceptions.
- A specifically authorized user holding the exact dispatch permission may dispatch. A separate reviewer/approver is required only where the applicable Tenant risk policy, financial/quantity threshold or fixed control requires it. Tenant configuration may add independent checks but cannot remove mandatory ownership, authorization, stock, custody, safety or audit controls.
- Dispatch freezes/version-locks the route, stop manifest, source demand allocation, assigned participants/resources and custody context. Any post-dispatch route, vehicle, driver, stop, quantity or destination change uses an explicit exception/reassignment event with reason, actor, permission, time and any required approval; never silently rewrite the dispatched plan.
- Vehicle/driver failure, absence, safety hold, breakdown, temperature excursion, route blockage or connectivity loss places the affected run/segment under a visible hold or exception path. Replacement personnel/resource require valid assignment, permission, availability and a real custody handover where goods change hands. If no valid replacement exists, work remains held; no automatic dispatch, custody transfer or successful completion is inferred.
- A run closes only after reconciling dispatched, delivered, returned, undelivered, damaged/quarantined, cash/receipt, reusable-container and custody balances as applicable. Closing with unresolved exceptions is a distinct permissioned outcome and must preserve outstanding obligations.

**Food handling, traceability and evidence**
- The applicable product/process risk profile determines vehicle suitability, cleanliness, covered/hygienic transport, packaging, protection from contamination, temperature control, timestamps and traceability evidence. These requirements are configurable by product/process/route where law permits; mandatory verified controls cannot be disabled.
- FSSAI's advisory dated 2026-10-15 directs FBOs handling the perishable commodities addressed by that advisory to use clean, covered and hygienic food-suitable conveyances, maintain prescribed temperatures, use food-grade packaging, protect products during loading/unloading/transport, and maintain consignment records/traceability. Apply this to the advisory's stated scope and verified legal applicability; do not turn it into one universal temperature or vehicle rule for every SKU and dairy workflow.
- FSSAI's Milk and Milk Products FSMS guidance (2019) and NDDB milk-chain material inform raw-milk collection, chilling, clean equipment/containers, insulated tanker/can transport, receiving checks and traceability. Values and time/temperature controls must be versioned against the product/process and authoritative applicability; do not present guidance text as a universal statutory value.
- Basic delivery evidence remains actor, run/stop, timestamp, actual quantities, outcome and reason. Customer signature, OTP/PIN, photo, location/GPS, sensor/temperature device and continuous tracking remain optional, purpose-limited and policy-controlled unless a verified applicable requirement makes an evidence type mandatory.

**Configurability and extensibility**
- Tenant-configurable values include shift/calendar, route templates/manual sequence, stop ordering, dispatch checklist, vehicle capacity/availability, assignment limits, service time, proof-of-delivery evidence, product handling profile, temperature/condition thresholds sourced from applicable rules, reattempt policy and exception/escalation thresholds.
- Historical run/dispatch records preserve the configuration, route and evidence profile versions used. Only configuration keys explicitly declared overrideable may be changed at Branch/site/route scope.
- Route optimization, map/geocoding, telematics/GPS, barcode/RFID, digital proof, fleet systems and external carrier services use versioned provider-neutral adapters, scoped identities, permissions, rate/resource limits, audit and revocation. Optimization is advisory by default; it cannot bypass Branch/ServiceArea ownership, capacity, workforce, product-safety, custody or customer commitments, nor silently modify the dispatched plan.
- High-volume optimization and telemetry work must be asynchronous, tenant-fair and isolated from core stock/financial transactions. Manual planning and safe dispatch remain available when an optimization or external provider fails.

**Verification**
- Owner-only delivery works without creating employee or vehicle records.
- A manual non-optimized Route and one DeliveryRun can be planned and completed.
- One Route can be reused across dates/shifts while each run preserves its snapshot.
- Branch/Route and Branch/ServiceArea mismatch is rejected.
- Personnel, driver and vehicle assignments are validated independently of job title; invalid/revoked assignments cannot dispatch or continue protected actions.
- The same vehicle or worker cannot be double-booked where exclusive assignment is configured; split vehicle work uses separate runs and non-overlapping demand allocations.
- Dispatch cannot proceed with missing required evidence, insufficient stock, invalid assignment, over-capacity vehicle or unmet configured food-handling control.
- A breakdown/driver replacement preserves completed stops, actual custody, route history and outstanding goods; replacement requires explicit authorization and handover.
- Post-dispatch changes are append-only, permissioned and auditable.
- Route optimization/provider outage does not prevent a permitted manual workflow or corrupt the run.
- Offline/stale/duplicate commands are revalidated and cannot double-fulfil, duplicate stock or payment effects, or retain revoked assignment access.
- Perishable consignment records preserve product, batch/lot where applicable, origin, destination, parties, timestamps, quantity, packaging/vehicle/temperature evidence and disposition as required by the applicable profile.
- A run can be closed only with a reconciled outcome or an explicitly authorized close-with-exceptions state.

**External evidence dependencies**
Exact driver licence/qualification categories, vehicle fitness/insurance/permit evidence, food-transport legal applicability, record retention, location/photo/privacy and contractual carrier liability require current jurisdiction-specific authoritative review. This does not block defining the operational dispatch model; it blocks claiming legal compliance or hardcoding unverified document lists.

**Source basis**
- FSSAI, Advisory on Handling, Storage and Transportation of Perishable Food Commodities under Controlled Temperature Conditions, 2026-10-15: https://www.fssai.gov.in/docs/food-law/advisory/Advisory%20dated%2015.10.2026_Transportation%20and%20storage%20of%20perishabe%20commodities.pdf
- FSSAI, Food Safety Management System Guidance Document for Milk and Milk Products, 2019-03-14: https://mail.fssai.gov.in/upload/uploadfiles/files/Guidance_Document_Milk_14_03_2019.pdf
- NDDB, Milkipedia — Raw Milk Transportation and handling: https://www.nddb.coop/ccnddb/milkipedia
- GS1 Global Traceability Standard — Critical Tracking Events and Key Data Elements: https://support.gs1.org/support/solutions/articles/43000734535-what-is-the-gs1-traceability-standard

These sources inform operational design and do not replace legal advice or activity-specific compliance review.

