# Mobile Client and Offline Operation Architecture

**Document ID:** ARC-MOB-001  
**Status:** Recommended engineering baseline — client technology and offline service commitments require separate evidence/approval  
**Product:** Dairy ERP SaaS (India)  
**Authority:** Project Vision and Scope, FRS-001, ARC-API-001, ARC-IAM-001, ARC-CONC-001, ARC-TEN-001, ARC-SCALE-001, ODR-004/005/019/020/030/032.  
**Purpose:** Define a secure, maintainable mobile-client strategy and explicit offline behavior for authorized dairy-business personnel without creating a second business-rule implementation or weakening transactional integrity.

## 1. Decisions and boundaries

### 1.1 Client strategy

Adopt **one unified, role-aware mobile application as the initial product baseline**. The application exposes role- and permission-appropriate workspaces (for example owner/manager, POS/sales, delivery, procurement/collection, inventory, accounting and workforce operations). A user may have more than one authorized duty; the application must not assume one fixed screen set per person.

This is a recommended, reversible product/engineering baseline—not a commitment that every module must be enabled for every tenant or every user. Separate role-focused applications may be introduced later only when measured evidence demonstrates a material benefit in security isolation, device distribution, usability, release cadence, or operational support that outweighs the cost of multiple applications.

The platform owner/operator's privileged platform-administration experience remains logically separate from tenant business permissions. It may be delivered through a separately secured administrative client or a restricted workspace; the final packaging decision is not required to define the tenant mobile contract.

### 1.2 Explicit exclusions

- Customer self-service login, customer mobile accounts and consumer e-commerce are outside the current product scope.
- A customer record is business data managed by authorized personnel, not a login identity.
- A mobile client is not more privileged than a web client. Device type, app installation, device identifier or possession of a tenant/branch identifier never grants authorization.
- Offline capability is not a promise that every module or every transaction can operate without connectivity.
- The native, cross-platform, hybrid or PWA implementation technology is not selected here; it belongs in the technology evaluation/ADR under ODR-030.

### 1.3 Shared API and business logic

Web, mobile and any POS client MUST use the same versioned business API contracts and server-authoritative business rules. Clients may provide presentation, local input validation, permitted caching and queued intent capture, but MUST NOT independently determine authoritative price, tax, stock availability, customer balance, accounting entries, permissions, approval outcome or final transaction state.

All tenant, branch, assigned-resource and permission scopes are resolved and enforced server-side for online requests and again when queued work is synchronized.

## 2. Role-aware mobile capability model

The mobile experience is composed from capabilities and effective permissions, not hard-coded assumptions about job titles. Candidate workspaces include:

- Owner/tenant administrator: business overview, permitted setup, approvals, reports and operational controls.
- Manager/supervisor: assigned branch operations, staff coordination, exceptions and approvals within delegated scope.
- Delivery operator: assigned route/run, minimum necessary customer and delivery instructions, delivery outcomes and exception capture.
- POS/sales operator: counter sales, product lookup, permitted tender capture and receipt workflow.
- Procurement/milk collection operator: assigned collection point, producer lookup, measurement and quality evidence capture.
- Inventory/warehouse operator: permitted receipts, counts, transfers, picking and stock workflows.
- Accountant/finance operator: billing, receipts, reconciliation, accounting review and controlled posting according to permissions.
- HR/workforce operator: permitted attendance, shifts, leave and payroll workflows.
- Platform operator/support: platform-only functions and separately granted, justified tenant support access.

This list is a capability catalogue, not an approved final role/permission matrix. ODR-004/005 governs membership, scopes, role bundles, delegation and approval policy. A tenant owner must be able to operate without creating an employee record or artificial staff account.

## 3. Offline support policy

### 3.1 Initial guaranteed offline baseline

The initial offline baseline is deliberately narrow and focuses on field continuity:

1. A signed-in, registered delivery device may download a bounded, server-authorized snapshot of its currently assigned delivery run.
2. The snapshot may include only the minimum customer/service-address details, delivery instructions, planned items/quantities, route ordering and operational metadata needed to execute that assigned run.
3. The operator may record delivery attempt outcomes, delivered/partial quantities, configured exception reason, timestamp captured by the device, and limited operational notes against the downloaded run.
4. These records remain visibly **pending synchronization** until the server validates and accepts them.
5. The client synchronizes queued commands when connectivity returns, using stable idempotency identities and the conflict rules in this document.
6. The client must show the last successful sync time, pending item count, per-item state and actionable failure reason.

The guaranteed workflow duration, maximum offline snapshot age and exact data fields are not service commitments yet. They must be set using field trials, privacy review, device storage/security tests and the approved operating model before release. Until then, offline use is best-effort within configured expiry and safety limits.

### 3.2 Not permitted as an initial offline guarantee

The following operations MUST require a live server-authorized transaction in the initial baseline:

- Accounting journal posting, period close/reopen and financial adjustments.
- Payment settlement, payment verification, bank reconciliation and refund posting.
- Stock ledger posting, stock transfers, inventory adjustments and final stock reservation/consumption.
- Procurement settlement, farmer payable finalization and supplier payment.
- Subscription creation/change, recurrence/cut-off changes and schedule regeneration.
- Price, tax, product, role, permission, tenant, branch and other authoritative master-data changes.
- Approval or release of high-risk actions.
- Tally synchronization acknowledgement or any operation whose remote outcome is unknown.
- Any command requiring a current balance, quota, approval limit, credit limit, stock availability or other contested shared state.

A client may capture a draft note or scan/evidence locally for later online entry only if the user interface clearly labels it as an unposted local draft and the data is protected and expires under policy. It must never present such a draft as a completed business transaction.

### 3.3 POS offline mode

Offline POS sales are **not included in the initial guaranteed offline baseline**. They require a separate decision and threat/financial analysis covering cached prices and tax versions, stock reservation policy, receipt numbering, cash/UPI evidence, duplicate prevention, refunds/voids, device loss, concurrent terminals and reconciliation. Do not silently enable offline checkout because delivery synchronization exists.

### 3.4 Other field workflows

Offline capture for milk collection, quality measurements, inventory counts or attendance may be considered as later bounded workflows. Each requires a workflow-specific risk assessment, local data minimization, timestamp/measurement provenance, duplicate handling, supervisor review and server-side acceptance rules. No general-purpose offline write queue is authorized for all modules.

## 4. Local data protection and device lifecycle

- Register devices to a user/principal and tenant context through an authenticated, audited enrollment flow.
- Device registration is a risk and session-management signal, not a substitute for authentication or authorization.
- Support remote device/session revocation. Revocation must prevent future synchronization and invalidate access according to the tested server policy.
- Protect locally stored sensitive data using platform-supported secure storage and encryption at rest. The implementation ADR must verify key protection on each supported operating system.
- Do not store passwords, long-lived reusable secrets, unrestricted API credentials or full payment credentials in application storage.
- Keep cached data minimal, tenant-bound, branch/assignment-bound and time-limited. Avoid caching full customer directories or unrelated tenant records for convenience.
- Clear or cryptographically render inaccessible protected local data on logout, account removal, tenant switch, device revocation, cache expiry or policy-required remote wipe when the device next connects. A remote wipe cannot be assumed to work while a device remains offline.
- Protect screenshots, clipboard, notifications and app-switcher previews for sensitive screens where platform controls permit; do not expose customer details in lock-screen notifications by default.
- Require device-local re-authentication (PIN/biometric where supported) for resuming sensitive sessions, subject to the approved authentication policy. Biometrics unlock a locally protected session; they do not replace server authorization.
- Apply root/jailbreak, device integrity and OS-version checks according to a documented risk policy. Detection may trigger restricted mode or re-authentication; it must not create false claims of perfect device security.
- Record device enrollment, last-seen, revocation and relevant security events in the audit system without logging secrets or unnecessary personal data.

## 5. Synchronization protocol

### 5.1 Queue record

Every queued operation must contain, at minimum:

- Globally unique client operation ID and idempotency key.
- Authenticated principal and registered device reference.
- Tenant and branch context captured at download/capture time (as untrusted selectors, to be revalidated by server).
- Operation type and contract version.
- Target resource identifier and downloaded resource/version reference where applicable.
- Minimal command payload and payload fingerprint.
- Device-captured event time plus local sequence/order metadata.
- Creation time, last attempt time, retry count and current sync state.
- Correlation ID and a safe diagnostic/error code.

Do not treat the device clock, tenant ID, branch ID, user ID or cached permission snapshot as trusted authority.

### 5.2 Processing sequence

1. Authenticate the current user/device and verify device/session status.
2. Re-resolve active tenant membership, branch scope, assigned route/run, permission and current policy.
3. Validate command schema, supported contract version, operation expiry and payload fingerprint.
4. Enforce uniqueness/idempotency atomically on the server.
5. Validate resource ownership, current state and concurrency preconditions.
6. Apply accepted changes in the authoritative transaction boundary and emit required audit/outbox records.
7. Return a durable per-operation outcome: accepted, already applied, rejected, conflict, expired, or manual review required.
8. Client marks an item complete only after receiving a durable server outcome; transport success alone is not business acceptance.

The server must be safe if it commits an operation but the response is lost. A retry with the same key and same semantic payload returns the prior result without repeating the effect. Reuse of a key with a different payload is rejected.

### 5.3 Ordering and batching

- Preserve order for commands whose business meaning depends on sequence within the same delivery run/resource.
- Independent commands may synchronize in bounded batches.
- A failed item must not silently block unrelated independent items; dependent items must remain blocked with an explicit reason.
- The server is authoritative for final ordering and state transitions. Device timestamps alone do not establish causal order.
- Apply bounded exponential backoff with jitter for transient network/service failures; permanent validation, authorization, expiry and conflict failures must not be retried indefinitely.
- Provide an operator-visible retry/manual-resolution path that cannot bypass permissions or idempotency.

## 6. Conflict resolution and duplicate prevention

Use explicit, domain-aware conflict policies; do not use generic last-write-wins for business records.

| Situation | Required behavior |
|---|---|
| Same operation retried with same key and same payload | Return the prior durable outcome; no duplicate effect |
| Same key reused with different payload | Reject as idempotency conflict |
| Delivery stop still assigned and valid; no competing fulfilment | Validate and accept according to delivery state rules |
| Stop reassigned, cancelled, expired or already finalized by another actor | Reject or route to authorized exception review; never silently overwrite |
| Two devices report incompatible outcomes for the same stop | Preserve both evidence trails; apply explicit supervisor/manual resolution policy |
| User/device/tenant/branch permission revoked while offline | Reject synchronization; retain only safe diagnostic metadata and require authorized recovery |
| Offline snapshot exceeds configured age or route is closed | Expire the command and require online review/re-entry |
| Server committed but client did not receive response | Idempotent retry retrieves prior outcome |
| Device time differs from server time | Preserve device event time as untrusted evidence; use server receipt/commit time for authoritative ordering and audit |

A correction to an accepted delivery outcome must be a new, linked correction/exception action, not an overwrite that erases the original event.

## 7. API compatibility and mobile release lifecycle

- Mobile clients use the versioned API contract and compatibility rules in ARC-API-001.
- Every queued command records the contract version under which it was created.
- The server must explicitly support, reject or migrate queued commands from older supported versions; it must never reinterpret an old payload silently under changed semantics.
- Breaking API changes require a documented migration and supported-client policy.
- App update prompts must distinguish security-critical minimum versions from recommended updates.
- The backend must not assume that every user updates the mobile app immediately.
- Offline queues must be bounded by age, size and supported contract version. Expired/unsupported items move to a visible resolution state, not silent deletion.
- Feature flags and plan entitlements must be rechecked server-side at synchronization time; a stale local UI state cannot grant a feature.

## 8. Network, battery and operational resilience

- Handle intermittent connectivity, captive portals, DNS/TLS failures, low bandwidth, request timeout, process termination and device restart.
- Use resumable/bounded downloads for route snapshots and evidence uploads where appropriate.
- Compress payloads and avoid repeated full snapshot downloads when a safe incremental protocol is available.
- Keep background sync opportunistic and platform-compliant; provide an explicit foreground sync action.
- Do not promise immediate background synchronization on mobile operating systems.
- Protect battery and data usage with bounded retries, batching and configurable sync windows.
- Display offline state clearly and distinguish locally saved, pending, server accepted and rejected records.
- Do not discard queued evidence on app upgrade or process restart; use a versioned local queue migration strategy with tested recovery.

## 9. Privacy, audit and retention

- Apply data minimization to route snapshots and local notes.
- Avoid retaining customer location/contact data longer than required for the assigned work and approved retention policy.
- Protect uploaded delivery evidence and notes with the same tenant/branch authorization as online records.
- Record the actor, device, operation ID, server receipt time, device event time, sync outcome and correction linkage for material offline actions.
- Ensure exports, support tools, logs and analytics do not expose local cache contents or secrets.
- Define local cache expiry separately from server business-record retention; clearing a device cache must not delete authoritative business history.
- Retention duration, customer notice/consent where applicable and evidence requirements require the India privacy/legal review under ODR-026 and relevant India requirements documents.

## 10. User experience and support requirements

The mobile interface must make these states unambiguous:

- Online and synchronized.
- Offline with a valid cached assignment.
- Locally saved and waiting to sync.
- Synchronizing.
- Accepted by server.
- Rejected/expired/conflicted and requiring action.
- Device/session revoked or re-authentication required.

Show last successful synchronization, pending count, per-item reason and safe next action. Do not show a green success state for an operation that is only stored locally. Provide support diagnostics using correlation/operation IDs without exposing sensitive payloads.

## 11. Verification and acceptance tests

Required tests include:

1. A user cannot download another tenant's or unauthorized branch's route snapshot by changing request identifiers.
2. A delivery operator receives only assigned-run data and minimum required customer fields.
3. Local data is protected at rest and is cleared/inaccessible under logout, expiry and revocation tests.
4. Device revocation prevents subsequent synchronization.
5. Identical concurrent retries create one delivery effect.
6. Same idempotency key with altered payload is rejected.
7. Server commit followed by lost response is recovered by safe retry.
8. Two devices reporting conflicting outcomes preserve evidence and invoke the defined conflict path.
9. Expired/reassigned/cancelled route commands are rejected or reviewed; no stale overwrite occurs.
10. Device clock skew does not alter authoritative server ordering or financial state.
11. Process termination/restart and app upgrade preserve recoverable queued commands.
12. Unsupported/expired API contract versions produce an explicit resolution state.
13. Offline capture cannot post accounting, settle payments, post stock or alter subscription master data.
14. POS offline checkout is unavailable unless a separately approved ADR and complete test suite exist.
15. Tenant isolation and permission revocation are tested during synchronization, not only at initial download.
16. Low-connectivity, timeout, retry, duplicate batch and partial batch outcomes are tested.
17. No customer self-service account or customer-facing API is introduced by the mobile implementation.

## 12. Decisions, ownership and exit criteria

| Item | Classification | Required next action |
|---|---|---|
| Shared API and server-authoritative business rules | Resolved project constraint / existing API baseline | Preserve across all clients |
| Customer login and consumer app excluded | Resolved project constraint | Enforce in requirements and API tests |
| One unified role-aware mobile app initially | Recommended engineering/product baseline | Validate with representative role journeys before UX freeze |
| Delivery-run offline capture as the first offline workflow | Recommended engineering baseline | Validate through field trial and threat/privacy review |
| Offline POS | Deferred with trigger | Reconsider only after delivery sync is proven and POS-specific financial controls are designed |
| Offline procurement/quality/stock-count/attendance | Deferred with trigger | Add one workflow at a time with domain-specific integrity and evidence rules |
| Native/cross-platform/PWA technology | Evidence required / ODR-030 | Compare support lifecycle, device APIs, security, offline storage, accessibility, localization, testing and total cost |
| Maximum offline duration, cache TTL and supported device/OS matrix | Evidence and owner approval required | Determine from field trials, risk assessment and support capacity before release |
| Minimum supported mobile/API versions and deprecation window | Owner approval required after technical proposal | Define support policy and operational cost before production |
| Mobile device trust, MFA, local re-authentication and remote wipe policy | Owner/security approval required | Align with ARC-IAM-001 threat model and incident response |

Development readiness requires the role journey catalogue, API operation mapping, local data classification, delivery offline protocol, conflict/idempotency tests and supported-client strategy. Production readiness additionally requires field-trial evidence, approved offline duration/cache policy, device-security validation, operational support runbooks and explicit acceptance of residual offline risks.
