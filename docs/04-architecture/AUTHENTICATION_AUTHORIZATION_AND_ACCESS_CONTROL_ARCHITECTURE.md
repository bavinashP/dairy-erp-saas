# Authentication, Authorization and Access Control Architecture

**Document ID:** ARC-IAM-001  
**Status:** Proposed architecture baseline — policies and technology choices open  
**Authority:** Project Vision and Scope, FRS-001, ERS-001, Actor and Access Model, Business Rules Catalogue, and ODR-004, ODR-005, ODR-019, ODR-020, ODR-025, ODR-031.  
**Purpose:** Establish the logical identity, session, authorization and privileged-access model for platform, tenant and branch operations.

## 1. Scope and boundaries

This document defines logical security responsibilities and authorization decision inputs. It does not select an authentication provider, token format, session store, MFA vendor, identity framework or final role catalogue.

The system has internal business users and platform operators. Customer self-service login is outside the current product scope. A tenant owner must be able to operate without an employee record or staff account.

## 2. Identity concepts

The following concepts MUST remain distinct:

- **Person:** A real-world individual where the business needs a person record.
- **User account:** A login identity with authentication factors and account status.
- **Tenant membership:** An independent, revocable association between a global user identity and one Tenant; one user may have memberships in several Tenants, but each Tenant membership is validated independently.
- **Membership Branch assignment:** Optional workforce/operating context linking a Tenant membership to one or more Branches, with at most one current Home/Primary Branch where applicable; this is not an access permission.
- **Personnel record/engagement:** Optional business workforce information; not required for every login.
- **Platform principal:** Identity authorized for platform-level operations.
- **Role:** A reusable named bundle of permissions.
- **Permission:** An atomic capability over a resource/action.
- **Role assignment:** An effective-dated grant connecting a principal, role and scope.
- **Device registration/session:** Authentication context with independent lifecycle and revocation.
- **Approval limit/policy:** Constraints on high-risk actions, amounts, categories or separation of duties.
- **Support access grant:** Explicit, limited platform-to-tenant access for a justified support purpose.

## 3. Authentication architecture principles

1. Authentication establishes who or what is making a request; it does not by itself authorize business data access.
2. Every protected API, background operation and integration action MUST authenticate an actor or service identity.
3. Credentials and sessions MUST be scoped to the intended identity boundary and MUST not be accepted as proof of tenant membership without membership validation.
4. Authentication factors, account recovery, MFA, session duration, refresh/rotation and device trust policies require a documented threat-based decision.
5. Session/device revocation MUST affect subsequent requests according to an explicitly defined and tested revocation policy.
6. Passwords, OTPs, recovery codes, access tokens and secrets MUST never be logged or exposed in URLs.
7. Authentication failure responses MUST not disclose whether another tenant or user account exists beyond what the approved account-discovery policy permits.
8. Platform identities and tenant memberships MUST remain distinct. Platform role assignment does not imply tenant membership.
9. Service identities for jobs and integrations MUST be non-human, least-privileged, revocable and attributable.
10. The product must support web and mobile clients without making a client type an authorization privilege.

## 4. Authorization decision model

Every authorization decision MUST evaluate all applicable dimensions:

| Dimension | Decision question |
|---|---|
| Principal | Which authenticated user or service is acting? |
| Security boundary | Platform operation or tenant business operation? |
| Tenant membership | Is the principal an active member of this tenant or covered by a valid explicit support grant? |
| Branch/location scope | Is the resource tenant-wide, branch-attributed or assigned to an allowed location? |
| Permission | Is the requested action allowed by the effective permission set? |
| Resource ownership | Does the resource belong to the resolved tenant and valid scope? |
| Resource state | Is the requested transition/action valid in the current state? |
| Assignment/delegation | Is the actor assigned or delegated to perform this operation? |
| Approval limit | Does the operation exceed a limit or require another approver? |
| Contextual policy | Do time, device, session, risk or workflow constraints apply? |

A role bundle MUST NOT be treated as proof of tenant/branch data scope. Neither role title, job title, seniority nor user-account type grants business access by itself. A feature entitlement MUST NOT grant authorization. Hiding a UI action MUST NOT replace server-side authorization.

## 5. Permission model

A consistent permission vocabulary should be used across domains. Candidate actions include:
- view
- create
- update
- delete_draft
- submit
- approve
- reject
- post
- reverse
- export
- configure
- manage_access
- verify
- reconcile

The final catalogue must define resource semantics, state constraints, sensitive-field rules and whether each action can be delegated. Posted financial or stock records must use controlled reversal/adjustment semantics rather than ordinary edit/delete permissions.

## 6. Scope model

- **Platform scope:** Explicit platform operations only.
- **Tenant scope:** Tenant-wide capabilities, only for an active tenant membership or valid explicit platform grant.
- **Branch/location scope:** One or more explicitly authorized branches/locations.
- **Assigned-resource scope:** Optional further restriction to assigned routes, registers, collection points, records or work queues.
- **Read versus mutate:** Cross-branch reporting visibility may be granted separately from transaction mutation.
- **Sensitive data scope:** Payroll, bank details, payment evidence, personal data and credentials may require separate permissions/field masking.

**Branch-isolation baseline:** A principal's effective Branch scope MUST be established by explicit permission and scope assignments, not by account type or title. Where a user is assigned to one home/operating Branch, that assignment is a default boundary for ordinary operations, not an independent permission. Access to another Branch MUST be denied unless a specific, audited permission and cross-Branch scope grant authorize the exact read/action. All-Branch visibility is never inferred from Tenant Owner status or any role title; it requires explicit tenant-configurable read permissions and scope assignments. All-branch visibility is distinct from permission to mutate, approve, export or view restricted fields. Cross-branch read/report and cross-branch mutation MUST be separately permissionable. Tenant-wide master records with branch-specific relationships MUST be filtered to the actor's authorized branch context; an un-attributed record is not implicitly public to all tenant users.

ODR-004/005 remain open for the broader membership, role and permission/delegation matrix, but their resolution MUST preserve this explicit product-owner branch-isolation constraint and the approved ODR-002 delegated-approval baseline.

## 7. Role assignment, delegation and lifecycle

Role assignments MUST be:
- Bound to the correct principal and security boundary.
- Bound to a tenant and optional branch/location scope where applicable.
- Effective-dated or otherwise lifecycle-controlled.
- Audited on creation, change, suspension, expiry and revocation.
- Validated against the assigning actor's own delegation authority.
- Evaluated together with direct grants/denials only under an explicit precedence policy.

The platform MUST prevent privilege escalation through self-assignment, circular delegation, broader scope assignment or granting permissions the actor cannot delegate.

Roles are optional, tenant-configurable permission bundles. Platform templates may be offered as editable starting points; they MUST NOT confer immutable privileges. Tenant policy may support direct principal or group grants, but all effective capabilities must resolve to explicit atomic permissions and scopes. Role/account labels alone MUST NOT affect authorization. Permission assignment, restriction, expiry and revocation must be configurable and audited. Avoid implicit role-weight arithmetic or seniority/account-type rules as a substitute for permission/scope evaluation. The precedence model must be explicit and fail closed; no conflicting or ambiguous grant may widen access.


### 7.1 Permission-based delegated approval (ODR-002)

Delegated Approval is a temporary, explicit authorization for a named delegate to approve only the stated category/action within a defined Tenant/Branch/resource scope and limits. It does not transfer a role, general permissions, general data access or the delegator's identity.

The delegator must hold both the current approval permission for the exact scope and the separate `approval.delegate` permission for that same scope. The delegate must be an active authenticated user in the same Tenant and eligible for the specified scope. The delegation record grants only the named approval action; it does not create general Branch membership or broaden read access. The delegate must have independently authorized access to the minimum information needed to decide, or receive a minimum permission-filtered approval packet.

A delegation cannot exceed or outlive the delegator's current effective approval scope or limits. The effective approval limit is the lowest applicable limit among the delegator, delegation record and governing policy. Maximum validity is 30 calendar days; no automatic renewal or extension is permitted. A new delegation requires fresh authorization. The delegator or a separately permissioned authorized user may revoke it immediately. Loss, suspension, expiry or narrowing of the delegator's relevant approval/delegation permission, scope or Tenant membership automatically invalidates it; suspension/expiry/revocation of the delegate's account or Tenant membership also invalidates it.

Permission/access administration, Emergency Override activation/extension/review, stock adjustments/write-offs, financial-period close/reopen and sensitive data deletion are non-delegable. Other actions are delegable only where their governing policy explicitly marks them delegable. Subdelegation, onward/circular/self-delegation and approval of one's own initiated action or own delegation record are prohibited. Delegation cannot bypass maker-checker, independent approval or multi-stage approval.

Each delegated decision must identify the actual delegate and delegator and retain the delegation identifier/version, exact resource and immutable payload/version/hash, policy version, limits, decision and timestamp. Revalidate delegation and both parties' current authority server-side and atomically at approval commit. A revocation/expiry racing with an uncommitted decision must prevent commit; an approval already committed while valid remains immutable.

## 8. Separation of duties and approvals

High-risk operations may include:
- Permission/role changes and platform support access.
- Refunds, credit adjustments and payment verification.
- Stock adjustments, write-offs and transfer discrepancy resolution.
- Procurement rate overrides and farmer settlement approval.
- Financial journal posting, reversal and accounting-period close.
- Payroll finalization.
- Bulk Tally synchronization and mapping changes.
- Tenant suspension, data export and deletion.

The system MUST support configurable approval and separation-of-duties policies. The exact actions requiring approval, amount thresholds and small-business simplifications remain open. ODR-002 delegated-approval controls are approved in section 7.1; the broader ODR-004/005 permission/delegation matrix remains separate. Emergency Override is governed separately by the approved ODR-002 policy. Where policy requires independent approval, the initiating actor MUST not satisfy the independent-approver condition.

## 9. Platform support and privileged access

Platform administration, platform support and tenant business operation are separate authorities.

A support grant SHOULD record:
- Tenant and permitted resource/action scope.
- Purpose, ticket/case reference or reason.
- Requesting and approving principals.
- Start, expiry and revocation.
- Whether read-only or mutation is allowed.
- Actions and data accessed during the grant.

Support access MUST be deny-by-default, explicitly activated, time-bounded where feasible and audited. It MUST NOT disable tenant isolation or create an unlogged global bypass. Emergency access must follow the approved ODR-002 Emergency Override policy: narrowly scoped, permission-based, time-limited, auditable and subject to retrospective review. It must not create a general authorization bypass.


### 9.1 Emergency Override (ODR-002)

Emergency Override is a controlled break-glass exception for an immediate threat to human safety, product quality, refrigeration/contamination control, physical safety or material stock loss. It is not a general administrator bypass.

Any authenticated user may report an emergency, but only an authorized user with the required stock-owning-Branch approval permission may approve the normal path. If that approver is unavailable and delay would cause material harm, a predesignated Emergency Authority may self-activate only with an explicit, current, correctly scoped emergency-activation permission. Emergency Authority is an explicit permission assignment, not a job title. If no eligible authority is available, the software must deny override activation.

The grant must identify the actor, stock-owning Branch, affected OperatingLocation/StockLocation, affected stock/batch/task, exact protective actions, reason and expiry. It may permit only necessary stock visibility and protective handling, emergency receiving/put-away or quarantine/isolation. It must not grant unrelated Branch visibility or stock adjustment, write-off, valuation, pricing, purchasing, sales, financial, export, access-administration or self-approval permissions.

Initial validity is 60 minutes, with one independently authorized extension of up to 60 minutes and a 120-minute hard maximum. No automatic renewal is permitted. Every activation, use, denied attempt, extension, revocation and expiry is audited. Self-activation requires independent retrospective review, targeted within one business day after the override ends.

Expiry/revocation immediately blocks further stock mutations; completed records remain immutable and in-progress tasks are held for controlled reconciliation. Authorization and override validity must be rechecked server-side and coordinated atomically with stock commits across every execution channel. See ODR-002 in the Open Decisions Register for the complete approved policy.

## 10. API, jobs and integration enforcement

- Every API operation MUST invoke server-side authorization before returning or mutating protected resources.
- Resource IDs supplied by clients MUST be resolved inside the authorized tenant/scope, not fetched globally and filtered only in the UI.
- Background jobs MUST execute as a named service identity with minimal permissions and explicit tenant/branch context.
- Integration credentials MUST map to one tenant connection and a bounded set of capabilities.
- Webhooks and callbacks MUST authenticate origin/integrity and resolve the correct tenant connection before processing.
- Export/download access MUST be checked at request time, not only when the export job was created.
- Authorization policy changes and session revocation MUST invalidate or version relevant cached decisions.

## 11. Audit and security events

Record, as applicable:
- Authentication success/failure and recovery events.
- Session/device creation, refresh, expiry and revocation.
- Membership, role, permission, scope and delegation changes.
- Approval requests, decisions and overrides.
- Support grant creation, use, expiry and revocation.
- Sensitive record access, exports and high-risk business operations.
- Authorization denials with safe reason classification.

Audit events MUST identify actor/service, tenant/security boundary, scope, timestamp, action, target reference, outcome and correlation ID. Do not include passwords, tokens, OTPs, PINs or full secret values.

## 12. Required verification scenarios

1. Unauthenticated access to every protected API is denied.
2. Authenticated user without tenant membership cannot access tenant data.
3. Suspended/expired membership and revoked session cannot continue access beyond the approved revocation behavior.
4. Changing tenant/resource IDs cannot cross tenant boundaries.
5. Branch-scoped user cannot read or mutate an unassigned branch unless a specific permission permits the exact action.
6. Read-only access cannot mutate through an alternate endpoint, bulk operation, job or export.
7. Role assignment cannot exceed assigner's delegation.
8. User cannot self-approve an action where independent approval is required.
9. Platform administrator cannot automatically access tenant business data.
10. Support grant expires/revokes and all use is auditable.
11. Job/service identity cannot access another tenant by changing payload identifiers.
12. Permission changes invalidate affected cached decisions and take effect within the approved policy window.
13. Client type (web/mobile/POS) alone never grants additional business permission.
14. Tenant plan entitlement and authorization are independently enforced.

## 13. Open decisions

- **ODR-004:** Membership and Branch-assignment cardinality are resolved under the recommended baseline in ODR-004 section 16.9; verify edge cases during workflow design.
- **ODR-005:** Role catalogue, permission actions, scope inheritance, delegation, custom roles, direct grants and approval limits.
- **ODR-019/020:** Client strategy and offline authentication/authorization behavior.
- **ODR-025:** Tenant suspension and permitted recovery/owner access.
- **ODR-031:** Deployment/network boundaries and privileged operations.

## 14. Exit criteria

This document is a logical baseline, not a final permission matrix or authentication implementation decision. Before development:
1. Approve identity and membership lifecycle.
2. Approve role/permission/scope and delegation semantics.
3. Define MFA, recovery, session/device and revocation policies from the threat model.
4. Define support and break-glass access workflows.
5. Map every API/job/integration action to a permission and scope.
6. Complete threat analysis and automated authorization/tenant-isolation tests.


### 5.1 Permission-first and customer-configurable authorization

The account authenticates the actor; it is not an authorization profile. The same authenticated principal may receive different permissions over time or across explicitly authorized resource scopes, and different principals with identical effective permissions and context must receive identical authorization outcomes. A change to a role name, job title or account category alone must not change access.

The Tenant must be able to configure eligible permission bundles and permission assignments, including grant, amendment, restriction, expiry and revocation. The permission catalogue supplies stable atomic semantics; customer configuration determines which eligible principal/group receives each permission, at what scope, limits and effective period. Roles are optional bundles only. Direct principal/group grants are permitted only under a governed assignment policy and cannot exceed the assigning actor's delegation authority. Tenant configuration cannot weaken fixed platform invariants.

Every read, field-level disclosure, mutation, approval, report, export, configuration and access-administration action must be checked independently. Permission scope may include Tenant, Branch, OperatingLocation, StockLocation, assigned task/resource, data/field category, quantity/value limits and effective time. Default deny applies to missing, expired, revoked, ambiguous or unverifiable permission/scope. Read does not imply mutation; mutation does not imply approval; count capture does not imply variance approval; quarantine does not imply release; internal transfer does not imply inter-Branch ownership transfer.

Permission changes must be versioned/audited and take effect through authoritative server-side revalidation. Cache, session, mobile/offline, export, background-job and integration contexts must not preserve revoked authority. Every write must coordinate authorization revalidation with the state-changing commit where race conditions are possible.

The complete approved cross-Branch stock-task action matrix and customer configurability boundaries are specified in `CROSS_BRANCH_PERMISSION_MATRIX.md`.


### 5.2 Permission precedence and conflict resolution

Authorization precedence is fail-closed:
1. Fixed platform/security invariants and explicit legal/contractual prohibitions are hard constraints; tenant grants cannot override them.
2. Authentication, active Tenant membership, security boundary and resource ownership must be valid before permissions are evaluated.
3. An explicit applicable deny overrides any applicable allow for the same action/resource/scope. A narrower allow cannot override a mandatory broader deny or fixed invariant.
4. Applicable allows contribute only within their own valid scopes, limits and effective periods. The effective allow scope is the union of independently valid allow scopes after applicable denies and higher-order constraints are applied.
5. No implicit role inheritance, seniority, account-type or branch-hierarchy rule may widen authority. An assignment cannot exceed the source authority's delegation.
6. Conflicting, ambiguous or unverifiable policy results default to deny.
7. Versioned permission changes must be revalidated at commit for protected state transitions.

The precedence model applies consistently to direct grants, group assignments, role bundles and temporary grants. Tenant configuration cannot change this precedence or weaken fixed invariants.


### 7.2 Tenant membership and workforce assignment lifecycle

A global human identity may hold multiple independent TenantMemberships, at most one current membership for a given Tenant. Each membership is activated, suspended, expired and revoked independently. A Tenant context is resolved and validated per request; no cross-Tenant operation is implied by multiple memberships. Each membership may have zero or more effective workforce Branch assignments and optionally one current Home/Primary Branch. These assignments support scheduling/organizational context only and never grant access. Explicit permissions and scopes remain mandatory. Membership revocation invalidates the associated Tenant permissions/delegations/sessions according to the immediate security revocation policy. See ODR-004 section 16.9 and ERS section 16.8.
