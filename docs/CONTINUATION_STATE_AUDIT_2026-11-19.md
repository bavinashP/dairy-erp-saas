# Dairy ERP SaaS — Continuation State Audit

**Audit date:** 2026-11-19  
**Repository:** https://github.com/bavinashP/dairy-erp-saas  
**Branch:** `main`  
**Verified branch head before this report was created:** `33f81a5283135404cb1b39b498d49f477d0342a5`  
**Repository documentation inventory:** 63 Markdown files under `docs/` at the time of the audit  
**Work type:** Documentation-only continuation, consistency audit and decision-register reconciliation  
**Application code / physical database schema changes in this work:** None

> This report supersedes the *current-state* interpretation of the 2026-11-17 audit. The earlier report remains a historical snapshot. This report does not certify production readiness, legal compliance, executable test results or implementation behavior.

## 1. Executive status

The documentation now contains a broad, cross-linked product baseline for a single Dairy ERP SaaS portal supporting owner-operated, small-team, multi-Branch and enterprise businesses. The product's governing design principle is explicit:

- Keep security, Tenant/Branch isolation, authorization, ownership, immutable history, concurrency and integrity rules fixed.
- Make legitimate business variation configurable through versioned, scoped and permission-controlled policies.
- Support customer presentation/workflow customization only on governed, upgrade-safe surfaces.
- Add capabilities through versioned, permission-scoped extension contracts rather than customer-specific code forks.
- Preserve the same domain semantics as the Tenant grows; small businesses must not need artificial employee, fleet, approval or enterprise infrastructure setup.

ODR-002's operational design has been specified across shared-site stock handling, permission matrix, custody/accountability, Route/Delivery execution, grant lifecycle and in-progress task reconciliation. ODR-005's documentation-phase permission inventory and conceptual resource/requirement/exception mappings have been completed to the extent possible without future implementation contracts.

The repository is **not** represented as production-ready. Concrete API/command/event/job/device/integration contracts, implementation-level field classification, executable automated test evidence, field trials, external legal/accounting review and human production approvals remain separate gates.

## 2. Repository and documentation inventory

- Repository and `main` branch were accessible through the connected GitHub integration.
- The recursive repository tree reported 63 Markdown documents under `docs/`.
- The existing system overview, tenancy, API, mobile/offline, concurrency, outbox, storage, recovery, scalability, analytics, VPS deployment, security, audit, technology and domain documents remain in place.
- The architecture documentation index already lists the Permission Catalogue, Domain Permission and Approval Control Matrix, Domain Permission and Resource Mapping Register, and Cross-Branch Permission Matrix.
- The 2026-11-17 continuation audit is retained as a historical record. This report is the later current-state snapshot.

## 3. ODR-002 operational baseline

The following logical/domain baselines are documented; their authority labels remain distinct between explicit product-owner approvals and assistant-selected recommendations:

| ODR-002 area | Current documentation location | Current state |
|---|---|---|
| Cross-Branch stock-task approval and authority | ODR-002 decision records; ERS; Cross-Branch Permission Matrix | Stock-owning Branch approval by an authorized user holding the exact permission is mandatory; approval is bound to the exact scope/version |
| Grant expiry/revocation and held work | ODR-002 §16.2 and §16.38; ERS §16.12; STM §3.11–3.12 | Immediate zero-grace denial; completed work immutable; active task held for controlled reconciliation |
| Emergency Override | ODR-002 §16.3; permission catalogue; approval-control matrix; QES | Permission-based, narrow scope, 60-minute initial period, one independently authorized extension up to 120 minutes total, retrospective review for self-activation |
| Delegated approval | ODR-002 §16.4; ERS; Permission Catalogue; QES | Explicit delegation permission, exact scope and limits, maximum 30 calendar days, no onward delegation or automatic renewal; non-delegable actions remain protected |
| Expiry notification and zero grace | ODR-002 §16.5; ODR-021 | Notification schedule selected as a recommended baseline; notification delivery never extends authority; provider/channel configuration remains under ODR-021 |
| Home Branch/workforce boundary | ODR-002 §16.6; ERS; Business Rules Catalogue | No blanket Home-Branch consent; required workforce assignment is a separate authorized workflow; stock grant does not alter schedule/attendance/employment/pay |
| Permission-first cross-Branch matrix | ODR-002 §16.7; `CROSS_BRANCH_PERMISSION_MATRIX.md` | Per-action access, default-deny, explicit permissions and scope; a grant does not create independent worker permission or general Branch access |
| Shared-site accountability and custody | ODR-002 §16.36; `SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md` | Ownership, stock-ledger attribution, physical location, custody, handler, workforce, permission and approval remain distinct |
| Route and dispatch execution | ODR-002 §16.37; `DELIVERY_LIFECYCLE_AND_EXCEPTION_RULES.md` | One-off manual Route and owner-operated delivery supported; multi-run plans, personnel/resource assignments, dispatch snapshots and exception workflows defined |
| Detailed grant/task lifecycle | ODR-002 §16.38; ERS §16.12; ERR-050..056 | Immutable GrantVersion, exact payload approval, material-change reapproval, automatic invalidation, task hold and append-only reconciliation |

The above is a documentation baseline and scenario design. It is not a claim that these workflows have been implemented, field-tested or legally certified.

## 4. ODR-005 — canonical permission and authorization coverage

The current conceptual permission documentation was re-fetched from `main` and checked across the catalogue, resource mapping, State Transition Matrix, FRS, RTM, Exception Matrix, Business Rules, NFR and QES.

### 4.1 Verified coverage counts

| Coverage item | Verified result |
|---|---:|
| Canonical atomic permission keys in ARC-IAM-CAT-001 §15.2 | 704 unique keys |
| Canonical resource namespaces in ARC-IAM-MAP-001 §3 | 205 unique resources |
| Permission keys mapped to resource namespaces | 704 / 704; no missing or additional key |
| Resource namespaces in the mapping table | 205 / 205 unique; no duplicate namespace row |
| State Transition Matrix workflows | 15 workflows (§3.1–§3.15) |
| Distinct canonical permission keys used by current state-machine bindings | 251; no key outside the canonical catalogue |
| FRS requirements | 195 unique IDs |
| FRS requirements mapped in RTM-001 | 195 / 195, with no missing, extra or duplicate requirement ID |
| FRS requirement ranges mapped in ARC-IAM-MAP-001 §5.2.1 | 195 / 195, with no missing, extra or duplicate requirement ID |
| Current exception cases | ERR-001 through ERR-064 (64 cases) |
| Exception/recovery mapping groups in ARC-IAM-MAP-001 §5.2.2 | 17 groups covering ERR-001..064 |
| Permission/approval control acceptance tests | AT-CTRL-001..026 |
| FRS/permission mapping acceptance tests | AT-IAM-021..030 |

The resource mapping table was also checked for repeated resource names within each FRS/ERR mapping row; duplicate resource references found during review were removed.

### 4.2 Selected authorization semantics

- Account identity authenticates an actor but does not grant business permissions.
- Role/permission bundles are optional, Tenant-configurable packaging; job title, role label, account type, Home/Primary Branch, feature entitlement, client/device type and physical proximity do not grant authority.
- Each read, sensitive-field disclosure, action, approval, post, reversal, export, configuration and access-administration operation is separately mapped and scoped.
- Tenant direct/group/bundle assignments are configurable only within the assigning actor's delegation authority. Explicit deny and default deny apply; unknown or unmapped protected operations fail closed.
- Workflow approval requirements are separate from action permission. Generic approval capability, task assignment or an ApprovalAction does not replace the exact domain permission.
- ApprovalPolicyVersions and ApprovalPolicyEvaluationSnapshots are immutable and effective-dated. Weakening a policy requires independent approval. A stricter policy may add requirements to an in-flight request; a less restrictive policy cannot silently relax a pending request.
- Sensitive fields, documents, query surfaces and exports require their exact registered permissions plus parent-resource scope and purpose. Download-time authorization is revalidated.
- Extension permission keys are namespaced, versioned and limited to declared extension resources. Installation/activation alone grants no data access.

### 4.3 Exception and recovery authorization

FRS-IAM-040, RTM-001, ARC-IAM-MAP-001 §5.2.2, AT-IAM-029..030, BR-FIX-041 and NFR-DATA-018 now express the same boundary:

- Viewing/reporting/reviewing an exception does not authorize a retry, correction, release, reversal, adjustment, write-off, refund, financial/stock post or export.
- Each corrective effect uses the exact permission(s) for its affected resource(s) and any required approval.
- Multi-resource recovery requires independent authorization for each effect, with linked audit/reconciliation evidence.
- No catch-all `exception.resolve`, `recovery.admin` or wildcard recovery authority is defined.
- A future unmapped exception/recovery command defaults to deny until the canonical catalogue and mapping are updated.

### 4.4 Remaining ODR-005 gates

ODR-005 is **partially resolved**, not because the current conceptual key/resource/FRS/exception mapping is missing, but because the following later-stage evidence does not exist yet:

1. Concrete API routes, commands, event handlers, background jobs, device/mobile operations and integration operations must be mapped after their contracts have been designed.
2. Exact field-by-field sensitivity mapping must be completed against actual request/response payloads, report columns, files and exports.
3. AT-IAM/AT-CTRL and domain acceptance scenarios must become executable automated tests and produce run evidence when implementation and test environments exist.

No speculative endpoint or handler names have been introduced merely to claim those later gates are complete.

## 5. Cross-document integrity corrections made during this continuation

The following consistency issues were found and corrected:

- RTM-001 did not include FRS-GEN-016 and FRS-GEN-017; its general-requirement range now covers FRS-GEN-001..017.
- FRS-IAM-040 was added for exception/recovery permission mapping and propagated to RTM, MAP, QES, Business Rules and NFR.
- NFR-SEC-025 had a duplicate row; the duplicate was removed.
- Duplicate `analytics.report` references in the FRS analytics mapping and ERR-030 mapping were removed.
- Two duplicate resource references in permission mapping rows were removed.
- The QES Authority and FRS/NFR ODR dependency descriptions were refreshed to distinguish completed conceptual mappings from future implementation-contract and automated-test gates.
- The documentation index already contains the permission catalogue, approval-control matrix and resource mapping register; no duplicate documents were created.

## 6. Other ODRs — genuine approval and evidence dependencies

The following remain visible and must not be mechanically marked resolved:

| ODR | Remaining dependency | Correct classification |
|---|---|---|
| ODR-003 | LegalEntity / AccountingEntity / AccountingBook / registration cardinalities and their legal/accounting meaning | Deferred post-project legal/accounting review; do not infer |
| ODR-008 | Actual instrument tolerances, device methods, Legal Metrology applicability, statutory rounding, inventory valuation and Tally target precision-loss cases | Evidence-gated |
| ODR-009 | Current legal quality thresholds and instrument-specific methods | Evidence-gated; product/process profiles are configurable |
| ODR-012 / ODR-017 | Costing/valuation, books and accounting recognition; ODR-012 §16.44 stock-attribution boundary must respect the separate legal-entity/accounting/tax gate | Evidence-gated and qualified accounting review |
| ODR-016 | GST classification, documents, e-invoice/e-way-bill and retention applicability | Deferred post-project authoritative legal/tax review |
| ODR-018 | Labour/payroll statutory applicability | Deferred post-project qualified legal review |
| ODR-019 / ODR-020 | Mobile UX field trials, device support, offline duration/cache policy and conflict evidence | Pilot/owner evidence gate |
| ODR-021 | Actual notification providers, consent, pricing, channel fallback and deliverability | Provider capability/cost evidence |
| ODR-022 | Official KPI owners/definitions and commercial analytics plan entitlements | Business-owner assignment/approval before launch |
| ODR-023 | Business priority of forecast outputs/horizons | Product prioritization; technical baseline remains extensible |
| ODR-024 | Tenant budget starter templates and actual approval ownership/thresholds | Tenant policy configurable; commercial/default template approval before launch |
| ODR-025 | Commercial plan packaging, price points, trials/grace and contractual service tiers | Explicit owner approval before commercial launch |
| ODR-026 | Data retention schedules, lawful purposes and legal-hold obligations | Current legal/contract evidence and data-owner approval |
| ODR-027 | Target Tenant adoption/workload assumptions and numeric capacity envelope | Business-owner input plus pilot telemetry before production sizing |
| ODR-028 | Production RPO/RTO/availability commitments, named responders, risk acceptance and restore evidence | Owner approval and tested operational evidence |
| ODR-029 | Provider capability/contract/cost proof for non-Tally integrations | Evidence/business-priority dependent; Tally remains the first accounting integration priority |
| ODR-030 | Final framework/database/mobile stack | Repeatable PoC, team capability and explicit technology approval |
| ODR-031 | Production provider/region, instance sizing, data-location and single-host risk acceptance | Current provider/cost/security/recovery evidence plus owner approval |
| ODR-032 | Localization rollout priority and translation ownership | Usage/quality evidence and human assignment |
| ODR-033 / ODR-034 | Named security risk-acceptance authority, responders, audit retention and alert owners | Human production-governance assignment and evidence |

These dependencies do not justify inventing numeric thresholds, legal conclusions, provider guarantees, production SLOs or a technology selection. The documented architecture already provides a baseline for them to be validated.

## 7. Phase boundary and production-readiness statement

This continuation remains **documentation-only**. No application code, SQL/DDL, physical schema, ORM models, migrations, controllers, services, API implementation, mobile application or production infrastructure were created or changed.

Before development/production, the relevant gates still include:

- ERS/DBR physical database readiness and approved legal/accounting boundaries.
- Concrete API/command/event/job/device/integration permission binding.
- Exact implementation-level sensitive-field classification.
- Executable authorization, workflow, money, stock, concurrency, offline and recovery tests.
- Current legal/accounting/food-safety applicability review where required.
- Tally and external-provider compatibility PoCs.
- Load/soak/stress evidence and approved workload assumptions.
- Restore/DR exercises, production RPO/RTO and named operational responders.
- Final technology/provider/commercial approvals where required.

**Overall assessment:** The current domain and architecture documentation is substantially more complete and cross-linked. ODR-002's operational domain baselines and ODR-005's conceptual permission/resource/FRS/exception coverage are documented and verified at the source-text/traceability level. The repository is not yet certified as implementation-ready or production-ready; the remaining gates above are intentionally preserved.

## 8. Authoritative follow-up documents

- [Open Decisions Register](01-product/OPEN_DECISIONS_REGISTER.md)
- [Permission Catalogue and Assignment Governance](04-architecture/PERMISSION_CATALOGUE_AND_ASSIGNMENT_GOVERNANCE.md)
- [Domain Permission and Approval Control Matrix](04-architecture/DOMAIN_PERMISSION_AND_APPROVAL_CONTROL_MATRIX.md)
- [Domain Permission and Resource Mapping Register](04-architecture/DOMAIN_PERMISSION_AND_RESOURCE_MAPPING_REGISTER.md)
- [Functional Requirements Specification](03-requirements/FUNCTIONAL_REQUIREMENTS_SPECIFICATION.md)
- [Requirements Traceability Matrix](03-requirements/REQUIREMENTS_TRACEABILITY_MATRIX.md)
- [State Transition Matrix](03-requirements/STATE_TRANSITION_MATRIX.md)
- [Exception, Reversal and Recovery Matrix](03-requirements/EXCEPTION_REVERSAL_AND_RECOVERY_MATRIX.md)
- [Business Rules Catalogue](03-requirements/BUSINESS_RULES_CATALOGUE.md)
- [Non-Functional Requirements Specification](03-requirements/NON_FUNCTIONAL_REQUIREMENTS_SPECIFICATION.md)
- [Quality Engineering and Acceptance Test Strategy](07-quality/QUALITY_ENGINEERING_AND_ACCEPTANCE_TEST_STRATEGY.md)
- [Shared-Site Accountability and Custody Management](02-domain/SHARED_SITE_ACCOUNTABILITY_AND_CUSTODY_MANAGEMENT.md)
- [Delivery Lifecycle and Exception Rules](02-domain/DELIVERY_LIFECYCLE_AND_EXCEPTION_RULES.md)

**No application implementation was performed as part of this audit.**
