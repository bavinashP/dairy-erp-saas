# Actor and Access Model

**Status:** Conceptual actor/workforce baseline — authorization is governed by ARC-IAM-001 and ARC-IAM-CAT-001  
**Purpose:** Identify who performs work and the access dimensions the authorization design must support. This is not the final role/permission matrix.

## 1. Core principles

1. A person, an employee record, a login account, a role, and a permission are different concepts.
2. A tenant owner may perform every operational task personally; the system must not require employee records or separate staff accounts for a one-person business.
3. One person may perform multiple duties. Do not force one person into one narrow role.
4. A role is an optional, tenant-configurable bundle of explicit permissions; neither role title nor user-account type grants access by itself.
5. Authorization must evaluate effective explicit permission, Tenant membership, Branch/location/resource scope, ownership/state, validity, limits and approval policy. Identity/account type only identifies the actor.
6. No client type (web/mobile/POS) grants permission by itself.
7. Customer self-service accounts are outside the current approved scope.
8. Platform support access must be distinct from tenant business access, limited, justified, time-bounded where feasible, and audited.
9. Shared devices may be used operationally, but shared login identities must not erase individual accountability for material actions.
10. Permission checks must be enforced by the backend, not only by hiding interface controls.

## 2. Actor categories

| Actor category | Typical responsibility | Candidate access scope | Notes |
|---|---|---|---|
| SaaS Platform Owner | Owns the SaaS product and commercial operation | Platform-wide administration | Exact separation from support/operations roles remains to be defined |
| Platform Administrator | Manages platform configuration and tenant lifecycle | Platform functions only, with explicit capabilities | Should not automatically receive unrestricted business-data access |
| Platform Support Operator | Assists tenants with incidents and configuration | Just-in-time, approved, scoped support access | Every access and action should be audited |
| Tenant Owner | Owns/controls the dairy business | Tenant-wide, subject to platform controls | May also act as cashier, purchaser, dispatcher, or accountant |
| Tenant Business Administrator | Manages tenant setup and users | Tenant configuration and delegated administration | Cannot grant permissions beyond own delegation |
| Branch Operations User | Performs assigned operational coordination across one or more explicitly scoped branches | Explicit branch scope and permissions | May need cross-branch reporting without transaction mutation; no job title itself grants scope or approval authority |
| Sales Operator | Records counter sales and customer interactions | Assigned POS/register/branch | Refund, discount, and void limits may differ |
| Delivery Operator | Performs assigned deliveries | Assigned route/run/stops | Should see only necessary customer and delivery information |
| Delivery Operations User | Plans routes and monitors delivery | Assigned branch/service areas | May reassign runs and resolve exceptions only where explicit permissions authorize those actions |
| Procurement Operator | Records supplier purchases and milk collection | Assigned procurement points/branches | Quality-test and rate-override rights may need separation |
| Inventory Operator | Receives, transfers, counts, and adjusts stock | Assigned stock locations | High-risk adjustments may require approval |
| Finance/Accounts Operator | Manages receipts, payables, reconciliation, and accounts | Financial scope assigned | Payment recording and verification should be distinct permissions |
| HR Operator | Maintains personnel and workforce records | HR scope | Salary and sensitive employee data require additional restrictions |
| Quality/Compliance Operator | Records tests, hygiene, temperature, and corrective actions | Relevant facilities/operations | Applicable only to tenant's activity profile |
| Auditor/Read-only Reviewer | Reviews records and reports | Explicit read-only scope | Export and sensitive-field access must be separately controlled |
| Temporary/Contract Worker | Performs limited assigned tasks | Narrow, time-limited operational scope | Expiry and revocation required |

This list is a discovery catalogue of business responsibilities only. It does not establish a fixed hierarchy, privilege class or mandate that every Tenant create all these roles. Permissions and scopes, not this list, determine access.

## 3. Access dimensions

Authorization design must evaluate at least the following dimensions where relevant:

- Principal: authenticated user/service identity.
- Tenant: tenant context bound to the session/request.
- Branch: one or more branches the user may access.
- Operational location: warehouse, POS register, collection point, processing site, route, or service area.
- Resource ownership: record tenant, branch, creator/assignee, or responsible unit.
- Action: view, create, update, submit, approve, post, reverse, export, configure, administer.
- Resource state: draft, submitted, approved, posted, closed, cancelled, reversed.
- Approval threshold: amount, quantity, discount, variance, or risk level.
- Field sensitivity: salary, bank details, identity documents, customer contact data, and other restricted fields.
- Time/context: active assignment, shift, temporary access, or support approval.
- Client/device context: allowed client or trusted device only where a justified security policy requires it.

## 4. Permission action vocabulary (candidate)

Use consistent action semantics rather than creating unrelated permission names in every module:
- view — retrieve permitted data.
- create — create a draft/new record.
- update — modify a record while its state permits modification.
- submit — submit for review/approval.
- approve — approve within delegated scope and limits.
- reject — reject with a reason where workflow supports it.
- post — commit a financially/operationally material transaction.
- reverse — create a controlled reversal/correction.
- cancel — cancel only when state and policy allow.
- export — extract data; treat separately from view.
- print — generate a document/receipt where relevant.
- configure — change tenant/branch business settings.
- manage_access — assign/revoke users, roles, and scopes within delegation.
- administer — perform explicitly defined administrative actions.

These verbs are candidates for a consistent vocabulary; each module must define its valid actions and state constraints.

## 5. Separation of duties and high-risk actions

The design should support optional maker-checker controls for actions such as:
- High-value refunds or credit notes.
- Large discounts or price overrides.
- Supplier rate changes.
- Milk quality acceptance overrides or test-result correction.
- Stock write-off and large stock adjustments.
- Cash/bank reconciliation approval.
- Payment reversal and receipt reallocation.
- Payroll finalization.
- User role elevation and platform support access.

Small businesses may choose simpler workflows where legally and operationally appropriate, but auditability and accountability remain. The exact thresholds and mandatory controls are open decisions.

## 6. Data visibility principles

- Tenant isolation is absolute at the platform boundary.
- Branch visibility is explicit and does not imply permission to mutate.
- Cross-branch reporting can be a separate permission from cross-branch transaction access.
- A delivery worker should receive only the customer/contact/address details needed for assigned deliveries.
- Financial, salary, bank, and identity fields should be field-restricted where appropriate.
- Exports are a separate risk surface and require explicit authorization and audit.
- Ordinary employees MUST default to only their own assigned branch; another branch's records remain inaccessible unless an explicit, audited cross-branch scope grant applies.
- All-Branch visibility is never implied by Tenant Owner status, role title or account type. It is granted only through explicit customer-configurable read permissions and Branch scopes; mutation, approval, export and sensitive-field access require separate permissions.
- No job title, role label, seniority, business designation or user-account type by itself grants visibility or approval authority. Each data category and action must be explicitly authorized through effective permission assignments and scope.
- Cross-branch reporting/read access and cross-branch transaction mutation MUST be separate capabilities. Tenant-wide master records must not become unrestricted merely because they are tenant-owned.
- Platform support access must not be a silent tenant-scope bypass.
- Access revocation must take effect for subsequent requests and sessions according to defined session invalidation policy.

## 7. User, employee, and role lifecycle

The future requirements must cover:
- Owner account creation and recovery.
- Invitation and activation.
- Employee profile without login account.
- Login account without employee profile (for owner/external auditor where justified).
- Multiple role assignments and branch scopes.
- Temporary assignments and expiry.
- Role/permission changes with audit.
- Suspension, termination, and immediate access revocation.
- Account recovery and MFA policy.
- Device/session management.
- Staff turnover and reassignment of open work.


### Emergency Authority (ODR-002)

Emergency Authority is not a job category or standing administrative role. It is a specifically assigned, current and scoped permission to activate the restricted ODR-002 Emergency Override when the ordinary approver is unavailable and a qualifying emergency makes delay materially harmful. The authority must be explicitly permissioned, audited and time-bounded; no title or general administrator status is sufficient. Emergency activation, extension and retrospective review are separate permission checks.


### Delegated Approval (ODR-002)

Delegated Approval is a temporary, explicitly permissioned authorization, not a job category or role inheritance. The delegator must hold the relevant approval permission and the separate `approval.delegate` permission for the same scope. The delegate receives only the specified approval action, category, Branch/resource scope and limits for no more than 30 calendar days. The delegation cannot widen data visibility, exceed the delegator's current authority, renew automatically or be passed onward. It is immediately revocable and automatically invalidated when either party's relevant Tenant membership or the delegator's underlying approval/delegation authority ceases to be valid.

Permission/access administration, Emergency Override activation/extension/review, stock adjustments/write-offs, financial-period close/reopen and sensitive data deletion are non-delegable. Other approval actions are delegable only when their governing policy explicitly permits it. Delegated approval cannot bypass maker-checker, independent or multi-stage approval; a delegate cannot approve an action they initiated or their own delegation record. Actual delegate and delegator identities must both be retained in the audit evidence.

## 8. Open decisions

- Can one user access more than one tenant?
- **Resolved under ODR-004:** a Tenant membership may have zero or more workforce Branch assignments; one optional Home/Primary Branch may be effective at a time. These assignments never grant data access by themselves.
- Is branch access inherited from a tenant-wide role or always explicit?
- Which roles are system-defined and which are tenant-customizable?
- Can tenants create custom roles and permission bundles?
- How are approval limits represented and inherited?
- Which actions require maker-checker at each plan/business scale?
- What support access workflow is required? Emergency Override and delegated approval are governed by approved ODR-002 decisions. ODR-004 membership cardinality is resolved in section 16.9; ODR-005 remains for the exact module-level permission catalogue and domain approval policies. See ARC-IAM-CAT-001.
- What authentication assurance (MFA, passkeys, OTP, password policy) is required for each risk tier?
- How are shared POS and delivery devices handled without shared identities?
- What is the session/device revocation behavior?

## 9. Acceptance criteria for the future authorization specification

The final design must demonstrate:
- A tenant user cannot read or mutate another tenant's data by changing identifiers or API parameters.
- A branch-scoped user cannot access unassigned branch data unless an explicit permission grants a defined cross-branch capability.
- A read-only user cannot mutate records through alternate API endpoints.
- A user cannot approve their own action where maker-checker policy prohibits it.
- A role assignment cannot grant permissions beyond the assigner's delegation.
- A delegated approver can act only within the active delegation's exact category/action, Tenant/Branch/resource scope, limits and effective period.
- Loss of the delegator's relevant approval/delegation permission or either party's active Tenant membership invalidates the delegation immediately.
- A delegated approver cannot subdelegate, approve a non-delegable action, bypass multi-stage approval or approve an action they initiated; the audit trail identifies both delegate and delegator.
- A disabled/expired user cannot continue using valid sessions beyond the documented revocation window.
- A support operator cannot silently bypass tenant isolation.
- Every privileged change and sensitive access is auditable.
- Web, mobile, and POS clients produce equivalent authorization outcomes for the same principal and resource context.


## 10. Tenant-configurable permission-first authorization

The authenticated user account identifies the actor; the account type, job title and role label do not determine business access. A Tenant may configure reusable permission bundles and assign, amend, restrict, expire or revoke them for eligible principals within the assigning authority's own delegation. A role is optional convenience for grouping permissions, not a fixed privilege class or hierarchy. Direct or group-based scoped grants may be supported under tenant policy, but every effective capability must resolve to an explicit atomic permission and scope.

Every read, mutation, approval, export, report, sensitive-field access and access-administration action is evaluated independently. Permissions must be separable by action and resource, and may be scoped to Tenant, Branch, OperatingLocation, StockLocation, assigned task/resource, fields/data categories, limits and effective period. No permission is inferred from another action, a feature entitlement, account category, client type or role title. Default deny applies where a required permission/scope/condition is absent or invalid. Customer configuration cannot disable tenant isolation, audit, stock ownership, segregation of duties or other fixed invariants.

Permission changes and revocations are audited and must be revalidated server-side across all channels, including cached sessions, mobile/offline queues, exports, jobs and integrations. Role/account label changes alone must not change authorization results. See `../04-architecture/CROSS_BRANCH_PERMISSION_MATRIX.md` for the approved cross-Branch stock-task action matrix.


### 10.1 Delivery-run and transport assignment

A participant's assignment to a DeliveryRun or an operational duty label such as driver, delivery handler, helper or accountable run assignee is not a permission grant. The participant needs current explicit permissions, Branch/run/stop scope and a valid assignment; any workforce duty/shift allocation required by Tenant policy is separately authorized. A vehicle or asset assignment never grants run or customer-data access. Cross-Branch delivery work requires explicit delivery permissions and the applicable workforce assignment; a StockTaskAccessGrant, Home/Primary Branch or role title cannot substitute. Dispatch, execute, hold/resume, exception resolution, proof capture and close are distinct actions. See [Cross-Branch Permission Matrix](../04-architecture/CROSS_BRANCH_PERMISSION_MATRIX.md) section 4.1 and ODR-002 section 16.37.

## 11. Tenant membership and Branch assignment

One global user identity may hold independent memberships in multiple Tenants, with at most one current membership per user/Tenant pair. Membership in one Tenant never grants access to another. Within a Tenant, a membership may have zero or more Branch/workforce assignments and optionally one current Home/Primary Branch. These assignments identify normal workforce/operating context only; they are not permissions and do not grant record visibility. All data and action authority comes from effective explicit permissions and scopes. See ODR-004 section 16.9 and ERS section 16.8.


The cross-module permission vocabulary, customer-configurable bundles/direct assignments, sensitive-field control and precedence rules are defined in [Permission Catalogue and Assignment Governance](../04-architecture/PERMISSION_CATALOGUE_AND_ASSIGNMENT_GOVERNANCE.md).