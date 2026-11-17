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
- **Tenant membership:** The user's association with a tenant, including status and permitted association.
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

A role bundle MUST NOT be treated as proof of tenant/branch data scope. A feature entitlement MUST NOT grant authorization. Hiding a UI action MUST NOT replace server-side authorization.

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

**Branch-isolation baseline:** An ordinary employee's default effective scope MUST be limited to their own assigned branch. Access to another branch MUST be denied unless a specific, audited cross-branch scope grant is assigned. Tenant Owner is the explicit example of a role that may receive all-branch operational visibility. A role title alone (including Branch Manager or Business Administrator) does not grant that scope. All-branch visibility is distinct from permission to mutate, approve, export or view restricted fields. Cross-branch read/report and cross-branch mutation MUST be separately permissionable. Tenant-wide master records with branch-specific relationships MUST be filtered to the actor's authorized branch context; an un-attributed record is not implicitly public to all tenant users.

ODR-004/005 remain open for the final membership, role and delegation matrix, but their resolution MUST preserve this explicit product-owner branch-isolation constraint.

## 7. Role assignment, delegation and lifecycle

Role assignments MUST be:
- Bound to the correct principal and security boundary.
- Bound to a tenant and optional branch/location scope where applicable.
- Effective-dated or otherwise lifecycle-controlled.
- Audited on creation, change, suspension, expiry and revocation.
- Validated against the assigning actor's own delegation authority.
- Evaluated together with direct grants/denials only under an explicit precedence policy.

The platform MUST prevent privilege escalation through self-assignment, circular delegation, broader scope assignment or granting permissions the actor cannot delegate.

Custom roles, platform templates, role inheritance, direct permission grants, deny rules and precedence remain policy decisions. Avoid implicit role-weight arithmetic as a substitute for permission/scope evaluation.

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

The system MUST support configurable approval and separation-of-duties policies. The exact actions requiring approval, amount thresholds, emergency exceptions and small-business simplifications remain open. Where policy requires independent approval, the initiating actor MUST not satisfy the independent-approver condition.

## 9. Platform support and privileged access

Platform administration, platform support and tenant business operation are separate authorities.

A support grant SHOULD record:
- Tenant and permitted resource/action scope.
- Purpose, ticket/case reference or reason.
- Requesting and approving principals.
- Start, expiry and revocation.
- Whether read-only or mutation is allowed.
- Actions and data accessed during the grant.

Support access MUST be deny-by-default, explicitly activated, time-bounded where feasible and audited. It MUST NOT disable tenant isolation or create an unlogged global bypass. Emergency access requires a separately governed break-glass procedure and retrospective review.

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

- **ODR-004:** Can a user belong to multiple tenants and/or branches? What is the default assignment?
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
