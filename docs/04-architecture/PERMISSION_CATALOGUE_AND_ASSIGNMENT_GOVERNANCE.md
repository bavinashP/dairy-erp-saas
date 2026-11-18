# Permission Catalogue and Assignment Governance

**Document ID:** ARC-IAM-CAT-001  
**Status:** Recommended permission-first baseline selected under the product owner's delegated decision mandate — 2026-11-19  
**Authority:** ODR-002 sections 16.7 and 16.9; ODR-005; ARC-IAM-001; ARC-AUTH-MATRIX-001; ARC-PV-001; Actor and Access Model; Business Rules Catalogue.  
**Purpose:** Define a consistent, extensible and customer-configurable permission language across all Dairy ERP modules without relying on job titles, role labels or account categories.

## 1. Non-negotiable principle

A user account authenticates an actor. It does not grant business access. A permission is the only source of business action/data authority after the actor's Tenant membership, resource ownership, scope, validity and all governing policies are validated.

No user receives a permission merely because they are a Tenant Owner, Owner, Administrator, Manager, Supervisor, Accountant, Employee, Contractor or another job/business title; a human, employee, service account, mobile/POS user, platform identity or account category; assigned to a Home/Primary Branch or multiple workforce Branches; or logged in through web, mobile, POS, barcode, API or integration.

Different people with identical effective permissions, scope and request context must receive identical authorization outcomes. Changing a title, role name or account category alone must not change an outcome.

## 2. Permission terminology

| Concept | Meaning | Not equivalent to |
|---|---|---|
| UserAccount | Authenticated identity | Tenant membership or business permission |
| TenantMembership | Independent association of a user identity with one Tenant | Data visibility or action authority |
| MembershipBranchAssignment | Workforce/operating context for a Tenant member | Branch read/write permission |
| Permission | Atomic data/action capability | Job title, role or plan entitlement |
| PermissionAssignment | Effective-dated grant/deny of a permission to a principal/group with scope and limits | Membership or a stock-task grant |
| PermissionBundle | Customer-configurable collection of atomic permissions | Immutable privilege class |
| Role | Optional named PermissionBundle assignment | Fixed hierarchy or authority rank |
| Scope | Resources/data to which a permission applies | Permission by itself |
| Feature entitlement | Commercial permission for a Tenant to use a product capability/usage allowance | User authorization |
| Capability activation | Tenant enablement of a supported capability | User authorization |
| Extension grant | Bounded permission for a specific extension/service identity | General Tenant permission |
| ApprovalDelegation | Time-bounded authority to perform specified approval actions | Role transfer or general permission bundle |
| StockTaskAccessGrant | Cross-Branch task-specific grant for specified stock actions | General target-Branch access |

## 3. Permission key catalogue

Use a stable, language-neutral key format: domain.resource.action.

Examples: inventory.stock.view; inventory.stock.receive; inventory.count.capture; inventory.variance.approve; finance.journal.post; party.bank_details.view.

Permission keys are machine identifiers and MUST NOT be renamed to match a customer label or language. Display name, description, help text and translation are separate metadata.

Every permission catalogue entry must define:
- Stable key and immutable semantic definition.
- Domain/resource/action and allowed scope types.
- Whether it is read, mutation, approval, export, sensitive-data or administration capability.
- Whether it is high risk, delegable, maker-checker protected or non-delegable.
- Required related policies/state preconditions and authorization inputs.
- Whether the permission is eligible for Tenant configuration and/or only platform authority.
- Default grant status (default is no grant).
- Audit event and sensitive-data classification.
- API/job/integration/mobile coverage requirements.
- Deprecation/alias/version compatibility behavior.

A permission's meaning must not change silently. A materially broader capability requires a new permission key or a versioned migration with impact analysis.

## 4. Standard action vocabulary

| Action | Meaning | Does not imply |
|---|---|---|
| discover | Discover that a permitted resource/relationship exists | View of all fields/details |
| list | Retrieve a scoped collection of permitted records | Full detail, export or mutation |
| view | Read permitted fields/details of a resource | Update, approve, export |
| view_sensitive | Read a declared sensitive field/data category | General resource access |
| create | Create a draft/new business record | Submit, post or approve |
| update_draft | Change an eligible unfinalized record | Rewrite posted history |
| submit | Submit a record/work item into a controlled workflow | Approve or post |
| assign | Assign work/resource/ownership within declared policy | Permission administration |
| execute | Perform an assigned operational task/action | Approve own work or access unrelated data |
| capture | Record an observation/evidence value | Accept, approve or post the resulting effect |
| verify | Verify a specific evidence/source condition | Complete reconciliation or financial posting unless separately defined |
| approve | Approve a specified action/resource under a defined policy | Arbitrary mutation, export or self-approval |
| reject | Reject a submitted item with reason/evidence | Delete it or erase history |
| post | Commit a validated operational/financial posting | Edit an already posted record |
| reverse | Create a controlled linked reversal/correction | Delete/overwrite original history |
| reconcile | Match and resolve defined differences | Change authoritative source evidence silently |
| export | Generate/download permitted dataset | Permission to view fields not otherwise authorized |
| configure | Change a declared configuration key within scope and allowed values | Change fixed invariants or permissions beyond delegated authority |
| manage_access | Administer membership/permission assignments within delegated ceiling | Self-escalation or access beyond own authority |
| administer | Perform an explicitly defined domain administration operation | Unrestricted platform or Tenant bypass |

Action independence is mandatory: view never implies update_draft; capture never implies approve; approve never implies post; post never implies reverse; list never implies export; configure never implies manage_access.

## 5. Standard scope vocabulary

| Scope | Meaning |
|---|---|
| Platform | SaaS platform operations only; no implicit Tenant data access |
| Tenant | One identified Tenant; no cross-Tenant implication |
| Branch | One or more explicitly enumerated Branches in the same Tenant |
| OperatingLocation | A specific physical/operational site |
| StockLocation | A specific inventory-control location with stock-owning Branch attribution |
| Assigned resource/task | A named task, order, run, record, queue item or resource assigned to the actor |
| Resource relationship | A defined relationship such as customer-service, supplier, farmer or account association |
| Data category/field | A declared sensitive field group such as payroll, bank details, identity evidence or tax identifiers |
| Amount/quantity limit | A validated maximum value/quantity or range for the specific action |
| Time window | Effective start/end, business calendar or permitted operating window |
| Report/dataset | A named report definition, data domain and row/field scope |
| Service identity/integration | One named Tenant-bound service connection and declared contract |

TenantMembership and Branch/workforce assignment remain prerequisite context, not permission. An un-attributed Tenant master record is not public to every member. If its visibility policy is not explicit, access defaults to deny.

## 6. Cross-module permission families

The following is the product-wide catalogue baseline. Resource-specific permissions are created from these families as each module's workflows are detailed. This table is a governed permission vocabulary, not an automatic set of permissions granted to any customer.

| Domain | Permission families / representative keys | Important separation |
|---|---|---|
| Tenant and Branch administration | tenant.profile.view/configure; branch.view/create/configure/archive; location.view/configure; capability.activate/deactivate | Tenant activation/configuration does not grant user access or bypass platform invariants |
| Membership and access | membership.invite/activate/suspend/revoke; access.permission.view/assign/restrict/revoke; access.bundle.configure; access.delegation.create/revoke | Each action is separately protected; assignment cannot exceed the actor's delegation |
| Party/identity | party.view/create/update_draft/identity.resolve; party.contact.view_sensitive; party.bank_details.view_sensitive; party.identity_document.view_sensitive | Tenant Party identity does not imply full Branch relationship or sensitive-field visibility |
| Customer/account | customer.account.view/create/update_draft/credit.configure; customer.service_address.view/configure | Address, transactions and credit terms may have narrower scope |
| Subscription/schedule | subscription.view/create/update_draft/submit/approve/cancel; subscription.schedule.configure/generate; subscription.occurrence.skip/substitute | Agreement changes, occurrence exceptions, generation and billing are distinct |
| Sales/POS | sales.order.view/create/update_draft/submit; sales.sale.capture/post; sales.discount.apply/approve; sales.refund.submit/approve/post | POS client does not grant access; discount/refund approval is separate from capture |
| Delivery/fulfilment | delivery.route.view/create/update_draft/publish/archive; delivery.plan.create/allocate; delivery.run.view/plan/assign/reassign/dispatch/execute/hold/resume/close; delivery.stop.view/execute/outcome.capture; delivery.exception.review/resolve; transport_resource.view/reserve/assign/release; delivery.qualification.view/record/verify | Route ownership, Branch/service-area assignment, workforce assignment, transport-resource readiness, dispatch authorization, execution and outcome posting are separate; duty labels confer no permission |
| Procurement | procurement.request.create/submit; procurement.order.approve; procurement.receipt.capture/accept/reject/post; procurement.supplier_invoice.view/verify | Order, physical receipt, quality acceptance and payable posting are distinct |
| Farmer collection | collection.entry.capture/review/accept/reject; collection.sample.create/test.capture/result.verify; farmer.rate_chart.configure/approve; farmer.settlement.calculate/approve/post | Measurement, test result, quality decision, rate calculation and settlement are distinct |
| Product/catalogue | product.view/create/update_draft/archive; product.uom.configure; product.tax_profile.configure | Master-data editing cannot rewrite historical transaction snapshots |
| Pricing | pricing.list.view/configure/publish; pricing.override.apply/approve; pricing.discount.configure | Price configuration, transaction override and approval are distinct |
| Inventory | inventory.stock.view/receive/put_away/pick/issue/transfer; inventory.reservation.create/release; inventory.count.capture; inventory.variance.review/approve; inventory.adjustment.submit/approve/post; inventory.write_off.submit/approve/post; inventory.quarantine/apply/release | See ARC-AUTH-MATRIX-001; stock ownership and task scope remain mandatory |
| Lot/traceability/recall | traceability.lot.view/trace; traceability.hold.apply/release; traceability.recall.initiate/approve/close | Traceability access does not grant stock disposition or unrelated customer data |
| Processing/production | production.recipe.view/create/update_draft/approve/publish; production.batch.create/execute/quality_release/post; production.variance.review/approve | Recipe authoring, batch execution, quality release and posting are distinct |
| Billing/receivables | billing.preview/create/issue/post; billing.credit_note.submit/approve/post; receivable.view/allocate/reconcile | Statement viewing does not grant invoice mutation or payment receipt |
| Payments/cash/bank | payment.capture/record/verify/allocate/refund; cash.float.view/manage/count; bank.statement.import/reconcile; bank_account.view_sensitive/configure | Manual entry, provider verification, bank reconciliation and allocation are distinct |
| Accounting | finance.journal.view/create_draft/submit/approve/post/reverse; finance.period.close/reopen; finance.accounting_book.configure; finance.coa.configure | Period, ledger, sensitive bank/tax info and posting privileges are independent |
| Tax | tax.profile.view/configure; tax.classification.view/configure/approve; tax.register.view/export | Configuration cannot certify legal applicability or bypass effective statutory rules |
| Expenses/petty cash | expense.claim.create/submit/approve/reject/post; petty_cash.float.manage/count/reconcile | Claim submit, approval, payout and cash custody are distinct |
| Workforce/HR | workforce.person.view/create/update; workforce.assignment.configure; attendance.capture/approve; leave.submit/approve | Workforce assignment is not a stock permission or data visibility grant |
| Payroll | payroll.view/prepare/review/approve/post; payroll.salary.view_sensitive; payroll.bank_details.view_sensitive | Payroll and employee financial data require separate sensitive permissions |
| Assets/maintenance | asset.view/create/update/assign; maintenance.work_order.create/assign/execute/close | Asset custody and work execution do not grant finance or stock adjustment |
| Custody/shared resources | custody.view/capture/assignment.create/assignment.revoke/handover.offer/dispatch/receive/accept/dispute/resolve/incident.report/incident.review; shared_resource.view/reserve/release/use.capture; asset.cleaning.capture/calibration.capture | Custody subject, physical custodian, actual handler, stock ownership, permission and workforce assignment remain separate; evidence is profile-versioned; high-risk resolution/asset disposition needs separate approval |
| Reports/analytics | report.view; report.dataset.domain.view; report.export; analytics.kpi.configure; forecast.run/view; budget.create/submit/approve | Row/field scope, formula version and exports are separate |
| Files/documents | file.upload/view/download/share/delete_draft; document.template.configure | Parent-resource permission and document sensitivity both apply; signed URL is not a general access token |
| Integration/API | integration.connection.configure; integration.sync.execute/reconcile; integration.webhook.manage; api_credential.create/revoke | Named service identity, Tenant binding, scope and external contract version required |
| Extensions | extension.install/configure/enable/disable/revoke; extension.data_access.grant/revoke | Extension installation never grants blanket data access |
| Audit/security | audit.event.view/export; security.session.revoke; security.incident.report/review; security.break_glass.activate/review | Audit and sensitive event access are separately permissioned; Emergency Override remains ODR-002-specific |

Permission keys above are representative candidates. Before module implementation, the owning domain must validate exact resource semantics and action states. No wildcard such as *.admin or all_permissions is a default grant. If a wildcard is ever supported for convenience, it must expand into explicit catalogue permissions at assignment time, show the full impact preview, require a dedicated permission and never include platform/non-delegable/security permissions implicitly.

## 7. Sensitive data permissions

Treat high-risk fields/data categories separately from ordinary entity visibility. Examples include personal identity documents and verified identifiers; bank account/payee details, payment instruments and statements; salary, compensation, payslips and payroll liabilities; tax identifiers, legal documents and statutory exports; customer contact data where use is purpose-limited; security credentials, recovery evidence and session metadata; and detailed audit/security investigations.

A principal may view a record while sensitive fields remain masked. A permission to export a dataset must enumerate the fields/data classes that can be exported. Sensitive field access is audited with purpose/category and safe references; do not log full secrets or identifiers unnecessarily.

## 8. Assignment and effective-permission model

Tenants may configure permission bundles and editable starter templates; group assignments where group membership is explicit and auditable; direct principal grants/denials where Tenant policy permits; and scope, limits, effective start/end and reason.

Assignment sources are separate concepts:
- Tenant Membership: prerequisite, never grants business access.
- Membership Branch Assignment: workforce context only.
- Role/Permission Bundle Assignment: contributes explicit current contents.
- Direct Permission Assignment: explicitly grants or denies named capability/scope.
- Temporary Task/Workflow Grant: narrowly authorizes a named task/resource.
- ApprovalDelegation: exact approval capability only, subject to ODR-002 section 16.4.
- StockTaskAccessGrant: cross-Branch stock-specific exception, subject to ODR-002 and ARC-AUTH-MATRIX-001.
- Service/Extension Grant: named service identity and bounded integration contract.

Effective access requires valid principal membership/context, one or more valid permission assignments, scope, resource ownership, state, validity, limits and governing policy. A valid scope grant cannot create a missing permission. A permission cannot bypass Tenant ownership or a required task grant.

## 9. Precedence, deny and conflict handling

Apply precedence:
1. Fixed platform/security invariants and verified legal/contractual prohibitions cannot be overridden.
2. Authentication, active Tenant membership, security boundary and resource ownership are prerequisites.
3. An applicable explicit deny overrides an applicable allow for the same action/resource/scope.
4. Valid allows contribute only within their own scopes, limits and periods after all denies and fixed constraints are applied.
5. An assignment cannot exceed the assigning actor's own effective delegation authority.
6. Ambiguous, conflicting or unverifiable policy defaults to deny.
7. Revoked/expired permission versions are revalidated at every protected request and at state-changing commit.

No implicit role inheritance, seniority arithmetic, account-type privilege or Home Branch access is permitted.

## 10. Access administration and safe bootstrap

- Access administration uses dedicated, scoped permissions. A permission administrator can only assign/revoke permissions they are authorized to delegate, within the Tenant/resource scope they control.
- Self-escalation and circular delegation are prohibited. An access change cannot rely on a job title or role name as proof of assignment authority.
- High-impact permission changes should require independent approval under Tenant security policy. The exact action is audited with before/after assignment versions and effective time.
- The first Tenant principal receives an explicit, narrow provisioning assignment through an audited onboarding process. A small owner-operated business can be given all operational capabilities it selects, but not an invisible bypass for Tenant isolation, non-delegable actions or mandatory independent controls.
- Emergency recovery is a separately controlled, time-limited, audited procedure; no permanent hidden superuser is created.

## 11. Service identities and extensions

Jobs, webhooks, API integrations, Tally agent and extensions use named non-human identities. Each identity must be Tenant-bound where applicable, least-privileged, scope-limited, revocable, secret-rotatable, versioned and audited. A service identity cannot borrow the permission set of the initiating human indefinitely; it can only execute declared actions under its own approved contract and required actor/correlation attribution.

## 12. Compatibility, audit and lifecycle

Permission catalogue keys have stable meanings and owners. Removing or materially narrowing a permission requires dependency analysis of APIs, UI, reports, jobs, clients, extensions and historical assignment references. Deprecated permissions remain resolvable during a controlled migration; they must not silently map to broader permissions. Every change records actor, target, permission/bundle, scope, previous/new version, reason, approval and effective time. Authorization outcomes and denials are observable without logging secrets. Permission revocation invalidates relevant cache/session/queued work and is tested for races with in-flight stock/financial operations. Completed transactions retain historical actor and authorization evidence; revocation does not rewrite completed history.

## 13. Required acceptance tests

1. Role title, job title or account type changes alone do not change the effective permission set.
2. A Tenant can create a custom bundle from permitted catalogue permissions, assign it to a group/principal and remove one permission without disturbing unrelated grants.
3. A principal cannot grant/revoke permissions beyond their own delegation authority.
4. Membership and Home/Primary Branch assignment alone grant no data visibility or action.
5. A user with multiple Tenant memberships cannot cross-read/write without active membership and permissions for the selected Tenant.
6. A view permission cannot mutate through alternate API, bulk, report, mobile, export, job or integration paths.
7. Sensitive fields remain masked without matching field permission even when the parent record is viewable.
8. An explicit deny overrides a conflicting allow; ambiguous scope fails closed.
9. Feature entitlement, capability activation and extension installation never create a user permission.
10. Revocation blocks subsequent protected access across sessions, caches, offline queues, exports and background jobs.
11. Permission version/expiry/revocation racing with a stock or financial commit cannot authorize an uncommitted operation.
12. Maker-checker, non-delegable and Emergency Override policies cannot be bypassed by custom bundles or wildcard convenience grants.

## 14. Domain-specific catalogue completion

Each domain document must add exact action keys and acceptance tests for all implemented lifecycle transitions before that domain enters development. ODR-005 remains open only for this exhaustive module-by-module completion and verified coverage. The permission-first core, Tenant configurability, scope, precedence and cross-Branch matrix are resolved.
