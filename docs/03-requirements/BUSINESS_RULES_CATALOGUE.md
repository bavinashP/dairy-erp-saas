# Business Rules Catalogue

**Document ID:** BRC-001  
**Status:** Proposed rule catalogue — approval required for business-policy defaults  
**Purpose:** Classify invariants, tenant/branch configuration and extension points without confusing flexibility with permission to weaken integrity.

## 1. Rule classification

| Class | Meaning | Change authority | Examples |
|---|---|---|---|
| **Fixed invariant** | Must hold for every tenant and workflow; cannot be switched off by plan or tenant setting | Product/platform governance and controlled versioned change | Tenant isolation, server-side authorization, balanced posted journals, immutable posted history, idempotent financial posting |
| **Configurable policy** | Legitimate businesses may choose different values or workflow options within validated boundaries | Authorized tenant administrator, sometimes platform plan boundary; effective-dated and audited | Cut-off times, shifts, billing cycle, route calendar, approval thresholds, enabled capabilities |
| **Extensible capability** | New provider, workflow strategy, report, adapter or business capability can be added without rewriting the invariant core | Product architecture/release governance; tenant enablement may be separately configurable | Payment adapters, notification providers, Tally adapter versions, new recurrence strategy, analytics models |
| **Open decision** | Not enough approved evidence to define a rule or default | Product owner decision after scenario/evidence review | ODR entries; no implementation assumption permitted |

A setting may be configurable only if it does not violate a fixed invariant. Extensibility adds a governed implementation; it does not allow arbitrary tenant-supplied code or bypass of security.

## 2. Fixed platform and data-integrity invariants

| ID | Rule | Applies to | Verification |
|---|---|---|---|
| BR-FIX-001 | Every tenant-owned record and query must resolve to authenticated tenant context; client-supplied tenant identity is not trusted. | All modules/API/jobs/exports | Cross-tenant negative tests |
| BR-FIX-002 | Branch/location scope is evaluated separately from tenant scope; a tenant-wide record may be branch-neutral only where its domain permits. | All branch-aware operations | Cross-branch and branch-neutral tests |
| BR-FIX-003 | Plan entitlement grants feature availability/usage allowance, never data access or permission. | SaaS and all tenant features | Entitlement/authorization independent tests |
| BR-FIX-004 | All authoritative business decisions execute server-side. | Price, tax, stock, balance, workflow, permission | Tampered-client tests |
| BR-FIX-005 | Material action history records actor, time, source, reason and outcome; sensitive secrets are never written to logs. | All material workflows | Audit and log inspection |
| BR-FIX-006 | A retry of a material command must not create duplicate financial, stock, delivery or schedule effects. | Posting, occurrence generation, imports, sync, delivery | Duplicate/retry tests |
| BR-FIX-007 | Concurrent material changes cannot silently overwrite each other; stale versions receive conflict or controlled merge outcomes. | Stock, schedule, fulfilment, allocation, journals, sync | Race-condition tests |
| BR-FIX-008 | Historical finalized transactions retain the effective versions/snapshots used at the time. | Price, tax, UOM, schedule, rates, posting rules, payroll | Historical reproducibility |
| BR-FIX-009 | Posted journals balance exactly; debit and credit totals match under approved decimal precision. | Accounting | Unbalanced journal rejected |
| BR-FIX-010 | Posted financial entries and posted stock movements are not directly edited or hard-deleted; correction uses linked reversal/adjustment. | Finance and inventory | Mutation denial and reversal linkage |
| BR-FIX-011 | Stock balances and party balances are derived from authoritative movements/postings and allocations, not manual overwrite. | Inventory and finance | Rebuild/reconciliation test |
| BR-FIX-012 | Planned, dispatched, delivered, returned and billable quantities remain distinct. | Subscription/delivery/billing | Quantity bridge reconciliation |
| BR-FIX-013 | Payment entry, external confirmation, settlement and reconciliation are distinct evidence/states. | Payments | Unverified UPI and unmatched statement tests |
| BR-FIX-014 | A notification failure cannot reverse a committed business transaction. | Notifications/workflows | Provider outage test |
| BR-FIX-015 | Reports, exports, background jobs and support tools enforce the same data scope as interactive APIs. | Analytics/platform operations | Export and job scope tests |
| BR-FIX-016 | Owner-only operation must not require artificial employee setup. | Tenant onboarding and operations | End-to-end owner-only scenario |
| BR-FIX-017 | Customer self-service login is outside current approved product scope. | Identity and customer experience | No customer auth flow provisioned |
| BR-FIX-018 | Currency is INR and business timezone defaults to IST; multi-currency and alternate calendar systems are not current product requirements. | Core platform | Date/amount boundary tests |
| BR-FIX-019 | Shared physical premises, shifts or proximity do not grant cross-Branch data or stock access. Cross-Branch stock handling requires an explicit, limited, effective-dated and auditable grant; stock ownership and transaction attribution remain with the stock-owning Branch. | Shared-site operations | Unauthorized cross-Branch access and stock mutation tests; ODR-002 |
| BR-FIX-020 | Emergency Override is a narrowly scoped, permission-based, time-limited exception only for qualifying emergencies; initial validity is 60 minutes, one independently authorized extension may add up to 60 minutes (120-minute hard maximum), and expiry/revocation blocks further stock mutations while preserving completed history and holding in-progress tasks for review. | Shared-site emergency stock handling | Self-activation permission, least-privilege, audit, retrospective review, expiry and concurrency tests; ODR-002 |
| BR-FIX-021 | Delegated approval is an explicit, permission-based, non-transferable and revocable authorization limited to named approval actions, scope and limits; the delegator must hold both the underlying approval permission and `approval.delegate`; validity is at most 30 calendar days with no renewal; loss of either party's relevant Tenant membership or the delegator's underlying authority invalidates it. Non-delegable actions and maker-checker controls cannot be bypassed. | ODR-002 shared-site grant approvals and explicitly delegable approval workflows | Scope/limit, 30-day expiry, revocation, non-delegable action, separation-of-duties, audit and race-condition tests |
| BR-FIX-022 | Shared-site stock-task grant expiry/revocation has zero grace: the exact effective end timestamp is exclusive and immediately blocks new tasks and stock mutations. Notification delivery, unread messages or pending extension requests never extend access; in-progress work is held for controlled review. | Shared-site stock-task grants | Exact-boundary expiry, no-grace, notification-failure, extension-race and held-task tests; ODR-002 |
| BR-FIX-023 | A shared-site stock-task grant authorizes only specified inventory actions; it does not create or modify a workforce assignment, shift, attendance, employment/engagement terms, compensation or cost allocation. Any workforce assignment required by tenant policy must be separately authorized through the workforce workflow. | Shared-site stock handling and workforce scheduling | Verify stock grants cannot mutate workforce records and required workforce assignments are independently authorized; ODR-002 |
| BR-FIX-024 | Business data visibility and action authority are determined only by effective explicit permissions and applicable scopes, not by role title, job title, seniority, user-account type or client type. Roles are optional tenant-configurable permission bundles. Permissions can be assigned, restricted, changed, expired and revoked within delegated authority. Missing/ambiguous authorization defaults to deny; tenant configuration cannot weaken tenant isolation, audit, ownership, segregation-of-duties or integrity invariants. | All modules, tenants, users, APIs, clients, reports, exports, jobs and integrations | Same permissions/context produce same result regardless of titles/account labels; grant/revoke and stale-session/cache/offline tests; ODR-002 section 16.7 |

## 3. Configurable business policies

These are configurable candidates. The precise setting owner, default, scope and precedence must be defined before implementation.

| ID | Policy area | Candidate configuration | Guardrails / unresolved decision |
|---|---|---|---|
| BR-CFG-001 | Operating profile | Resale-only, loose milk, repacking, processing, delivery-focused, counter-focused | Capability does not remove common accounting/audit controls |
| BR-CFG-002 | Branch/location | Branches, outlets, collection points, stock locations, processing locations, service areas | Tax/legal entity is not inferred from branch; ODR-002/003 |
| BR-CFG-003 | Shift/calendar | Morning/evening or named shifts, branch calendars, holidays, business-day cut-offs | Effective-dated; ODR-007 |
| BR-CFG-004 | Subscription recurrence | Daily, alternate-day, every N days, selected weekdays and validated calendar rules | Anchor, timezone, exception precedence and cut-off need ODR-006/007 |
| BR-CFG-005 | Subscription changes | Pause, resume, skip, substitution, quantity/product change, cancellation | Exact transition semantics require ODR-006 |
| BR-CFG-006 | Delivery | Route, worker assignment, proof/evidence requirement, attempt and exception reason | Must preserve attempts and actual quantities; ODR-019/020 |
| BR-CFG-007 | Procurement | Central/branch purchasing, PO requirement, receiving tolerances, supplier terms | Accepted/rejected quantities remain distinct |
| BR-CFG-008 | Milk quality | Test parameters, sample frequency, acceptance thresholds, rate bands | No universal formula; evidence and ODR-009 |
| BR-CFG-009 | Farmer settlement | Rate formula, quality incentives/deductions, period, advances and payment approval | Effective-dated and reproducible; ODR-010 |
| BR-CFG-010 | UOM and pack | Enabled units, precision, conversion, pack sizes | Conversion validation; ODR-008 |
| BR-CFG-011 | Inventory | Lot/expiry tracking, reservations, negative-stock exception, count cut-off, transfer approval | Ledger cannot be bypassed; ODR-011/012 |
| BR-CFG-012 | Pricing | Price lists, customer/branch/channel rates, effective date, discount/override limits | Historical price snapshot; ODR-013 |
| BR-CFG-013 | Billing | Monthly arrears or other approved cycle, cut-off, proration, statement grouping | Invoice/legal document classification verified; ODR-014/016 |
| BR-CFG-014 | Payment | Cash, UPI QR, direct bank, optional gateway; receiving account | Manual UPI reference remains unverified until evidence; ODR-015 |
| BR-CFG-015 | Accounting | Books scope, chart of accounts, voucher series, cost centres, approval thresholds | Cannot weaken double-entry or immutability; ODR-003/017 |
| BR-CFG-016 | GST/tax profile | Registration details and applicable document/tax configuration | No assumption based solely on branch or product name; ODR-016 |
| BR-CFG-017 | Workforce | Employee/contractor types, shifts, attendance, leave and pay components | Statutory applicability requires verified rules; ODR-018 |
| BR-CFG-018 | Authorization | Roles, scoped assignments, approval thresholds and delegation | Least privilege; ODR-004/005 |
| BR-CFG-019 | Analytics | KPI visibility, filters, report schedule and approved custom dimensions | Permission-aware; KPI definition versioned; ODR-022 |
| BR-CFG-020 | Forecast/budget | Horizon, scenario assumptions, dimensions, approval process | Forecast is not actual; ODR-023/024 |
| BR-CFG-021 | SaaS plan | Enabled features, quotas, grace, upgrade/downgrade policy | Entitlement never changes tenant isolation; ODR-025 |
| BR-CFG-022 | Localization | Enabled language, translated templates and locale per user/tenant | English primary; Indian language support; ODR-032 |
| BR-CFG-023 | Notifications | Channel, language, template, retry and provider routing | Consent, cost and delivery evidence; ODR-021 |

## 4. Extensibility contracts

| ID | Extension point | Required contract |
|---|---|---|
| BR-EXT-001 | Payment providers | Provider-neutral payment intent/confirmation/refund contract; provider evidence retained |
| BR-EXT-002 | Bank statement formats | Versioned import profiles, validation, row fingerprints and error reporting |
| BR-EXT-003 | Tally versions/adapters | Stable internal ledger contract, company-scoped mappings, version detection, preview and reconciliation |
| BR-EXT-004 | Notification providers | Channel adapter, delivery status, retry/backoff and failure isolation |
| BR-EXT-005 | Recurrence strategies | Validated recurrence interface; deterministic occurrence key and versioned rules |
| BR-EXT-006 | Quality/rate formula strategies | Versioned formula, input evidence, deterministic calculation and explainable output |
| BR-EXT-007 | Tax/statutory rules | Jurisdiction, source, effective date, version, applicability and review metadata |
| BR-EXT-008 | Reports/KPIs | Governed metric definition, data lineage, permissions, version and export contract |
| BR-EXT-009 | Forecast models | Model/version, training data cut-off, assumptions, validation metrics and forecast uncertainty |
| BR-EXT-010 | External integrations | Scoped credentials, idempotency, correlation IDs, retry and dead-letter/manual resolution |
| BR-EXT-011 | Client applications | Shared API contracts and server authorization; web/mobile clients do not own business rules |
| BR-EXT-012 | Workflow/approval strategies | Policy evaluation interface, actor scope, versioned approval evidence and immutable action history |

## 5. Configuration governance

Every configurable setting must define:
- Stable key, data type, allowed values/range and default (if approved).
- Scope: platform, tenant, branch, location, user, role, product or workflow.
- Owner/permission allowed to change it.
- Inheritance and override precedence.
- Effective-from/effective-to and timezone/business-date semantics where relevant.
- Validation and cross-setting constraints.
- Audit event and rollback/previous-version behavior.
- Whether changing it affects only future transactions or requires explicit migration.
- Plan entitlement dependency, if any.

No configuration may:
- Disable tenant isolation, authorization, audit or idempotency.
- Convert a draft into a posted transaction without required validations.
- Edit posted accounting or stock history.
- Mark unverified UPI as bank-confirmed.
- Allow negative stock, out-of-scope access or approval bypass unless a separately approved and bounded exception policy exists.
- Rewrite completed occurrences or finalized tax/price snapshots.

## 6. Decision boundaries

The following must remain Open until the ODR is explicitly resolved: legal entity/books ownership, remaining ODR-002 items (complete permission matrix and detailed shared-site accountability); grant-expiry event timing/zero-grace baseline is recommended in ODR-002 section 16.5; the Home-Branch/workforce-assignment separation is recommended in section 16.6; notification provider/channel configuration remains under ODR-021, membership and role matrix, recurrence precedence/cut-offs, UOM precision, quality/rate formulas, stock valuation/negative stock, price and billing policy, payment verification, GST applicability, accounting policy, payroll applicability, app packaging/offline behavior, notifications, KPI catalogue/forecast horizon/budget approvals, SaaS commercial rules, retention, capacity/recovery targets, integration priority, technology/deployment and localization rollout.

## 7. Approval

This catalogue classifies rules and proposes policy boundaries. It does not approve every candidate default. Product-owner approval and dependent-document updates are required before implementation.
