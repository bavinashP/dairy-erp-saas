# Quality Engineering and Acceptance Test Strategy

**Document ID:** QES-001  
**Status:** Recommended engineering baseline selected under the product owner's delegated decision mandate — 2026-11-19  
**Authority:** Project Vision and Scope; FRS-001; NFR-001; ERS-001; RTM-001; Business Rules Catalogue; ARC-PV-001; ARC-IAM-001; ARC-IAM-CAT-001; ARC-AUTH-MATRIX-001; ARC-CONC-001; ARC-API-001; ARC-SEC-001; ARC-OBS-001; ARC-SCALE-001; ODR-001..036.  
**Purpose:** Define quality, testability and release acceptance for one comprehensive Dairy ERP SaaS product serving small owner-operated to large enterprise workloads.

## 1. Quality objectives

Quality is a product property, not a final phase. Every feature, configurable policy, module, integration, report, extension and release must be assessed for business correctness; small/medium/large operating-profile fit; Tenant isolation and permission-first authorization; data ownership, historical integrity, accounting and stock correctness; concurrency, idempotency and safe recovery; API/client/extension/configuration compatibility; performance, availability, scalability, tenant fairness and resource cost; security, privacy, auditability and operational support; accessibility, localization and upgrade safety.

Tests must verify both permitted behavior and prohibited behavior. A workflow that succeeds for the intended user is not accepted until unauthorized, stale, duplicated, conflicting and failure-path cases are also tested.

## 2. Requirements and test traceability

Every requirement and material decision has a stable ID and must link to:
1. Domain workflow/business rule.
2. Owning module and data ownership boundary.
3. Permission keys and scopes.
4. API/command/query/event contract where applicable.
5. Configuration/entitlement/extension dependencies.
6. Positive and negative acceptance scenarios.
7. Test level and automation/evidence owner.
8. Release gate and operational monitoring where applicable.

RTM-001 is the authoritative traceability register. A requirement cannot be marked verified based only on prose or screenshot; evidence must identify test version, environment, data profile, execution date, expected outcome and actual result.

## 3. Test layers

Use a balanced test portfolio. Do not maximize unit-test count while leaving integration, security, concurrency or end-to-end risks untested.

| Test layer | Primary responsibility | Examples |
|---|---|---|
| Domain/unit | Pure calculations, state transitions, invariants | Milk quantity conversion, rate chart, pricing precedence, tax calculation fixture, billing eligibility, stock availability |
| Application/service | Use-case orchestration, permission checks, transaction boundaries | Receive stock, approve settlement, issue invoice, close period, create grant |
| Persistence/integration | Constraints, locking, transactions, outbox/inbox, storage adapters | Tenant-safe references, unique keys, duplicate imports, atomic stock posting |
| API/contract | Request/response/event compatibility and error semantics | OpenAPI validation, API client compatibility, webhook event schemas |
| Cross-module workflow | Complete business transaction chains | Collection-to-settlement, subscription-to-cash, procurement-to-payable, production-to-finished stock |
| End-to-end/UI | User journeys across web/mobile/POS | Owner-only retail, branch delivery, multi-site processing, finance close |
| Security/authorization | Threat paths and denied operations | Tenant isolation, IDOR, Branch scope, sensitive fields, role/account-label neutrality |
| Concurrency/resilience | Race conditions, duplicates, failure, retry, recovery | Grant revoke vs stock commit, double settlement, stale update, provider timeout |
| Performance/scale | Capacity, fairness, overload and resource isolation | Peak POS, billing jobs, tenant noisy-neighbour, report pressure |
| Migration/recovery | Data/configuration evolution | Upgrade, rollback, restore, tenant isolation-tier move, Tally import reconciliation |
| Accessibility/localization | Inclusive operation and language/format integrity | Keyboard workflow, screen-reader labels, Marathi/Hindi Unicode round trip |

## 4. Required Tenant and operating profiles

Maintain representative synthetic datasets for:

- **Solo/small:** one user, one Branch, one OperatingLocation, no employees, no GST assumptions, basic retail or milk delivery, manual payments, simple stock.
- **Small combined operator:** collection/procurement plus retail/delivery, few products, basic quality capture and simple accounting.
- **Growing multi-Branch:** multiple Branches/ServiceAreas/Routes, shared physical site, independent StockLocations, employees, approvals, branch reporting and cross-Branch grants.
- **Processing dairy:** collection lots, quality holds, process/recipe versions, production batches, yield/loss, packaged finished goods and traceability.
- **Enterprise/multi-site:** many users/devices/locations, high transaction volume, central and Branch processes, delegated approvals, integration-heavy workflows, advanced analytics and isolation-tier needs.
- **Adverse/hostile:** duplicate/replayed requests, revoked users, stale devices, malformed imports, conflicting permissions, provider outage, quota saturation, cross-Tenant ID substitution and compromised extension credentials.

Use synthetic or properly de-identified data. Production personal/financial records must not be copied into non-production without an approved privacy-protective process.

## 5. Mandatory security and authorization test suites

### 5.1 Tenant and membership isolation
- Every Tenant-owned resource, relation, search index, file, cache, report, export, job, event, analytics row and integration callback is Tenant-bound.
- Multiple memberships of one global user are independent. Revoking Tenant A does not revoke Tenant B membership; Tenant B does not enable Tenant A access.
- Tenant ID supplied by a client is never accepted as authority.
- Platform identities and service identities have no default Tenant data access.

### 5.2 Permission-first behavior
- Role/job title, seniority, account type, client type and Home/Primary Branch changes alone must not alter authorization.
- Same effective permissions/context produce same outcome; identical role labels with different permissions produce different outcomes as configured.
- Tenant custom bundles can grant/revoke eligible permissions only within the assigning principal's delegation ceiling.
- Explicit deny overrides allow; ambiguity/missing policy fails closed.
- Read/list/search/export/sensitive-field access and each mutation/approval/post/reverse/configure action are tested independently.
- Feature entitlement, activation, configuration, feature flags and extension installation never grant permissions.
- Test every alternate entry point: UI, API, bulk endpoints, mobile/offline sync, POS, barcode, search, reports, exports/downloads, files, jobs, webhooks and third-party adapters.

### 5.3 Sensitive-data boundaries
Test masking/field permissions for bank/payee details, identity documents, payroll, compensation, tax identifiers, customer personal data, payment evidence, secrets and audit investigation records. Export permission must not bypass field permissions.

### 5.4 Delegation and break-glass
Test the ODR-002 Delegated Approval constraints, 30-day maximum, scope/limit inheritance, invalidation, non-delegable actions, self-approval and revoke races. Test Emergency Override qualification, exact actor/resource/action scope, 60-minute initial/120-minute total maximum, independent extension, immutable audit and retrospective review.

## 6. Financial, quantity and stock correctness

Mandatory invariants:
- Every posted journal balances in the accounting currency/scale.
- Posted journal, invoice, payment, stock movement and finalized payroll facts are not silently overwritten.
- Reversals/corrections link to the original and preserve history.
- Retry cannot duplicate one business effect.
- Partial payment, allocation, refund and reconciliation cannot exceed available/unsettled source values.
- Quantity dimensions cannot be mixed; unit conversions preserve source value, conversion evidence/version and precision.
- Measurement tolerance is distinct from rounding.
- Stock ownership/Branch/StockLocation/lot/status remains attributable.
- Reserved, in-transit, quarantined, expired, damaged and available quantities remain distinct.
- Count capture cannot approve variance; quarantine cannot release stock; receipt cannot imply adjustment.
- Negative stock default-deny and approved exceptions are tested against product tracking profile and integrity constraints.
- Historical price, UOM, tax, rate, schedule, recipe, permission/policy and configuration snapshots reproduce the original transaction outcome.
- Tally import/export detects lossy precision, duplicate voucher, unsupported mapping and unknown external outcomes.

Use property-based or generated boundary tests for money, quantities, conversions, rounding, balance equations, reservation allocation and idempotency where useful. Avoid binary floating-point as an authoritative source.

### 6.1 Physical custody and shared-resource assurance

Test the selected custody assurance profile at each applicable operating scale:
- **AT-CST-001:** Small owner-operated workflow completes a basic single-party capture without requiring a second employee account or enterprise-only setup.
- **AT-CST-002:** Acknowledged cross-Branch/carrier handover requires the actual receiving party's acknowledgement under the selected profile; notification delivery and timeout never imply acceptance.
- **AT-CST-003:** Partial receipt records accepted and outstanding quantities separately; a rejected shipment preserves the actual custodian and known location.
- **AT-CST-004:** A custody-only event does not create a stock movement or financial posting; an actual stock location/quantity change records a separate linked movement.
- **AT-CST-005:** A missing handover response, offline device or duplicate event never creates a custody gap or phantom acceptance.
- **AT-CST-006:** Raw milk/perishable profile captures configured timestamps, container/lot, required temperature/condition and quality evidence; invalid/missing/out-of-range evidence follows the configured hold/review policy.
- **AT-CST-007:** Sensor/device readings retain device identity, source timestamp, calibration/reference and raw value; unverified/manual readings are not misrepresented as sensor-confirmed.
- **AT-CST-008:** Shared equipment use across Branches preserves asset ownership, permitted-use scope, custody, booking, cleaning/calibration/maintenance and any separately approved cost allocation.
- **AT-CST-009:** A stock-task grant expiring while stock is physically held blocks further stock mutation but preserves the last verified custodian/location until a real handover or incident resolution.
- **AT-CST-010:** Custody permissions, evidence attachments and Branch-scoped data are tested across web, API, mobile/barcode, reports, exports, jobs and integrations.
- **AT-CST-011:** A custody incident or discrepancy never automatically creates a write-off, supplier deduction, payroll deduction, liability finding or financial posting.
- **AT-CST-012:** Historical custody output reproduces the profile/configuration version and evidence available at the time.

## 7. Concurrency, idempotency and failure testing

For each critical write path, test:
- Same request repeated concurrently and sequentially.
- Same idempotency key with different semantic payload.
- Two users updating the same record/version.
- Concurrent stock issue/reservation/count/posting.
- Grant/permission expiry or revocation racing with an uncommitted operation.
- Permission scope change while a background job is queued.
- Subscription edit racing with occurrence generation.
- Duplicate bank/Tally import and webhook replay.
- Provider timeout after unknown acceptance, followed by reconciliation.
- Worker restart/process crash and retry around outbox/inbox acknowledgement.
- Partial batch success and per-item recovery.
- Closed period/posted transaction correction.
- Clock skew and effective-date boundaries.

Tests must prove safe outcomes, not assume distributed exactly-once delivery or automatic rollback.

## 8. API, event and extension compatibility

- Validate API contracts against reviewed OpenAPI sources.
- Test supported client versions and additive/breaking changes, including enum expansion, stricter validation, default changes and precision changes.
- Validate event schema versions, event identity, Tenant binding, correlation/causation and replay handling.
- Test old supported mobile clients and Tally desktop agents against backend releases.
- Test extension installation with missing permissions, incompatible version, expired credentials, excessive scope, replay, timeout and provider outage.
- Ensure an extension cannot write directly into another module's private persistence.
- Deprecation tests verify migration, alias/adapter, warning telemetry, sunset policy and no silent broadening of permission.

## 9. Configuration and customization testing

For every configuration key or supported customization:
- Type, range, unit, default and scope are valid.
- Only declared Tenant/Branch/site scopes can override.
- Locked parent policies cannot be weakened.
- Fixed invariants cannot be disabled.
- Invalid dependency combinations block publication.
- Draft, preview/simulation, approval, publish and rollback behave safely.
- Concurrent edits detect stale versions.
- Historical transactions retain the configuration snapshot used.
- Failed configuration publish leaves the previous valid version active.
- Customer custom fields/templates/reports remain Tenant-isolated and permission-controlled.
- Upgrade preserves values or provides an explicit migration with a safe fallback.

Test feature entitlement, capability activation, permission, configuration and extension grant as separate dimensions.

## 10. Mobile/offline and device testing

- Offline cache contains only minimum task data and expires under the approved policy.
- Revoked/expired permissions/grants cannot be used at server synchronization.
- Offline commands preserve original actor/device/occurred-at/correlation references and are server-revalidated.
- Duplicate sync batches do not duplicate delivery/stock/financial effects.
- Partial sync exposes per-command outcome; no silent loss.
- App restart, upgrade, device clock change, storage exhaustion, lost device and account sign-out are tested.
- Offline POS/stock posting/accounting/payment settlement remain disabled unless a separate approved workflow-specific gate is completed.

## 11. Performance, scale and Tenant fairness

Use conservative, expected and stress workload envelopes defined by ARC-SCALE-001 and ODR-027. No numeric targets are inferred from development VPS capacity.

Measure API latency percentiles by operation class; throughput/concurrent writes for POS, collection, billing, procurement, stock and accounting; queue age/throughput/retries/dead letters; report/export/forecast resource isolation; database connection/storage saturation; object upload/download/restore throughput; Tenant usage quotas/noisy-neighbour/fair scheduling; scale-out, deploy/rollback and tenant migration between pool/bridge/silo tiers.

Test overload, backpressure, queue saturation and provider outage while proving accepted money/stock transactions are not silently lost.

## 12. Accessibility, localization and usability

- Core tasks should be keyboard operable with visible focus and meaningful labels.
- Validation and errors must be understandable and associated with the relevant field/action.
- Sensitive data is not disclosed by screen-reader labels, notifications or screenshots unintentionally.
- English, Marathi and Hindi resources must support Unicode names, addresses, documents and templates.
- INR, Indian number/date presentation and IST business-date boundaries are tested separately from stored values.
- Small owner-operated journeys must not require employee creation or enterprise-only setup; large Tenants must still have advanced controls without separate product forks.

## 13. Test data, environments and reproducibility

Maintain local developer, shared integration, staging/release-candidate, load/performance and isolated recovery/restore environments. Each environment has separate credentials, data stores, storage locations and service identities. Use synthetic or de-identified data outside production.

Every test report records commit/build, contract/schema/configuration versions, environment, dataset/profile, relevant entitlements/permissions, tool versions, execution time, expected result, actual result and failures. Secrets must be synthetic or ephemeral.

## 14. Defect classification and release gates

Severity is determined by impact:
- **Blocker/Critical:** cross-Tenant disclosure, privilege escalation, financial/stock corruption, unauthorized posting, lost accepted transactions, unrecoverable data loss, secret exposure or unsafe statutory output.
- **High:** material workflow failure, duplicate money/stock effect, audit evidence loss, broken recovery, broad client incompatibility or major Tenant unfairness.
- **Medium:** important workflow degradation with safe workaround and no integrity/security compromise.
- **Low:** cosmetic or low-impact usability issue with no data/security/integrity effect.

Release gate:
1. All applicable fixed invariants and security-negative tests pass.
2. Critical financial, stock, payment, delivery and concurrency cases pass.
3. No unresolved critical/high defect without documented, time-bounded risk acceptance by authorized human governance.
4. API/event/configuration/extension compatibility checks pass.
5. Migration/rollback and backup/restore rehearsal pass for the release risk level.
6. Performance and noisy-neighbour results meet approved workload envelope.
7. Monitoring, alert ownership and rollback/runbook readiness exist.
8. Requirements traceability and acceptance evidence are updated.
9. Documentation and user/operator change notes are complete.
10. Release approver identity and decision are auditable.

No universal code-coverage percentage substitutes for risk-based release evidence. Domain teams may set coverage thresholds after test maturity and technology selection; critical business invariants require explicit tests.

## 15. Ownership and improvement

Each module has an accountable product/domain owner function and a technical test owner assigned by the delivery organization. These are operational responsibilities, not automatic application permissions. Permission to approve a release, waive a test or accept risk must be explicitly assigned and audited.

After incidents, escaped defects or major releases, update tests, requirements, runbooks and architecture decisions so the same failure class is prevented. Repeated manual regression should be automated where economically reasonable.

## 16. Related authoritative documents

FRS-001; NFR-001; ERS-001; RTM-001; ODR-002/004/005/008/015/020/027/028/033–036; ARC-PV-001; ARC-IAM-001; ARC-IAM-CAT-001; ARC-AUTH-MATRIX-001; ARC-CONC-001; ARC-API-001; ARC-EVT-001; ARC-MOB-001; ARC-SCALE-001; ARC-SEC-001; ARC-OBS-001; ARC-DR-001.
