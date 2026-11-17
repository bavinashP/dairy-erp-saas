# Security Threat Model and Security Architecture

**Document ID:** ARC-SEC-001  
**Status:** Proposed security baseline — risk acceptance and policy choices open  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, ARC-TEN-001, ARC-IAM-001, ARC-API-001, ARC-EVT-001, ARC-STO-001, NFR-001, ODR-004/005/019/020/025/026/028/030/031/033.  
**Purpose:** Identify the platform's trust boundaries, valuable assets, credible threats, mandatory security controls and verification expectations without selecting a framework, cloud provider or final legal interpretation.

## 1. Scope and security objectives

This threat model applies to platform administration, tenant workspaces, branches/locations, web and mobile clients, APIs, background workers, files/object storage, integrations, analytics, support operations, deployment and recovery.

Security objectives, in priority order:
1. Prevent cross-tenant and unauthorized cross-branch disclosure or mutation.
2. Prevent account takeover, privilege escalation and unauthorized platform/support access.
3. Preserve integrity of money, stock, procurement, delivery, subscription and accounting records.
4. Protect personal, financial, employee, supplier/farmer and business-confidential information.
5. Maintain availability and recoverability against abuse, provider failure, ransomware and operator error.
6. Preserve reliable evidence for investigation, dispute resolution and accountability.
7. Make secure operation practical for small owner-operated dairies without weakening fixed platform controls.

This document is a design baseline, not a penetration-test report, certification, legal opinion or claim of compliance.

## 2. Security invariants

The following controls are non-configurable by tenants:
- Every protected operation authenticates an attributable user or service identity and authorizes the exact action, resource, tenant, branch/location and relevant state on the server.
- Tenant context is derived from validated membership/session or an explicit, scoped platform grant; client-supplied tenant IDs are never proof of authority.
- Missing, invalid, suspended, expired or ambiguous security context fails closed.
- Tenant isolation applies to APIs, database access, caches, search, reports, exports, files, events, jobs, webhooks, integrations and operational tooling.
- Branch authorization is evaluated independently from tenant authorization.
- A role, device type, feature entitlement or unguessable identifier is not itself authorization.
- Platform administration does not implicitly grant access to tenant business data.
- Support access is explicit, scoped, justified, time-bounded where feasible, revocable and audited.
- Secrets are never placed in source control, client bundles, URLs, ordinary logs or unprotected backup metadata.
- Posted financial and stock effects are corrected through controlled reversal/adjustment workflows, not silent mutation.
- Security controls and evidence collection must not depend solely on client-side checks.
- Tenant configuration cannot disable authentication, authorization, isolation, audit, integrity, rate protection or recovery controls.

## 3. Assets and security classification

| Asset | Examples | Primary security concern |
|---|---|---|
| Identity credentials | Password hashes, OTP/recovery factors, tokens, device credentials | Account takeover and impersonation |
| Authorization state | Memberships, roles, permissions, scopes, approvals, support grants | Privilege escalation and scope bypass |
| Tenant business data | Customers, subscriptions, procurement, stock, invoices, accounting | Confidentiality and integrity |
| Personal/sensitive data | Phone/address, employee/payroll, bank/payment details, farmer records | Privacy, misuse and unauthorized disclosure |
| Financial evidence | Receipts, payment references, journals, reconciliation records | Fraud, repudiation and financial loss |
| Operational records | Delivery runs, collection, quality, batch/lot and inventory movements | Safety, traceability and business continuity |
| Documents and exports | Invoice PDFs, reports, Tally packages, attachments | Data leakage, malware and tampering |
| Integration secrets | Tally agent credentials, API keys, webhook secrets, provider tokens | Lateral movement and fraudulent operations |
| Platform control plane | Plans, tenant status, feature entitlements, configuration | Platform-wide compromise |
| Audit/security evidence | Authentication events, access logs, approvals, incident evidence | Detection, accountability and forensic integrity |
| Backups and recovery keys | Database/object backups, manifests, encryption key recovery | Ransomware resilience and recoverability |
| Software supply chain | Source, dependencies, build artifacts, deployment credentials | Malicious or vulnerable code execution |

Apply data minimization and field-level access according to actual business purpose. Do not assume every tenant collects every sensitive data class.

## 4. Trust boundaries and attack surfaces

| Boundary | Untrusted input / exposure | Required control family |
|---|---|---|
| Web/mobile/POS client → API | User input, IDs, filters, tokens, file uploads | Authentication, validation, authorization, rate limits |
| API → tenant data store | Tenant/branch context, query predicates, ORM scopes | Fail-closed context, ownership constraints, isolation tests |
| API → cache/search/reporting | Cache keys, index filters, aggregate queries | Scope-aware keys and query-time authorization |
| API → object storage | Object keys, signed URLs, file metadata | Server-issued scoped access, opaque keys, malware checks |
| API → background queue | Job payloads, retries, replay | Authenticated producer, explicit tenant scope, idempotency |
| Platform operator → tenant workspace | Support and emergency access | Separate grants, approval, expiry, session recording/audit |
| Tenant → external integration | Webhooks, imported files, API callbacks | Signature/authentication, schema validation, replay protection |
| Worker → external provider | Credentials, timeouts, ambiguous outcomes | Least privilege, secret vault, retry/reconciliation |
| Build/deploy → production | Dependencies, artifacts, CI credentials | Protected pipeline, review, provenance and restricted secrets |
| Backup system → archive provider | Backup data, retention and delete operations | Separate credentials, encryption, immutability, restore tests |
| Tenant → tenant | Shared infrastructure and identifiers | Tenant ownership checks at every read/write boundary |

Internet-facing attack surfaces include authentication/recovery endpoints, public API routes, upload/download endpoints, webhooks, integration callbacks, invitation flows and any future public status or marketing endpoints. Internal attack surfaces include admin consoles, job dashboards, support tools, database consoles, deployment pipelines and backup management.

## 5. Threat analysis method and risk treatment

Use a repeatable threat-analysis method for each major feature and material change:
1. Identify the asset, owner, data classification and business impact.
2. Draw the data flow and trust boundaries, including asynchronous and third-party paths.
3. Identify attacker capability and entry point; consider external users, compromised tenant accounts, malicious insiders, compromised dependencies and cloud-account compromise.
4. Describe the abuse path and security property at risk.
5. Rate likelihood and impact using a documented risk scale; record uncertainty and evidence.
6. Select preventive, detective, responsive and recovery controls.
7. Define negative tests and operational evidence.
8. Assign an owner and disposition: mitigate, avoid, transfer or explicitly accept.
9. Reassess after architecture, provider, data-flow or threat changes.

No risk is accepted merely because the feature is not yet in production. Unmitigated critical/high risks block the relevant production release unless a documented, time-bounded exception is explicitly approved by the accountable owner.

## 6. Threat register

| ID | Threat / abuse case | Impact | Required controls and verification |
|---|---|---|---|
| SEC-001 | Tenant ID, resource ID or filter substitution exposes another tenant's records | Critical confidentiality breach | Resolve resources inside validated tenant context; database constraints and cross-tenant negative tests for every resource family |
| SEC-002 | Branch ID substitution or stale branch assignment bypasses branch scope | High/critical data exposure or unauthorized operations | Validate branch ownership and actor assignment on every operation; cross-branch read/write tests |
| SEC-003 | IDOR through invoice, customer, file, report or export identifiers | High | Object-level authorization, non-disclosing not-found behavior, identifier-substitution tests |
| SEC-004 | Role/permission assignment, delegation or approval workflow enables privilege escalation | Critical | Delegation ceiling, separation of duties, self-approval prevention, effective-date audit and privilege-escalation tests |
| SEC-005 | Platform operator uses implicit global access or support bypass | Critical | Separate platform/tenant authority; explicit scoped support grant; expiry/revocation; audited break-glass review |
| SEC-006 | Credential stuffing, brute force, OTP abuse or account recovery takeover | High | Risk-based throttling, abuse detection, secure recovery, MFA policy decision, notification and session revocation tests |
| SEC-007 | Stolen/long-lived token or lost device continues accessing data | High | Short-lived access, protected refresh rotation, device/session inventory, revocation and re-authentication tests |
| SEC-008 | CSRF, CORS misconfiguration or token leakage enables cross-site action | High | Client-appropriate CSRF controls, strict CORS allowlist, secure cookie attributes, no secrets in URLs, browser security tests |
| SEC-009 | SQL/NoSQL injection, unsafe query construction or mass assignment alters data | Critical | Parameterized queries, allowlisted writable fields, domain validation, least-privileged DB identity, injection tests |
| SEC-010 | XSS or unsafe document rendering steals sessions or performs actions | High | Contextual output encoding, sanitization, restrictive content security policy where applicable, safe file serving and security tests |
| SEC-011 | Malicious upload, polyglot file, oversized archive or parser exploit compromises service | High | Allowlisted types, size/count limits, content inspection, quarantine and malware scanning, isolated processing, decompression limits |
| SEC-012 | SSRF through URL import, webhook test, document fetch or integration configuration | High | Egress allowlist, block private/link-local/metadata ranges, DNS/IP revalidation, redirect restrictions and SSRF tests |
| SEC-013 | Forged/replayed webhook or callback causes duplicate or fraudulent business effects | High | Signature verification, timestamp/nonce or provider event ID, replay window, idempotency, tenant-bound secret and reconciliation |
| SEC-014 | Background job payload is tampered, replayed or processed under ambient tenant context | Critical | Explicit tenant/scope, service identity, schema validation, authorization recheck, idempotent consumer and cross-tenant worker tests |
| SEC-015 | Signed file URL is leaked, over-scoped or remains usable after access revocation | High | Very short expiry, one object/operation, no listing, minimal content disposition, authorization at issuance, revocation/expiry risk assessment |
| SEC-016 | Cache/search/report artifact returns data after permission or tenant context changes | Critical | Tenant/scope/policy-version cache keys, invalidation, query-time checks, export access recheck and stale-permission tests |
| SEC-017 | Secrets leak through source, logs, crash dumps, CI output or backups | Critical | Secret manager, scanning, redaction, rotation/revocation, restricted debug data and incident drill |
| SEC-018 | Ransomware/operator compromise deletes or encrypts production and backups | Critical | Independent backup identity/account, immutable generations, offline/isolated copy, key separation, restore drills |
| SEC-019 | Dependency, build pipeline or deployment credential is compromised | Critical | Dependency inventory/scanning, pinned/reviewed updates, protected branches, least-privilege CI, artifact integrity and rollback |
| SEC-020 | Resource exhaustion/API abuse creates denial of service or disproportionate provider cost | High | Per-identity/tenant/IP rate limits, concurrency and payload bounds, queue backpressure, quotas, anomaly alerts and load/abuse tests |
| SEC-021 | Financial or inventory transaction is raced, duplicated or silently altered | Critical | Transactional invariants, locks/constraints, idempotency, immutable history, reversal workflow and deterministic concurrency tests |
| SEC-022 | Audit logs are modified, deleted, overexposed or contain secrets | High | Append-oriented protected storage, restricted access, integrity monitoring, redaction, retention policy and tamper-evidence tests |
| SEC-023 | Sensitive fields are exposed through broad reports, exports, notifications or support tooling | High | Field-level permissions, masking, export scopes, recipient verification, download audit and data-loss tests |
| SEC-024 | Provider outage, region failure or account suspension makes records unavailable | High | Provider-neutral abstraction, documented fallback/degraded behavior, independent backups, exportability and recovery exercise |
| SEC-025 | Tenant offboarding or retention automation deletes required records or retains personal data indefinitely | High | Approved data-class policy, legal hold, deletion workflow, version/replica/backup analysis and deletion evidence |
| SEC-026 | Malicious insider or compromised tenant owner abuses legitimate permissions | High | Least privilege, approval for high-risk actions, alerts, immutable business history, export controls and anomaly review |

Risk severity is an initial qualitative assessment, not a quantified risk acceptance. Re-score with actual architecture, threat intelligence, tenant profile and impact evidence.

## 7. Identity, authentication and session controls

ARC-IAM-001 remains the authority for identity and authorization semantics. Security architecture adds these requirements:
- Use a vetted password-hashing scheme and parameters supported by the selected identity technology; never reversible encryption for passwords.
- Decide MFA requirements by risk, especially platform operators, privileged tenant roles, support access and recovery changes. Do not claim MFA is enabled until implemented and tested.
- Protect login, OTP, invitation and recovery flows against enumeration, automation, replay and delivery abuse.
- Define access-token lifetime, refresh rotation/reuse detection, session inventory, device revocation and concurrent-session policy before implementation.
- Secure browser sessions with appropriate HttpOnly, Secure and SameSite cookie controls where cookie sessions are used; define CSRF protections for state-changing requests.
- Native/mobile secrets must use platform secure storage; do not embed shared privileged secrets in distributed application binaries.
- Re-authenticate or step-up authenticate for high-risk changes such as changing MFA/recovery factors, granting privileged access, exporting sensitive data or changing integration credentials.
- Session revocation and membership/permission changes must take effect within an approved, tested policy window.
- Account recovery must not be weaker than normal authentication and must notify the account owner through an independent channel where feasible.

## 8. Authorization, tenant isolation and business integrity

- Enforce authorization at the service/domain boundary and data-access boundary, not only in controllers or UI.
- Resolve tenant membership, branch assignment, role, permission, resource ownership, resource state and approval policy for each operation.
- Use deny-by-default policy for unknown resources/actions and missing scope.
- Avoid global resource lookup followed by UI filtering. Queries must be scoped before data is returned or mutated.
- Protect bulk operations, nested resources, report filters, exports, search, autocomplete and aggregate endpoints with the same rules as detail endpoints.
- Ensure caches and materialized analytics preserve tenant, branch, permission and data-cutoff scope.
- High-risk financial, stock, procurement and settlement actions require immutable event/history references and explicit reversals/adjustments.
- Apply concurrency controls and idempotency to prevent duplicate charges, receipts, settlement, stock movement, schedule generation and integration replay.
- Tenant plan entitlements govern availability/limits only; they never replace authorization.

## 9. API, browser and network protections

- Validate all input using explicit schemas, length/range limits, enum allowlists and domain invariants.
- Apply rate limits by multiple relevant dimensions: IP/network, account/principal, tenant, API key/service identity and expensive operation class.
- Use request size, pagination, timeout, concurrency and query-cost bounds.
- Return safe errors without stack traces, secrets, SQL details, internal paths or cross-tenant existence signals.
- Configure CORS only for known client origins; do not use wildcard origins with credentials.
- Use CSRF protection for cookie-authenticated state-changing requests.
- Apply appropriate security headers and content security policy to browser surfaces.
- Restrict outbound network access for URL-fetching features and integrations; prevent access to cloud metadata, loopback, private and link-local destinations unless explicitly required and safely mediated.
- Enforce TLS for client, service and provider communication; evaluate modern protocol/cipher configuration during deployment review.
- Keep administrative interfaces off public routes where feasible and require stronger authentication and monitoring.

## 10. File, object storage and export security

ARC-STO-001 defines storage lifecycle and provider abstraction. In addition:
- Treat every upload as untrusted until validation and scanning complete; quarantine before availability where risk warrants.
- Validate actual content signatures as well as declared MIME type and extension. Generate server-side object names.
- Apply file size, archive expansion, page count, image dimensions and processing-time limits appropriate to each file class.
- Process risky formats in an isolated, resource-limited worker. Do not execute uploaded content.
- Store documents privately by default; public access requires an explicit reviewed use case.
- Signed URLs must be narrowly scoped, short-lived and unguessable; do not treat them as a substitute for authorization.
- Avoid inline rendering of active content from the application's authenticated origin. Use safe download headers or an isolated content origin.
- Recheck authorization when a report/export is downloaded; exports must record creator, tenant, branch scope, filters, data cutoff and policy version.
- Apply retention, legal hold and deletion rules to originals, versions, thumbnails, generated PDFs, temporary exports and replicas.
- Malware scanning is a control with false negatives/positives; preserve scan status and quarantine/review workflow.

## 11. Integration, webhook and Tally-agent security

- Each integration connection belongs to one tenant and has explicit capabilities, scopes, credential ownership and lifecycle.
- Store integration credentials in a dedicated secret-management boundary; encrypt at rest and restrict retrieval to the minimum service.
- Verify webhook authenticity and payload integrity before processing; use provider event IDs, timestamps and idempotency to prevent replay/duplicate effects.
- Validate schemas and size limits; treat external payloads and files as untrusted.
- Never trust tenant IDs or branch IDs asserted by an external callback without resolving the authenticated connection mapping.
- Outbound requests must use destination allowlists where feasible and SSRF-safe resolution.
- Tally desktop synchronization credentials must be tenant-bound, revocable and constrained to approved synchronization operations; local agent trust and machine compromise require separate threat review.
- Record request/correlation IDs and reconciliation state without logging credentials or unnecessary personal/financial payloads.
- Unknown provider outcomes must be reconciled before retrying operations that could create duplicate financial or stock effects.

## 12. Secrets, encryption and key management

- Separate application secrets, integration credentials, signing keys, storage credentials, database credentials and backup encryption keys.
- Use a managed secret store or an equivalently protected deployment secret mechanism; never commit production secrets.
- Restrict secret read access to named services and operators; log access metadata without logging values.
- Define generation, rotation, expiry, revocation, compromise response and emergency replacement for every secret class.
- Encrypt sensitive data in transit and at rest; select algorithms and key-management technology during architecture evaluation rather than inventing cryptographic primitives.
- For backup classes requiring independent control, encrypt before upload and test key recovery independently.
- Keep key recovery material separate from the data it decrypts, with controlled multi-person recovery where justified.
- Define encryption key rotation and historical backup decryptability; rotation must not make retained backups unrecoverable.
- Redact secrets and sensitive payloads from logs, traces, metrics, support bundles and error reports.

## 13. Audit, detection and security monitoring

ARC-IAM-001 and NFR-001 define audit semantics. Security events should include:
- Authentication failures/success patterns, recovery and MFA changes.
- Session/token/device creation, refresh anomalies and revocation.
- Membership, role, permission, branch scope, delegation and support-grant changes.
- Repeated authorization denials, cross-tenant/branch access attempts and suspicious identifier enumeration.
- Bulk exports, sensitive downloads, unusual data access and high-risk financial/stock operations.
- Integration credential changes, webhook signature failures, replay attempts and unusual sync activity.
- Rate-limit events, upload scanning detections, suspicious outbound requests and resource exhaustion.
- Backup failures, deletion attempts, retention-policy changes, key-management events and restore outcomes.
- Deployment, privileged configuration and security-control changes.

Events must have reliable timestamps, actor/service, tenant/security boundary, action, target reference, outcome, source/correlation ID and safe reason classification. Never include passwords, OTPs, tokens, payment PINs, private keys or full secret values. Define alert severity, owner, response runbook and evidence retention; avoid collecting more personal data than necessary.

## 14. Secure development and software supply chain

Before implementation and throughout maintenance:
- Establish secure coding standards and security review for authentication, authorization, tenancy, financial, file and integration boundaries.
- Maintain an inventory of direct/transitive dependencies, versions, licenses and update status.
- Scan dependencies and build artifacts for known vulnerabilities; define triage and remediation deadlines by severity.
- Protect main/release branches, require review for sensitive changes and restrict who can modify CI workflows and release credentials.
- Use reproducible or verifiable builds where practical; restrict production secrets to protected deployment jobs.
- Run static analysis, secret scanning, dependency scanning and applicable dynamic/API security tests.
- Review database migrations, seed data, debug routes, test accounts and default credentials before release.
- Keep development/staging credentials and data separate from production.
- Maintain rollback and emergency patch processes; a rollback must not silently reverse committed financial/business data.

## 15. Incident response and recovery

Create an incident response runbook before production, with named responsibilities and contact/escalation paths. At minimum:
1. Detect and classify the incident; preserve relevant evidence and timestamps.
2. Contain the affected identity, token, integration, tenant, service or deployment without destroying forensic evidence.
3. Revoke/rotate compromised credentials and invalidate affected sessions.
4. Determine affected tenants, data classes, time window and possible unauthorized actions.
5. Preserve logs, object versions, audit records and provider evidence under controlled access.
6. Restore integrity using reconciliation, reversals or verified backups; do not blindly replay uncertain operations.
7. Assess notification, regulatory, contractual and customer communication obligations using current qualified legal guidance.
8. Recover services in priority order and validate tenant isolation, financial/stock integrity and backup state.
9. Document root cause, impact, timeline, remediation and preventive tests.
10. Conduct a post-incident review and track corrective actions to closure.

Prepare separate playbooks for account takeover, cross-tenant data exposure, privileged insider misuse, ransomware, leaked secrets, malicious upload, provider outage, compromised deployment pipeline and accidental destructive migration.

## 16. Security verification and release gates

Required verification suites:
- Cross-tenant and cross-branch isolation for list/detail/create/update/delete/search/aggregate/export/file/job/webhook paths.
- Role escalation, delegated assignment, self-approval and support-grant expiry/revocation.
- Authentication brute force, account enumeration, recovery abuse, session fixation/replay and revocation.
- CSRF, CORS, XSS, injection, mass assignment, SSRF and unsafe redirect tests as applicable to selected clients/features.
- Upload malware/quarantine, file signature mismatch, archive bomb, oversized file and safe-download tests.
- Webhook signature, timestamp/replay, duplicate event and tenant-connection binding tests.
- Cache invalidation and stale permission tests; export access recheck.
- Rate-limit, queue backpressure, resource exhaustion and noisy-neighbour tests.
- Secret scanning, dependency vulnerability scanning, build/deployment permission review.
- Backup credential isolation, immutable retention, encryption-key recovery and full restore tests.
- Audit completeness, redaction, restricted access and tamper-evidence checks.
- Deterministic concurrency tests for money, stock, settlement and idempotent external effects.

Production release gates:
- No unresolved critical security finding.
- High findings are remediated or covered by an explicitly approved, time-bounded risk exception with compensating controls.
- Threat model updated for changed trust boundaries and data flows.
- Automated security tests pass and evidence is retained.
- Production secrets, access, monitoring, incident runbooks and backup recovery are verified.
- A security test or penetration test is performed at a scope and depth proportionate to exposure before broad production launch and after material changes.

## 17. India legal and regulatory boundary

This document does not determine legal applicability or retention periods. Privacy, cybersecurity incident reporting, data localization/cross-border transfer, employment, payment and sector-specific obligations must be researched separately against current primary Indian sources and the tenant's actual activities. Maintain a legal-requirements register with source, provision, jurisdiction, effective date, applicability conditions and qualified review. Do not encode a legal obligation as a universal product rule until applicability is established.

## 18. Open decisions and dependencies

- **ODR-004/005:** membership, branch scope, role/permission catalogue, delegation and approval policy.
- **ODR-019/020:** client packaging, offline authentication, local storage and conflict behavior.
- **ODR-025/026:** tenant suspension, data lifecycle, retention, legal holds, export and deletion.
- **ODR-028:** security incident recovery objectives, backup frequency, RPO/RTO and DR.
- **ODR-030/031:** identity/secret tooling, hosting topology, network boundaries, regions and isolation tiers.
- **ODR-033:** security policy decisions including MFA, session/recovery policy, incident governance and vulnerability remediation targets.

## 19. Exit criteria

Before development readiness, complete the threat model for each critical workflow; approve identity/session and privileged-access policies; map every API/job/integration to authorization and tenant scope; define data classification and security event catalogue; select secret/key-management approach; define secure file and webhook controls; establish vulnerability management and incident response; and create automated isolation/security test plans. Before production, execute and retain the relevant verification evidence and formally disposition remaining risks.
