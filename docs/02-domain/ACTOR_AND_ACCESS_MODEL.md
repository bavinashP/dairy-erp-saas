# Actor and Access Model

**Status:** Working baseline — actor discovery  
**Purpose:** Identify who performs work and the access dimensions the authorization design must support. This is not the final role/permission matrix.

## 1. Core principles

1. A person, an employee record, a login account, a role, and a permission are different concepts.
2. A tenant owner may perform every operational task personally; the system must not require employee records or separate staff accounts for a one-person business.
3. One person may perform multiple duties. Do not force one person into one narrow role.
4. A role is a reusable bundle of permissions; it is not proof that the user is entitled to every record in the tenant.
5. Authorization must evaluate tenant, permitted branch/location scope, action, resource ownership/state, and any approval limit.
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
| Branch Manager | Oversees one or more assigned branches | Explicit branch scope | May need cross-branch reporting without transaction mutation |
| Sales Operator | Records counter sales and customer interactions | Assigned POS/register/branch | Refund, discount, and void limits may differ |
| Delivery Operator | Performs assigned deliveries | Assigned route/run/stops | Should see only necessary customer and delivery information |
| Delivery Supervisor | Plans routes and monitors delivery | Assigned branch/service areas | May reassign runs and resolve exceptions within limits |
| Procurement Operator | Records supplier purchases and milk collection | Assigned procurement points/branches | Quality-test and rate-override rights may need separation |
| Inventory Operator | Receives, transfers, counts, and adjusts stock | Assigned stock locations | High-risk adjustments may require approval |
| Finance/Accounts Operator | Manages receipts, payables, reconciliation, and accounts | Financial scope assigned | Payment recording and verification should be distinct permissions |
| HR Operator | Maintains personnel and workforce records | HR scope | Salary and sensitive employee data require additional restrictions |
| Quality/Compliance Operator | Records tests, hygiene, temperature, and corrective actions | Relevant facilities/operations | Applicable only to tenant's activity profile |
| Auditor/Read-only Reviewer | Reviews records and reports | Explicit read-only scope | Export and sensitive-field access must be separately controlled |
| Temporary/Contract Worker | Performs limited assigned tasks | Narrow, time-limited operational scope | Expiry and revocation required |

This list is a discovery catalogue. It does not establish a fixed hierarchy or mandate that every tenant create all these roles.

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
- Tenant Owner is the explicit example of a role that may receive all-branch operational visibility. This scope does not automatically grant every mutation, approval, export or sensitive-field permission.
- Other roles, including branch managers and business administrators, MUST NOT receive all-branch visibility merely from their title. Their branch scope must be explicitly assigned.
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

## 8. Open decisions

- Can one user access more than one tenant?
- Can one user be assigned to multiple branches simultaneously?
- Is branch access inherited from a tenant-wide role or always explicit?
- Which roles are system-defined and which are tenant-customizable?
- Can tenants create custom roles and permission bundles?
- How are approval limits represented and inherited?
- Which actions require maker-checker at each plan/business scale?
- What support access workflow and emergency-access procedure are required?
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
- A disabled/expired user cannot continue using valid sessions beyond the documented revocation window.
- A support operator cannot silently bypass tenant isolation.
- Every privileged change and sensitive access is auditable.
- Web, mobile, and POS clients produce equivalent authorization outcomes for the same principal and resource context.
