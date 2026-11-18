# Cross-Branch Permission Matrix and Configurable Authorization

**Document ID:** ARC-AUTH-MATRIX-001  
**Status:** Product-owner-approved baseline — 2026-11-19  
**Authority:** ODR-002 sections 16.7 and 16.36–16.38; ODR-005 sections 16.39 and 16.41; Permission Catalogue and Assignment Governance; Domain Permission and Approval Control Matrix; Authentication, Authorization and Access Control Architecture; Actor and Access Model; Business Rules Catalogue.  
**Scope:** Permission-first authorization and cross-Branch shared-site stock-task access. This document does not implement software or database structures.

## 1. Mandatory permission-first principle

The authenticated user account establishes the identity of the actor. It does not itself grant business access. A person's job title, seniority, business designation, role title, account category, employment category, device/client type or Tenant Owner status MUST NOT independently grant permission to view data or perform an action.

Business authorization is determined only by the actor's effective explicit permissions and applicable scope, together with Tenant membership, resource ownership/state, assignment or grant validity, limits, approval requirements, separation-of-duties and other governing policy.

A role is an optional, reusable and customer-configurable bundle of atomic permissions. It is not a fixed privilege class, authority rank or hierarchy. A role name alone grants nothing. A role assignment can contribute permissions only through the bundle's current explicit contents and only within the assigned scope and validity period.

## 2. Customer configurability and permission lifecycle

Each Tenant must be able to configure eligible permission bundles and permission assignments for its own principals, subject to fixed platform invariants. The model must support:

- Creating and maintaining customer-configurable permission bundles from the platform's stable atomic permission catalogue.
- Assigning permissions through a configured bundle, group or other governed assignment mechanism.
- Direct principal-level permission grants where tenant policy enables them.
- Granting, restricting, amending, suspending, expiring and revoking permissions.
- Defining scope, limits, purpose and effective start/end where applicable.
- Removing one permission without unintentionally removing unrelated permissions.
- Auditing who changed which permission, for whom, within what scope, when, why and under which authority.
- Preventing an assigning actor from granting or delegating authority beyond their own current effective permissions and explicit access-administration authority.

Platform-provided bundles may be editable starting templates, not immutable role-based privileges. The platform permission catalogue defines stable action semantics; tenant configuration determines which eligible principals receive which permissions and scopes. Customer configuration cannot invent a shortcut around a fixed invariant or convert a read permission into a write, approval or export permission.

## 3. Effective authorization rules

1. Evaluate every requested data disclosure and every action independently.
2. Require an explicit permission for the exact resource/action and applicable scope.
3. Apply scope restrictions such as Tenant, Branch, OperatingLocation, StockLocation, assigned task/resource, field/data category, quantity/value limit and effective period.
4. Validate active Tenant membership and resource ownership independently of permission assignment.
5. Treat read, create, update, submit, approve, reject, post, reverse, reconcile, export, configure and manage-access as distinct capabilities.
6. A permission for one action does not imply another: view is not update; count capture is not variance approval; quarantine is not release; receive is not adjustment; same-Branch internal transfer is not inter-Branch ownership transfer.
7. Default deny applies when any required permission, scope, membership, approval, assignment or validity condition is absent, expired, revoked, ambiguous or unverifiable.
8. Feature entitlement controls feature availability/usage only; it never grants permission or data access.
9. A role/account label change alone must not change authorization outcomes.
10. Permission changes and revocations must be enforced by authoritative server-side checks. Stale sessions, caches, queued jobs, exports, mobile/offline actions and integrations must not preserve revoked authority.
11. Permission validation and protected state-changing commits must be coordinated atomically where a race could otherwise permit a revoked/expired action.
12. Tenant isolation, stock ownership, auditability, concurrency integrity and segregation-of-duties controls cannot be disabled by customer configuration or subscription plan.

### 3.1 Permission precedence and conflict handling

Apply authorization in this order:

1. Fixed platform/security invariants and explicit legal/contractual prohibitions are hard constraints and cannot be overridden by a Tenant permission grant.
2. Authentication, active Tenant membership, security boundary and resource ownership must be valid before a business permission is considered.
3. An explicit applicable deny takes precedence over any applicable allow for the same action/resource/scope. A narrower allow cannot override a broader mandatory deny or fixed invariant.
4. Applicable allow assignments may contribute permissions only within their own valid scopes, limits and effective periods. The effective allowed scope is the union of independently valid allow scopes after applicable denies and all higher-order constraints are applied; scope is never inferred from a role label or account type.
5. A permission assignment cannot widen the scope or limits of its source authority. No implicit role inheritance, seniority, account-type or branch hierarchy is used.
6. If two policies conflict, precedence cannot be established, ownership is ambiguous or required authorization data is unavailable, fail closed and deny the operation.
7. Permission changes are versioned; in-flight operations must revalidate the current version at commit where the action changes protected state.

## 4. Cross-Branch shared-site stock-task matrix

This matrix applies when a named worker handles stock owned by another Branch at a shared OperatingLocation. The worker must have both (a) an active approved StockTaskAccessGrant for the exact action and (b) the independently effective permission for that action. The grant is not general Branch membership and exposes only minimum task/stock data.

| Action or data category | Baseline | Required boundary |
|---|---|---|
| View assigned task | Allow only with explicit permission and active grant | Minimum task data only; no unrelated task queues or Branch dashboards |
| View stock needed for task | Allow only with explicit permission and active grant | Minimum SKU/lot/quantity/location details; no general inventory or valuation browsing |
| Receive stock | Allow only when explicitly granted | Exact receipt/task, item, StockLocation and quantity limits; preserve evidence |
| Put-away | Allow only when explicitly granted | Only approved destination StockLocations and assigned task |
| Pick stock | Allow only for assigned task and explicit permission | No unrestricted picking or unrelated demand visibility |
| Issue stock to task | Allow only for assigned task and explicit permission | Preserve stock-owning Branch attribution and custody evidence |
| Internal transfer within same stock-owning Branch | Allow only with explicit permission | Explicit source/destination StockLocations; no implicit access to other locations |
| Physical stock count capture | Allow only with explicit count-capture permission | Record observation only; no variance approval or adjustment |
| Quarantine / isolate | Allow only with explicit protective-action permission | Does not authorize release, disposal, write-off or valuation change |
| Release from quarantine | Deny by default | Separate explicit release permission and required independent approval |
| Stock reservation / allocation | Deny by default | Only a separate task-bound reservation permission may allow a narrow reservation |
| Stock ownership transfer to another Branch | Deny under stock-task grant | Separate inter-Branch workflow, permissions and approvals |
| Stock adjustment / correction | Deny under stock-task grant | Separate authorized workflow; no ordinary handling grant |
| Write-off / wastage posting | Deny under stock-task grant | Separate permission and independent approval under inventory policy |
| Stock variance approval | Deny under count-capture permission | Separate variance-review/approval permission and maker-checker controls |
| Stock valuation, costing or price change | Deny | Separate inventory/finance authority; never implied by handling stock |
| Purchase, supplier/farmer, sales, customer or financial records | Deny unrelated access | No access merely because stock handling requires limited item/task context |
| Financial posting, payment, settlement or period close | Deny | Separate financial permissions and workflows |
| Full Branch reports, analytics or bulk search | Deny | Separate report permissions and data scopes |
| Export, bulk download or data sharing | Deny | Separate export permission, dataset scope, purpose and audit |
| Permission, role, grant or workforce administration | Deny | Separate access-administration/workforce permissions |
| Safety/incident report and audit evidence | Allow minimum non-mutating reporting where explicitly permitted | Does not authorize stock mutation or unrelated data access |

## 4.1 Cross-Branch delivery-run and transport assignment boundary

The stock-task matrix in section 4 is not a general cross-Branch work grant. A StockTaskAccessGrant authorizes only its exact inventory actions and cannot authorize Route access, DeliveryRun assignment, customer-stop access, driving, delivery execution or receipt collection.

For a worker assigned to a DeliveryRun owned by another Branch:
- The Route and DeliveryRun remain owned by the same Branch; route/service-area ownership rules in ODR-002 section 16.37 still apply.
- The participant must have an explicit delivery action permission scoped to the target run/Branch/assigned stops and a current run-personnel assignment.
- Any shift, duty, attendance or workforce allocation required by Tenant policy must be separately authorized through the workforce workflow. Home/Primary Branch does not grant or veto run access automatically.
- The participant sees only the minimum run/stop/customer information required for the assigned work. Reassignment/revocation blocks subsequent access and actions; physical custody of goods remains separately recorded until an actual handover or controlled resolution.
- Vehicle/transport-resource assignment is separately permissioned and scoped. Vehicle ownership, driver duty label or asset custody does not grant access to a run or customer data.
- Dispatch, stop execution, proof capture, cash/receipt capture, exception review and run closure are distinct actions. Each requires its own permission and applicable scope/limit.
- External carriers or temporary participants receive only a bounded task/connection identity where supported; this does not create broad Tenant membership or a customer self-service account.
- Cross-Branch run reassignment, route deviation or replacement resource must be audited and cannot borrow another Branch's Route or customer allocation implicitly.

A missing permission, workforce assignment required by policy, active run assignment, valid resource, or Branch/Route ownership match defaults to deny. The stock-task grant, a job title, an account category or an existing Home Branch assignment cannot be used as a substitute.

## 4.2 Stock-task grant lifecycle and task reconciliation authority

A StockTaskAccessGrant is a narrowly scoped authorization exception, not a permission assignment. The following actions are independent and require the corresponding explicit scoped permissions:
- `inventory.stock_task_grant.create/submit`: create and submit a request for exactly one named worker and the proposed stock-owning Branch/resource/action/task/quantity/period envelope.
- `inventory.stock_task_grant.approve/reject`: decide the exact immutable GrantVersion. Approval requires the stock-owning Branch's approval permission, or a currently valid delegate within ODR-002 section 16.4. The approved payload/version must not be substituted after approval.
- `inventory.stock_task_grant.withdraw`: withdraw a pending request before approval where the requester retains the exact withdrawal authority.
- `inventory.stock_task_grant.revoke`: immediately end an active/scheduled grant within the actor's stock-owning-Branch revocation scope.
- `inventory.stock_task_grant.review/reconcile`: review held work and decide reauthorization, reassignment, controlled closure, correction/compensation or escalation.
- Inventory action permissions such as receive, put-away, pick, issue, transfer or count capture remain separately assigned to the worker. A grant cannot create those permissions.

Every active action requires all of the following: active grant version; active worker account and Tenant membership; independent permission for the exact stock action; exact stock-owning Branch, OperatingLocation, StockLocation, item/lot/task scope; quantity/value limits; any workforce assignment required by Tenant policy; and no safety/quality/ownership/incident prohibition. Default deny applies if any condition is absent, invalid or ambiguous.

A material change to the named worker, Branch, location, action, item/task scope, limit or validity window requires a new immutable grant version and fresh approval. Replacing the worker requires a new grant request; a grant cannot be transferred. A successor extension must be approved before the current version expires. A pending request never extends current authority.

At expiry, revocation or automatic invalidation, new tasks and further stock mutations stop immediately. Each affected in-progress task is held and preserves committed steps, posted stock movements, physical custody, quantities and outstanding work. Further action requires an active grant for the same worker, or a new grant and any required workforce assignment for a replacement worker with a real custody handover where goods change hands. Reconciliation is separate from the grant's authorization state and must use linked, auditable correction/compensation evidence; no auto-complete, auto-rollback, silent balance overwrite or fabricated custody transfer is permitted.

## 5. Cross-channel enforcement

The same authorization model applies to web, mobile, POS, barcode/scanner, API, reports, search, files/attachments, exports, analytics, scheduled/background jobs, webhooks and third-party integrations.

- Every read and every state-changing transition is checked server-side at the resource boundary.
- Resource identifiers supplied by a client are resolved inside the authorized Tenant and scope.
- UI hiding is not a security control.
- Export/download access is rechecked when the artifact is requested, not only when its job is created.
- Jobs and integrations use named service identities with explicit least-privilege permissions and Tenant/resource scope.
- Offline or delayed operations are revalidated before server commit; stale offline authorization cannot authorize a write.
- Permission changes invalidate or version affected authorization caches and pending work.
- Stock authorization and stock commit are concurrency-safe with respect to grant expiry/revocation and duplicate retries.

## 6. Governance, audit and non-delegable boundaries

Permission assignment and revocation require dedicated access-administration permissions and scope. Self-escalation is prohibited. A role, account type or title does not authorize permission administration.

Audit evidence must include the actor, target principal/group/bundle, permission, scope, previous and new state, reason, effective time, approving authority where required, policy/version and correlation reference. Denials and sensitive data access must be attributable without logging secrets.

The separately approved ODR-002 rules continue to govern:
- Cross-Branch stock-task grant approval by an authorized user with the required stock-owning-Branch approval permission, or a currently valid delegate.
- Delegated approval scope, 30-calendar-day maximum, revocation and non-delegable actions.
- Emergency Override scope, permission, 60-minute initial validity and 120-minute hard maximum.
- Zero-grace grant expiry/revocation, in-progress task hold and controlled reconciliation.
- Home-Branch/workforce-assignment separation.

Customer configurability cannot bypass maker-checker, independent approval, multi-stage approval or a non-delegable action restriction.

## 7. Acceptance tests

1. Two principals with identical effective permissions and scope receive identical outcomes for the same resource/action context, regardless of titles or account labels.
2. Two principals with the same role title but different effective permissions receive different outcomes as configured.
3. Changing a role title, job title or account type alone changes no authorization outcome.
4. A Tenant can grant, amend, restrict and revoke a permission within its delegated authority; revocation denies the next authoritative request.
5. Removing one permission does not silently remove unrelated permissions.
6. A role/group/direct assignment cannot exceed the assigning actor's delegation authority.
7. Read-only task visibility cannot mutate through alternate endpoints, bulk APIs, exports, jobs, mobile/barcode or integrations.
8. Count capture cannot approve a variance or post an adjustment.
9. Quarantine cannot release stock; same-Branch internal transfer cannot transfer ownership to another Branch.
10. A stock-task grant cannot expose unrelated Branch data or grant purchase, sales, financial, export or access-administration capabilities.
11. Revoked/expired grants and permissions deny subsequent actions; stale sessions, caches and offline queues cannot commit unauthorized work.
12. Every grant, change, revocation, denial and sensitive access is attributable and auditable.

## 8. Scope still requiring separate specification

This document resolves the approved cross-Branch stock-task action baseline, the delivery-run boundary and the permission-first configurability principle. The canonical atomic permission inventory is selected in ARC-IAM-CAT-001 section 15 / ODR-005 section 16.39; the domain-level risk and approval-control baseline is selected in ARC-IAM-CTRL-001 / ODR-005 section 16.41. Shared-site custody, route/dispatch and detailed grant/task-reconciliation baselines are specified in ODR-002 sections 16.36–16.38. Remaining work is scenario and implementation-readiness validation: exact resource/state/API mapping, sensitive-field classification, automated tests, plus external legal/contract/insurance applicability review. None of these remaining gates creates a title-based or account-based access exception.
