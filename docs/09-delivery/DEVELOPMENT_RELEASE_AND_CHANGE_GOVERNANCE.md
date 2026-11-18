# Development, Release and Change Governance

**Document ID:** DRC-001  
**Status:** Recommended delivery baseline selected under the product owner's delegated decision mandate — 2026-11-19  
**Authority:** Project Vision and Scope; Documentation Structure and Governance; FRS-001; NFR-001; ARC-PV-001; ARC-API-001; ARC-CONC-001; ARC-EVT-001; ARC-DR-001; ARC-OBS-001; QES-001; ODR-030/031/033/034.  
**Purpose:** Define a safe, repeatable development-to-production lifecycle once documentation-only work transitions to implementation.

## 1. Scope and governing principles

This document describes future engineering governance only. It does not authorize application code, physical database schema or infrastructure implementation during the current documentation-only phase.

The engineering lifecycle must preserve one product/release line, fixed security/data/financial invariants, Tenant-configurable policy and supported customization, versioned governed extensions and stable public contracts, reproducible builds/migrations/tests/releases/rollback, and no deployment that silently invalidates supported clients, extensions or customer configuration.

## 2. Repository and source-control workflow

When implementation is authorized:
- Protect the default branch; no unreviewed direct commits to production release branches.
- Use short-lived topic branches tied to a requirement/issue/ADR and change purpose.
- Require pull-request review, automated quality/security checks and traceable acceptance evidence before merge.
- Keep commits focused and descriptive; do not mix unrelated business/architecture changes.
- Require conflict resolution and fresh review if the target branch changes materially.
- Signed commits/tags and protected release artifacts SHOULD be used where supported by the chosen Git/hosting platform.
- Never commit secrets, private data, production exports or unapproved generated artifacts.
- Every change references affected FRS/BR/ODR/ADR/API/configuration/test documents.

The exact branching strategy and CI platform are tooling decisions under ODR-030/031; these controls are vendor-neutral.

## 3. Definition of Ready

A feature/change is ready for implementation only when it has:
1. Business purpose, scope, non-goals and affected operating profiles.
2. Requirements and acceptance cases, including negative/security cases.
3. Owning module/bounded context and data ownership.
4. Fixed/configurable/customizable/extensible classification.
5. Permission keys, scopes, approval/delegation, sensitive-data and audit behavior.
6. Configuration keys, defaults, allowed values, scope/precedence and migration impact where applicable.
7. API/event/extension contract and compatibility impact.
8. Transaction boundary, idempotency/concurrency/failure/recovery behavior.
9. Reporting, notification, localization and accessibility impacts.
10. Data lifecycle, retention and export/deletion impact.
11. Test strategy, test data and release evidence.
12. External/provider/legal/accounting dependency clearly classified and verified or safely deferred.

A missing high-impact business rule is not resolved by a developer silently selecting a convenient implementation.

## 4. Definition of Done

A feature is done only when:
- Acceptance tests pass at required layers and risk coverage is recorded.
- Permission checks cover every read, mutation, report, export, file, job, integration and alternate client path.
- Data ownership, state transition, concurrency, idempotency and historical snapshots are enforced and tested.
- API/event/configuration compatibility is updated and verified.
- Tenant isolation, sensitive data, audit, retention and observability controls are complete.
- Configuration/extension documentation and admin/operator instructions are updated.
- Migration, rollback/recovery and support runbooks exist where required.
- No unresolved blocker/critical defect remains; any allowed risk acceptance is named, scoped, time-bounded and approved by authorized human governance.
- RTM, module map, ODR/ADR and release notes are synchronized.

## 5. Continuous integration quality gates

A change pipeline SHOULD run formatting/static analysis/lint; type/build checks; unit/domain/application tests; persistence/integration tests; API/event contract validation and compatibility checks; authorization/Tenant isolation negative tests; dependency/secret/license/supply-chain scanning; migration up/down/compatibility checks in disposable environments; concurrency/idempotency tests for affected paths; targeted accessibility/localization checks; artifact provenance and reproducible build checks where supported.

Critical gate failure blocks merge/release. Flaky tests are investigated and quarantined with an owner and expiry; they are not silently ignored.

## 6. Environment and secret governance

Separate development, test, staging and production with distinct credentials, signing keys, storage locations, API credentials and data stores. No production secrets in developer machines, CI logs or test fixtures. Use synthetic or properly de-identified data outside production. Production access is just-in-time, permission-based, logged and limited to approved operational purpose. Configuration/secrets are environment-specific. Backups/restore tests use separate credentials and protected key custody.

## 7. Release and compatibility lifecycle

Each release has version/build identity and source commit; included requirements/issues/ADRs; API/event/configuration/extension compatibility matrix; migration and background-job compatibility notes; security/dependency advisories; test and recovery evidence; rollout, monitoring and rollback criteria; and user/operator release notes.

Use semantic versioning or a clearly documented equivalent for public contracts and extensions. Internal application versions need not equal public API/event/configuration versions.

Breaking API/event/configuration/extension changes require a new major contract version or explicit migration contract. Deprecation identifies consumers, offers replacement/migration instructions, emits telemetry, provides notice and uses controlled sunset. Never silently reinterpret a Tenant's saved configuration.

## 8. Database migration and data change governance

When database implementation is authorized:
- Migrations are version-controlled, ordered, repeatable where feasible and reviewed.
- Use expand-and-contract changes for large/live datasets and mixed application versions.
- A destructive migration requires verified backup, restore plan, data-impact analysis and explicit approval.
- Backfill jobs are idempotent, resumable, observable, Tenant-aware and rate-limited.
- Avoid long-running schema locks on critical transaction paths; prove timing with representative data.
- Never silently rewrite posted financial/stock/audit history or historical configuration snapshots.
- Data corrections use versioned migration/compensation records and reconciliation.
- Tenant-specific data movement between pool/bridge/silo placement retains identity, ownership, authorization, audit, file/object links, integrations and recovery evidence.

Physical schema, database engine and migration framework remain gated by ERS readiness and ODR-030/031.

## 9. Feature rollout, configuration and extension releases

Feature flags are for staged rollout, operational disablement or experiments; they are not authorization. Plan entitlements, Tenant activation, business configuration and principal permissions are evaluated separately.

Use progressive rollout (internal/test Tenant → pilot Tenants → measured cohort → broader release) with explicit eligibility/opt-in; tenant-safe telemetry; kill-switch scope; data migration/backfill compatibility; rollback behavior and no loss of accepted transactions; and no exposure of incomplete capability through client UI.

Extensions are published/revoked through their own review/version/permission lifecycle under ARC-PV-001 and ARC-IAM-CAT-001.

## 10. Deployment and rollback

Each release must be deployable with compatible application and worker versions, safe schema evolution, monitored health and rollback strategy. Use canary/rolling/blue-green rollout when topology supports it and the migration contract is compatible.

Rollback must not assume that application binary rollback reverses committed business transactions or schema changes. Use forward-fix or controlled data compensation when rollback is unsafe. Preserve outbox/inbox state, idempotency keys and external unknown outcomes; reconcile before replay.

## 11. Incident, hotfix and emergency change

Emergency fixes require incident severity, customer impact and containment; minimum-scope code/configuration change; independent review where feasible; automated security/integrity regression tests; auditable approval and release identity; post-deployment monitoring and rollback/forward-fix decision; post-incident review and permanent test/runbook update.

A production break-glass deployment account does not imply business data access or permission bypass. Operational and business permissions remain separate.

## 12. Change impact and architecture decision records

Any change affecting module boundaries, authoritative data ownership, permissions, isolation, contract versions, transaction semantics, configuration, retention, integrations or recovery requires a written impact analysis and ADR where material.

ADR fields: context/problem; decision/status; alternatives; rationale/evidence; consequences/trade-offs; security/data/scale/operational impact; compatibility/migration/rollback; verification; superseded/revisit trigger.

Decision classifications distinguish user-confirmed, delegated engineering baseline, recommendation, external evidence dependency and owner approval required. Do not convert a recommended default into an approved business/legal rule by wording alone.

## 13. Operational readiness and support

Before release, define service health indicators/alerts; Tenant-aware logs/traces and audit separation; queue/reconciliation dashboards; backup/restore and incident runbooks; support escalation and data-access request process; release notes/known limitations/customer communication; capacity/quota monitoring and Tenant fairness; dependency/provider status and integration replay procedures.

Named human incident, deployment, restore and key-custody owners are production governance requirements; software must not infer them from job titles.

## 14. Change-control checklist

- Scope and requirement IDs identified.
- No code/document decision conflicts.
- Domain owner/data ownership preserved.
- Permissions, scope, audit and sensitive-data access updated.
- Configuration/extension/API/event compatibility reviewed.
- Concurrency, idempotency and historical integrity tests included where relevant.
- Security and Tenant isolation negative tests pass.
- NFR/performance/fairness impact checked.
- Data migration/recovery plan reviewed.
- Documentation, RTM, ODR/ADR and release notes synchronized.
- Human approvals for external/legal/business risk recorded.

## 15. Related documents

ARC-PV-001; ARC-IAM-001; ARC-IAM-CAT-001; ARC-API-001; ARC-CONC-001; ARC-EVT-001; ARC-DR-001; ARC-SCALE-001; ARC-SEC-001; ARC-OBS-001; QES-001; NFR-001; RTM-001; ODR.
