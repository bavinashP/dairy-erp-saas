# Permission Catalogue and Assignment Governance

**Document ID:** ARC-IAM-CAT-001  
**Status:** Recommended permission-first baseline selected under the product owner's delegated decision mandate — 2026-11-19  
**Authority:** ODR-002 sections 16.7 and 16.9; ODR-005; ARC-IAM-001; ARC-AUTH-MATRIX-001; ARC-IAM-CTRL-001; ARC-PV-001; Actor and Access Model; Business Rules Catalogue.  
**Purpose:** Define a consistent, extensible and customer-configurable permission language across all Dairy ERP modules without relying on job titles, role labels or account categories.

## 1. Non-negotiable principle

A user account authenticates an actor. It does not grant business access. A permission is the only source of business action/data authority after the actor's Tenant membership, resource ownership, scope, validity and all governing policies are validated.

No user receives a permission merely because of Tenant ownership or administrator status, a job/business title, a human/employee/contractor/service-account category, a mobile/POS or platform-account type, Home/Primary Branch or workforce assignment, or the client/channel used to sign in.

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

A permission's meaning must not change silently. A materially broader capability requires a new permission key or a versioned migration with impact analysis. Domain-level approval requirements and risk/threshold defaults are governed by [Domain Permission and Approval Control Matrix](DOMAIN_PERMISSION_AND_APPROVAL_CONTROL_MATRIX.md); that document cannot redefine or grant keys. A read key's metadata must state whether it covers list/detail/query surfaces and which fields/rows are returned. A collection, search or discovery operation must map either to an explicit `resource.list`/`resource.discover` key or to a `resource.view` entry whose metadata explicitly includes that query surface; no implicit query privilege is inferred. Sensitive fields and exports remain independently controlled.

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

The following examples use exact, registered keys from section 15.2. This table is an index only; section 15.2 is the canonical inventory.

| Domain | Representative canonical keys | Important separation |
|---|---|---|
| Tenant and Branch administration | `tenant.profile.view`; `organization.branch.view`; `organization.operating_location.configure`; `organization.operational_capability.activate` | Tenant activation/configuration does not grant user access or bypass platform invariants |
| Membership and access | `identity.membership.invite`; `access.permission.assign`; `access.bundle.configure`; `approval.delegation.create` | Assignment cannot exceed the actor's delegated authority; approval delegation is separate |
| Party/identity | `party.profile.view`; `party.identity_resolution.submit`; `party.contact.view`; `party.bank_details.view_sensitive` | Identity resolution and sensitive-field visibility are separately permissioned |
| Customer/account | `customer.account.view`; `customer.service_address.view`; `customer.credit_override.approve` | Address, transactions and credit terms may have narrower scope |
| Subscription/schedule | `subscription.agreement.view`; `subscription.schedule.configure`; `subscription.schedule_exception.skip`; `subscription.schedule_occurrence.allocate` | Agreement changes, occurrence exceptions, generation and billing are distinct |
| Sales/POS | `sales.customer_order.view`; `sales.sale.capture`; `sales.sale.post`; `sales.discount.approve`; `sales.refund.post` | POS client does not grant access; capture, post, return and refund approval are distinct |
| Delivery/fulfilment | `delivery.route.view`; `delivery.plan.allocate`; `delivery.run.dispatch`; `delivery.stop.outcome_capture`; `delivery.transport_resource.assign` | Route ownership, Branch/service-area assignment, workforce assignment, transport-resource readiness and dispatch are separate |
| Procurement | `procurement.request.submit`; `procurement.order.approve`; `procurement.receipt.capture`; `procurement.supplier_invoice.verify` | Order approval, physical receipt, quality acceptance and payable posting are distinct |
| Farmer collection | `collection.entry.capture`; `collection.measurement.verify`; `collection.test.result_verify`; `farmer.rate_chart.approve`; `farmer.settlement.post` | Measurement, test result, quality decision, rate calculation and settlement are distinct |
| Product/catalogue | `product.master.view`; `product.uom.configure`; `product.unit_conversion.approve`; `product.tax_profile.configure` | Master-data editing cannot rewrite historical transaction snapshots |
| Pricing | `pricing.price_list.view`; `pricing.price_list.publish`; `pricing.override.apply`; `pricing.override.approve` | Price configuration, publication, transaction override and approval are distinct |
| Inventory | `inventory.stock.view`; `inventory.stock.receive`; `inventory.count.capture`; `inventory.variance.approve`; `inventory.quarantine.release`; `inventory.stock_valuation.view_sensitive` | Count capture is not variance approval; quarantine is not release; handling is not adjustment/write-off or valuation approval |
| Cross-Branch stock-task grants | `inventory.stock_task_grant.create`; `inventory.stock_task_grant.approve`; `inventory.stock_task_grant.revoke`; `inventory.stock_task_grant.reconcile` | Grant administration never creates the worker's independent inventory permissions |
| Emergency stock handling | `inventory.emergency_override.activate`; `inventory.emergency_override.extend`; `inventory.emergency_override.review` | Protective break-glass is limited by ODR-002 and is not a general administrator bypass |
| Lot/traceability/recall | `traceability.lot.trace`; `traceability.hold.apply`; `traceability.recall.initiate`; `traceability.recall.approve` | Traceability access does not grant stock disposition or unrelated customer data |
| Processing/production | `production.recipe.approve`; `production.batch.execute`; `production.quality_release.approve`; `production.variance.approve` | Recipe authoring, batch execution, quality release and posting are distinct |
| Billing/receivables | `billing.document.preview`; `billing.document.issue`; `billing.statement.export`; `billing.receivable.reconcile` | Statement viewing does not grant invoice mutation or payment receipt |
| Payments/cash/bank | `payments.transaction.record`; `payments.transaction.verify`; `payments.refund.approve`; `cash.handover.accept`; `bank.statement.reconcile` | Manual entry, external verification, bank reconciliation and allocation are distinct |
| Accounting | `finance.journal.post`; `finance.journal.reverse`; `finance.period.close`; `finance.tally_sync.execute` | Period, ledger, sensitive bank/tax info and posting privileges are independent |
| Tax | `tax.profile.configure`; `tax.classification.approve`; `tax.register.export` | Configuration cannot certify legal applicability or bypass effective statutory rules |
| Expenses/petty cash | `expense.claim.submit`; `expense.claim.approve`; `cash_management.petty_cash.reconcile` | Claim submit, approval, accounting post, payment and physical cash custody are separate |
| Workforce/HR | `workforce.person.view`; `workforce.assignment.configure`; `workforce.attendance.approve`; `workforce.leave.approve` | Workforce assignment is not a stock permission or data visibility grant |
| Payroll | `payroll.run.prepare`; `payroll.run.approve`; `payroll.salary.view_sensitive`; `payroll.run.statutory_export` | Payroll and employee financial data require separate sensitive permissions |
| Assets/maintenance | `asset.asset.view`; `maintenance.work_order.execute`; `asset.calibration.verify`; `asset.shared_resource.use_capture` | Asset custody and work execution do not grant finance or stock adjustment |
| Custody/shared resources | `custody.event.capture`; `custody.handover.accept`; `custody.incident.resolve`; `asset.shared_resource.reserve` | Custody subject, physical custodian, actual handler, stock ownership, permission and workforce assignment remain separate |
| Reports/analytics | `analytics.report.view`; `analytics.report.export`; `analytics.kpi.configure`; `analytics.forecast.run`; `finance.budget.approve` | Row/field scope, formula version and exports are separate |
| Files/documents | `document.file.upload`; `document.file.download`; `document.template.configure` | Parent-resource permission and document sensitivity both apply; signed URL is not a general access token |
| Integration/API | `integration.connection.configure`; `integration.sync.execute`; `integration.api_credential.create`; `integration.data_export.download` | Named service identity, Tenant binding, scope and external contract version required |
| Extensions | `extension.package.install`; `extension.package.configure`; `extension.data_access.grant` | Extension installation never grants blanket access |
| Audit/security/privacy | `audit.event.view`; `security.session.revoke`; `security.incident.review`; `privacy.legal_hold.release` | Audit and sensitive event access are separately permissioned; Emergency Override remains ODR-002-specific |

### 6.1 Delivery and transport action independence

For delivery workflows, the catalogue's representative permissions are distinct:
- Route view/create/update/publish/archive and Branch-ServiceArea coverage management.
- DeliveryPlan create, demand allocate/split and plan submit.
- DeliveryRun plan, assign, reassign, dispatch, execute, hold, resume, close and close-with-exceptions.
- DeliveryStop view/execute/outcome capture and proof/evidence capture.
- Delivery exception review/resolve and correction/reopen.
- Transport resource view/reserve/assign/release and vehicle-readiness evidence review.
- Driver/participant qualification evidence view/record/verify where enabled.

An operational duty label (driver, handler, helper, accountable run assignee) is not an access grant. Assigning a person to a run does not grant permission to dispatch, view unrelated customer records, collect payments, change subscriptions, alter prices or close the run. Vehicle assignment does not grant run/customer data access. Route ownership and ServiceArea assignment do not confer cross-Branch membership. Cross-Branch delivery follows ARC-AUTH-MATRIX-001 section 4.1 and ODR-002 section 16.37. A StockTaskAccessGrant never substitutes for a DeliveryRun personnel assignment or delivery permission.

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

## 14. Domain-specific catalogue completion and change governance

The product-wide v1 atomic key inventory is specified in section 15. A domain specification may introduce an additional resource/action key only when it records the owning domain, canonical resource, exact action semantics, scope types, risk/delegation/approval metadata, sensitive-data treatment, audit events, cross-channel enforcement and acceptance tests. New keys are additive and reviewed for overlap with existing keys; the same business action must not receive competing keys in different modules.

A key's semantic meaning is immutable after publication. A broader action or materially different data disclosure requires a new key or a formally versioned compatibility migration. Deprecated keys remain resolvable during the declared compatibility window but cease granting authority after retirement. Unknown, malformed, unregistered or retired keys default to deny.

ODR-005 remains partially open only for exact resource/state/API mapping, sensitive-field classification and verified automated coverage as each workflow is finalized. Numeric thresholds are Tenant-specific configuration values governed by ARC-IAM-CTRL-001, not universal product constants. The canonical v1 permission inventory and governance baseline are selected in section 15; the domain approval-control baseline is ARC-IAM-CTRL-001. This does not mean permissions are automatically assigned or that every module is enabled for every Tenant.


## 15. ODR-005 — product-wide atomic permission catalogue v1

**Status:** Recommended v1 catalogue baseline selected on 2026-11-19 under the product owner's delegated decision mandate. The inventory is a stable permission vocabulary for requirements and future implementation planning; it does not grant permissions, approve commercial entitlements, determine legal applicability or complete every module's approval thresholds.

**Authority:** ODR-005; ODR-002 sections 16.7 and 16.9; ARC-PV-001; ARC-AUTH-MATRIX-001; this document's sections 1–14; FRS; ERS; Business Rules Catalogue; domain specifications listed in the RTM.

### 15.1 Catalogue-wide rules

1. Every key shown below is a separate atomic permission. A slash-separated list is only a compact way of listing multiple independent keys; it is not a composite privilege.
2. Keys use the stable, language-neutral form `domain.resource.action`. The key is not a display label and must not change for translation, customer terminology or UI customization.
3. The canonical catalogue is platform-governed. Tenants may create, edit, version and retire their own permission bundles, assign eligible keys directly to principals/groups, create scoped allow/deny assignments and configure permitted limits. A Tenant cannot invent an unregistered core permission key, redefine a key's meaning, weaken its fixed constraints or create an implicit wildcard permission.
4. Extensions may register namespaced permission keys under a versioned extension contract. An extension key grants access only to that extension's declared resources/actions. It does not automatically grant access to core Tenant, Branch, customer, stock, payment, payroll, audit or security data.
5. No permission is granted by default. Tenant activation, feature entitlement, account category, job title, role name, seniority, Home/Primary Branch, user client or physical presence cannot grant a key.
6. An unregistered, malformed, retired, incompatible or unverifiable permission key is denied. A renamed/deprecated key requires an explicit compatibility mapping and sunset date; it must never silently widen authority.
7. The key inventory below covers the product's current capability map, including optional modules. Availability of a module is separately controlled by Tenant capability/plan entitlement; enabled capability still does not assign a user permission.
8. The current product does not include customer self-service login. The customer-facing account/contact/address records below are accessed by authorized business personnel and service identities only.
9. Platform-only permissions are separated from Tenant-assignable permissions. Platform support access to Tenant data requires a separate scoped SupportAccessGrant and audit evidence; a platform identity does not have blanket Tenant access.
10. Product/domain owners must map each key to its authoritative API commands, jobs, events, reports, fields and state transitions before implementation. UI visibility is never the enforcement boundary.

### 15.2 Canonical v1 module/resource/action inventory

All listed keys are individually assignable and scope-controlled unless marked platform-only. A resource's default scope is its owning Tenant and applicable Branch/location/resource; the detailed scope metadata for each key is defined by section 3 and its owning domain specification.

| Domain / resource | Canonical atomic permission keys | Default scope and important boundary |
|---|---|---|
| Platform Tenant lifecycle | `platform.tenant.view`; `platform.tenant.provision`; `platform.tenant.activate`; `platform.tenant.suspend`; `platform.tenant.reactivate`; `platform.tenant.close` | **Platform-only.** Does not grant access to Tenant business data. |
| Platform plans and entitlements | `platform.plan.view`; `platform.plan.configure`; `platform.plan.publish`; `platform.plan.retire`; `platform.entitlement.view`; `platform.entitlement.assign`; `platform.entitlement.revoke`; `platform.feature_catalogue.configure` | **Platform-only.** Capability availability and usage quotas are not user permissions. |
| Platform support | `platform.support_grant.create`; `platform.support_grant.approve`; `platform.support_grant.activate`; `platform.support_grant.revoke`; `platform.support_grant.review` | **Platform-only.** Each grant is Tenant-, purpose-, resource-, action- and time-scoped; no silent/global bypass. |
| Tenant profile and settings | `tenant.profile.view`; `tenant.profile.configure`; `tenant.business_profile.view`; `tenant.business_profile.configure`; `tenant.operating_profile.view`; `tenant.operating_profile.configure`; `tenant.configuration.view`; `tenant.configuration.configure` | Tenant scope. Fixed platform/security invariants cannot be configured away. |
| Branch and physical organization | `organization.branch.view`; `organization.branch.create`; `organization.branch.update_draft`; `organization.branch.activate`; `organization.branch.deactivate`; `organization.branch.archive`; `organization.operating_location.view`; `organization.operating_location.create`; `organization.operating_location.update_draft`; `organization.operating_location.activate`; `organization.operating_location.deactivate`; `organization.operating_location.archive`; `operating_location.branch_association.configure`; `organization.operational_capability.view`; `organization.operational_capability.activate`; `organization.operational_capability.deactivate`; `organization.operational_capability.configure` | Tenant/Branch/OperatingLocation scope. Co-location does not merge ownership or access. |
| Service areas, calendars and shifts | `organization.service_area.view`; `organization.service_area.create`; `organization.service_area.update_draft`; `organization.service_area.archive`; `organization.branch_service_area_assignment.view`; `organization.branch_service_area_assignment.create`; `organization.branch_service_area_assignment.update`; `organization.branch_service_area_assignment.end`; `organization.business_calendar.view`; `organization.business_calendar.configure`; `organization.business_shift.view`; `organization.business_shift.configure` | Tenant/Branch/ServiceArea scope with effective dates. Geographic coverage never grants cross-Branch access. |
| Membership and access administration | `identity.membership.view`; `identity.membership.invite`; `identity.membership.activate`; `identity.membership.suspend`; `identity.membership.revoke`; `access.bundle.view`; `access.bundle.create`; `access.bundle.update_draft`; `access.bundle.publish`; `access.bundle.retire`; `access.group.view`; `access.group.create`; `access.group.update_draft`; `access.group.add_member`; `access.group.remove_member`; `access.group.archive`; `access.permission.view`; `access.permission.assign`; `access.permission.restrict`; `access.permission.revoke`; `access.assignment.view`; `access.assignment.expire`; `access.role_assignment.assign`; `access.role_assignment.revoke`; `access.delegation.create`; `access.delegation.revoke`; `access.review.execute`; `access.review.close` | Tenant/Branch/permission scope. Assignment authority cannot exceed the actor's effective delegation ceiling; self-escalation is prohibited. |
| Party identity and identity resolution | `party.profile.view`; `party.profile.list`; `party.profile.create`; `party.profile.update_draft`; `party.profile.archive`; `party.identity_resolution.submit`; `party.identity_resolution.approve`; `party.identity_resolution.reject`; `party.identity_resolution.unmerge`; `party.contact.view`; `party.contact.update_draft`; `party.address.view`; `party.address.update_draft`; `party.identity_document.view_sensitive`; `party.identity_document.upload`; `party.identity_document.download_sensitive`; `party.identity_document.verify`; `party.bank_details.view_sensitive`; `party.bank_details.update_draft`; `party.bank_details.verify` | Tenant plus permitted Branch relationship. Sensitive identifiers/documents and bank data require separate permissions; merge/unmerge requires governed review and preserves posted history. |
| Customer account and commercial relationship | `customer.account.view`; `customer.account.create`; `customer.account.update_draft`; `customer.account.suspend`; `customer.account.close`; `customer.service_address.view`; `customer.service_address.configure`; `customer.credit_limit.view`; `customer.credit_limit.configure`; `customer.credit_override.apply`; `customer.credit_override.approve`; `customer.communication_preference.view`; `customer.communication_preference.configure` | Tenant/Branch/customer relationship. Contact and address visibility is minimum-necessary and separately permissioned. |
| Supplier and farmer master | `supplier.account.view`; `supplier.account.create`; `supplier.account.update_draft`; `supplier.account.suspend`; `supplier.account.close`; `supplier.terms.view`; `supplier.terms.configure`; `supplier.terms.approve`; `farmer.profile.view`; `farmer.profile.create`; `farmer.profile.update_draft`; `farmer.membership.configure` | Tenant/Branch/supplier or farmer relationship. Supplier/farmer status does not imply payment, settlement or rate approval. |
| Customer subscriptions and schedules | `subscription.agreement.view`; `subscription.agreement.create`; `subscription.agreement.update_draft`; `subscription.agreement.submit`; `subscription.agreement.approve`; `subscription.agreement.suspend`; `subscription.agreement.resume`; `subscription.agreement.cancel`; `subscription.agreement.reinstate`; `subscription.schedule.view`; `subscription.schedule.configure`; `subscription.schedule.publish`; `subscription.schedule.generate`; `subscription.schedule_exception.create`; `subscription.schedule_exception.approve`; `subscription.schedule_exception.skip`; `subscription.schedule_exception.extra_supply`; `subscription.schedule_exception.substitute`; `subscription.schedule_exception.override`; `subscription.schedule_occurrence.view`; `subscription.schedule_occurrence.allocate`; `subscription.schedule_occurrence.hold`; `subscription.schedule_occurrence.release` | Tenant/customer/Branch/service-date scope. Schedule changes and occurrence exceptions are distinct; generated/executed history is immutable. |
| Orders, sales and POS | `sales.customer_order.view`; `sales.customer_order.create`; `sales.customer_order.update_draft`; `sales.customer_order.submit`; `sales.customer_order.approve`; `sales.customer_order.cancel`; `sales.sale.view`; `sales.sale.capture`; `sales.sale.post`; `sales.sale.void`; `sales.discount.apply`; `sales.discount.approve`; `sales.return.capture`; `sales.return.submit`; `sales.return.approve`; `sales.return.post`; `sales.refund.submit`; `sales.refund.approve`; `sales.refund.post`; `pos.register.view`; `pos.register.configure`; `pos.session.open`; `pos.session.close`; `pos.session.reconcile` | Tenant/Branch/register/session/transaction scope. Capture, post, void, return, discount approval and refund posting are separate. |
| Delivery and fulfilment | `delivery.route.view`; `delivery.route.create`; `delivery.route.update_draft`; `delivery.route.publish`; `delivery.route.archive`; `delivery.plan.create`; `delivery.plan.allocate`; `delivery.plan.split`; `delivery.plan.submit`; `delivery.run.view`; `delivery.run.plan`; `delivery.run.assign`; `delivery.run.reassign`; `delivery.run.dispatch`; `delivery.run.execute`; `delivery.run.hold`; `delivery.run.resume`; `delivery.run.close`; `delivery.run.close_with_exceptions`; `delivery.stop.view`; `delivery.stop.execute`; `delivery.stop.outcome_capture`; `delivery.exception.review`; `delivery.exception.resolve`; `delivery.exception.reopen`; `delivery.proof.capture`; `delivery.proof.view_sensitive`; `delivery.transport_resource.view`; `delivery.transport_resource.reserve`; `delivery.transport_resource.assign`; `delivery.transport_resource.release`; `delivery.qualification.view`; `delivery.qualification.record`; `delivery.qualification.verify` | Branch/Route/ServiceArea/DeliveryPlan/Run/Stop/transport resource. Route ownership, personnel assignment, vehicle, dispatch, proof and close remain separate. |
| Procurement and supplier invoices | `procurement.request.view`; `procurement.request.create`; `procurement.request.update_draft`; `procurement.request.submit`; `procurement.request.approve`; `procurement.request.cancel`; `procurement.order.view`; `procurement.order.create`; `procurement.order.update_draft`; `procurement.order.submit`; `procurement.order.approve`; `procurement.order.release`; `procurement.order.close`; `procurement.order.cancel`; `procurement.receipt.capture`; `procurement.receipt.accept`; `procurement.receipt.reject`; `procurement.receipt.post`; `procurement.return.create`; `procurement.return.approve`; `procurement.return.dispatch`; `procurement.return.close`; `procurement.supplier_invoice.view`; `procurement.supplier_invoice.verify`; `procurement.supplier_invoice.approve`; `procurement.supplier_invoice.post` | Tenant/Branch/supplier/order/receipt/StockLocation. Purchase approval, physical receipt, quality acceptance, supplier invoice verification and payable posting are distinct. |
| Milk collection and measurement | `collection.session.view`; `collection.session.create`; `collection.session.open`; `collection.session.close`; `collection.session.reconcile`; `collection.entry.capture`; `collection.entry.update_draft`; `collection.entry.verify`; `collection.entry.accept`; `collection.entry.reject`; `collection.measurement.capture`; `collection.measurement.verify`; `collection.sample.create`; `collection.test.capture`; `collection.test.result_verify`; `collection.test.retest` | Tenant/Branch/collection centre/session/farmer/entry/sample/device. Measurement capture is not quality acceptance or supplier settlement. |
| Quality, food safety and corrective action | `quality.profile.view`; `quality.profile.configure`; `quality.profile.publish`; `quality.result.view`; `quality.result.capture`; `quality.result.verify`; `quality.hold.apply`; `quality.hold.release`; `quality.nonconformance.report`; `quality.nonconformance.review`; `quality.nonconformance.resolve`; `food_safety.checklist.configure`; `food_safety.checklist.execute`; `food_safety.checklist.verify`; `sanitation.record.capture`; `sanitation.record.verify`; `quality.corrective_action.create`; `quality.corrective_action.assign`; `quality.corrective_action.verify`; `quality.corrective_action.close` | Tenant/site/process/product/lot/evidence. Hold, release, recall, disposition and statutory applicability are separate; mandatory verified controls cannot be disabled. |
| Custody, handover and shared-resource accountability | `custody.subject.view`; `custody.assignment.create`; `custody.assignment.revoke`; `custody.event.capture`; `custody.handover.offer`; `custody.handover.dispatch`; `custody.handover.receive`; `custody.handover.accept`; `custody.handover.dispute`; `custody.handover.resolve`; `custody.evidence.capture`; `custody.evidence.view_sensitive`; `custody.incident.report`; `custody.incident.review`; `custody.incident.resolve` | Tenant/Branch/OperatingLocation/subject/handover/resource/task. Physical custody, stock movement, ownership, permission and workforce assignment are separate; missing acknowledgement never implies acceptance. |
| Emergency stock-handling override | `inventory.emergency_override.activate`; `inventory.emergency_override.extend`; `inventory.emergency_override.revoke`; `inventory.emergency_override.review` | Stock-owning Branch/OperatingLocation/StockLocation/affected stock/task/action/time. Critical, narrowly scoped and non-delegable; governed by ODR-002 section 16.3, not a general platform break-glass bypass. |
| Approval delegation | `approval.delegation.view`; `approval.delegation.create`; `approval.delegation.revoke` | Tenant/Branch/category/action/resource/limits/period. Creation requires the delegator's underlying approval permission plus this separate delegation permission; no onward delegation or non-delegable actions. |
| Tax and statutory reporting | `tax.profile.view`; `tax.profile.configure`; `tax.classification.view`; `tax.classification.configure`; `tax.classification.approve`; `tax.register.view`; `tax.register.export` | Tenant/legal-entity/Branch/transaction/period. Tax permissions do not establish legal applicability or authorize statutory filing where a separate filing workflow is required. |
| Farmer rates, advances and settlement | `farmer.rate_chart.view`; `farmer.rate_chart.configure`; `farmer.rate_chart.submit`; `farmer.rate_chart.approve`; `farmer.rate_chart.publish`; `farmer.settlement.view`; `farmer.settlement.calculate`; `farmer.settlement.review`; `farmer.settlement.approve`; `farmer.settlement.post`; `farmer.settlement.reverse`; `farmer.advance.view`; `farmer.advance.create`; `farmer.advance.approve`; `farmer.advance.settle`; `farmer.deduction.configure`; `farmer.deduction.apply`; `farmer.deduction.approve` | Tenant/Branch/farmer/rate period/collection source. Rates, deductions, calculation, approval, posting and payment are separate. |
| Product, SKU, UOM and packaging | `product.master.view`; `product.master.create`; `product.master.update_draft`; `product.master.archive`; `product.variant.view`; `product.variant.configure`; `product.sku.view`; `product.sku.configure`; `product.uom.view`; `product.uom.configure`; `product.unit_conversion.configure`; `product.unit_conversion.approve`; `product.package.view`; `product.package.configure`; `product.tax_profile.view`; `product.tax_profile.configure`; `product.barcode.assign`; `product.barcode.revoke` | Tenant/catalogue and applicable Branch scope. Master-data changes cannot rewrite historic transaction snapshots; tax configuration does not certify legal applicability. |
| Pricing and commercial policies | `pricing.price_list.view`; `pricing.price_list.create`; `pricing.price_list.update_draft`; `pricing.price_list.approve`; `pricing.price_list.publish`; `pricing.price_list.retire`; `pricing.discount_policy.view`; `pricing.discount_policy.configure`; `pricing.discount_policy.approve`; `pricing.override.apply`; `pricing.override.approve`; `pricing.deposit_policy.configure` | Tenant/Branch/customer segment/product/date/transaction. Configuration, publication, transaction override and override approval are separate. |
| Inventory, reservations, counts and valuation | `inventory.stock.view`; `inventory.stock.receive`; `inventory.stock.put_away`; `inventory.stock.pick`; `inventory.stock.issue`; `inventory.stock.transfer`; `inventory.stock.return`; `inventory.reservation.create`; `inventory.reservation.release`; `inventory.count.plan`; `inventory.count.capture`; `inventory.count.submit`; `inventory.variance.review`; `inventory.variance.approve`; `inventory.adjustment.submit`; `inventory.adjustment.approve`; `inventory.adjustment.post`; `inventory.adjustment.reverse`; `inventory.write_off.submit`; `inventory.write_off.approve`; `inventory.write_off.post`; `inventory.quarantine.apply`; `inventory.quarantine.release`; `inventory.stock_valuation.view_sensitive`; `inventory.stock_valuation.recalculate`; `inventory.stock_valuation.approve`; `inventory.stock_ledger.export` | Tenant/stock-owning Branch/StockLocation/item/lot/task/quantity. Count capture is not variance approval; quarantine is not release; handling is not adjustment/write-off or valuation approval. |
| Cross-Branch stock-task grants | `inventory.stock_task_grant.view`; `inventory.stock_task_grant.create`; `inventory.stock_task_grant.submit`; `inventory.stock_task_grant.approve`; `inventory.stock_task_grant.reject`; `inventory.stock_task_grant.withdraw`; `inventory.stock_task_grant.revoke`; `inventory.stock_task_grant.review`; `inventory.stock_task_grant.reconcile` | One named worker, one stock-owning Branch, exact task/resource/action/limit/period. Grant administration never creates the worker's independent inventory permissions. |
| Lots, traceability and recall lineage | `traceability.lot.view`; `traceability.lot.trace`; `traceability.lot.export`; `traceability.hold.apply`; `traceability.hold.release`; `traceability.recall.initiate`; `traceability.recall.approve`; `traceability.recall.execute`; `traceability.recall.close` | Tenant/Branch/product/lot/derived-lot/customer-impact scope. Trace access does not grant stock disposition, unrelated customer data or financial write-off. |
| Processing, production and packaging | `production.recipe.view`; `production.recipe.create`; `production.recipe.update_draft`; `production.recipe.approve`; `production.recipe.publish`; `production.order.view`; `production.order.create`; `production.order.submit`; `production.order.approve`; `production.order.release`; `production.order.cancel`; `production.batch.view`; `production.batch.create`; `production.batch.execute`; `production.batch.hold`; `production.batch.resume`; `production.batch.close`; `production.input.consume`; `production.input.verify`; `production.output.capture`; `production.output.verify`; `production.yield.review`; `production.yield.approve`; `production.variance.review`; `production.variance.approve`; `production.quality_release.approve`; `production.packaging.execute`; `production.packaging.approve` | Tenant/Branch/process/recipe/batch/lot. Recipe authoring, batch execution, quality release, yield variance and stock posting are distinct. |
| Billing and receivables | `billing.document.preview`; `billing.document.create`; `billing.document.submit`; `billing.document.approve`; `billing.document.issue`; `billing.document.post`; `billing.document.cancel`; `billing.credit_note.submit`; `billing.credit_note.approve`; `billing.credit_note.issue`; `billing.credit_note.post`; `billing.debit_note.submit`; `billing.debit_note.approve`; `billing.debit_note.issue`; `billing.debit_note.post`; `billing.statement.view`; `billing.statement.generate`; `billing.statement.export`; `billing.receivable.view`; `billing.receivable.allocate`; `billing.receivable.reconcile`; `receivable.adjustment.submit`; `receivable.adjustment.approve`; `receivable.adjustment.post` | Tenant/Branch/customer/accounting period/document. Invoice, statement, receipt, allocation and bank settlement remain distinct. |
| Payments, cash and bank | `payments.transaction.record`; `payments.transaction.capture`; `payments.transaction.verify`; `payments.transaction.allocate`; `payments.refund.submit`; `payments.refund.approve`; `payments.refund.post`; `payments.reversal.submit`; `payments.reversal.approve`; `payments.reversal.post`; `cash.float.view`; `cash.float.create`; `cash.float.assign`; `cash.float.count`; `cash.float.reconcile`; `cash.float.close`; `cash.handover.offer`; `cash.handover.receive`; `cash.handover.accept`; `cash.handover.dispute`; `cash.handover.reconcile`; `bank.account.view_sensitive`; `bank.account.configure`; `bank.statement.import`; `bank.statement.verify`; `bank.statement.reconcile`; `bank.match.create`; `bank.match.approve` | Tenant/Branch/account/cash session/payment evidence. Manual reference entry is not external verification; cash custody is not bank settlement. |
| Accounting and Tally | `finance.accounting_book.view`; `finance.accounting_book.configure`; `finance.coa.view`; `finance.coa.configure`; `finance.coa.publish`; `finance.journal.view`; `finance.journal.create_draft`; `finance.journal.submit`; `finance.journal.approve`; `finance.journal.post`; `finance.journal.reverse`; `finance.posting_rule.view`; `finance.posting_rule.configure`; `finance.posting_rule.publish`; `finance.period.view`; `finance.period.close`; `finance.period.reopen`; `finance.reconciliation.view`; `finance.reconciliation.match`; `finance.reconciliation.approve`; `finance.reconciliation.close`; `finance.subledger.view`; `finance.subledger.reconcile`; `finance.tax_report.view`; `finance.tax_report.export`; `finance.tally_mapping.configure`; `finance.tally_mapping.approve`; `finance.tally_sync.execute`; `finance.tally_sync.cancel`; `finance.tally_sync.retry`; `finance.tally_sync.reconcile` | Tenant/accounting entity/financial book/period/target company. Draft, approval, posting, reversal, period close/reopen and external sync are independent. |
| Expenses and petty cash | `expense.claim.view`; `expense.claim.create`; `expense.claim.update_draft`; `expense.claim.submit`; `expense.claim.approve`; `expense.claim.reject`; `expense.claim.post`; `expense.payment.approve`; `expense.payment.execute`; `petty_cash.float.view`; `petty_cash.float.manage`; `cash_management.petty_cash.count`; `cash_management.petty_cash.reconcile` | Tenant/Branch/cost centre/cash float. Claim, approval, accounting post, payment and physical cash custody are separate. |
| Workforce, HR and attendance | `workforce.person.view`; `workforce.person.create`; `workforce.person.update_draft`; `workforce.person.archive`; `workforce.engagement.view`; `workforce.engagement.create`; `workforce.engagement.update_draft`; `workforce.engagement.end`; `workforce.assignment.view`; `workforce.assignment.configure`; `workforce.assignment.approve`; `workforce.shift.view`; `workforce.shift.configure`; `workforce.shift.publish`; `workforce.attendance.view`; `workforce.attendance.capture`; `workforce.attendance.correct`; `workforce.attendance.approve`; `workforce.leave.view`; `workforce.leave.submit`; `workforce.leave.approve`; `workforce.leave.reject`; `workforce.leave.cancel`; `workforce.document.view_sensitive`; `workforce.document.upload`; `workforce.document.verify` | Tenant/Branch/person/engagement/shift/time. Workforce assignment is not stock permission or general data visibility; statutory status is external-evidence dependent. |
| Payroll and employee financial data | `payroll.run.view`; `payroll.run.prepare`; `payroll.run.review`; `payroll.run.approve`; `payroll.run.post`; `payroll.run.reverse`; `payroll.salary.view_sensitive`; `payroll.salary.configure`; `payroll.bank_details.view_sensitive`; `payroll.payslip.view_sensitive`; `payroll.payslip.publish`; `payroll.run.statutory_export` | Tenant/Branch/payroll period/employee. Sensitive pay and bank details, calculation, approval, post and export are separately permissioned. |
| Assets, facilities and maintenance | `asset.asset.view`; `asset.asset.create`; `asset.asset.update_draft`; `asset.asset.assign`; `asset.asset.decommission`; `maintenance.work_order.create`; `maintenance.work_order.submit`; `maintenance.work_order.approve`; `maintenance.work_order.assign`; `maintenance.work_order.execute`; `maintenance.work_order.review`; `maintenance.work_order.close`; `asset.calibration.record`; `asset.calibration.verify`; `asset.cleaning.record`; `asset.cleaning.verify`; `asset.inspection.record`; `asset.inspection.verify`; `asset.disposal.submit`; `asset.disposal.approve`; `asset.disposal.post`; `asset.shared_resource.view`; `asset.shared_resource.reserve`; `asset.shared_resource.release`; `asset.shared_resource.use_capture` | Tenant/Branch/OperatingLocation/asset/resource/time. Ownership, permitted use, custody, maintenance, calibration and disposal are separate. |
| Compliance and regulatory records | `compliance.record.view`; `compliance.record.create`; `compliance.record.update_draft`; `compliance.record.submit`; `compliance.record.verify`; `compliance.record.approve`; `compliance.record.renew`; `compliance.record.archive`; `compliance.document.view_sensitive`; `compliance.document.download_sensitive`; `compliance.reminder.configure` | Tenant/legal entity/Branch/site/activity and effective period. A permission or uploaded document does not certify legal compliance or applicability. |
| Reports, analytics, forecasting and budgets | `report.definition.view`; `report.definition.configure`; `report.definition.publish`; `analytics.report.view`; `report.dataset.view`; `analytics.report.export`; `analytics.dashboard.view`; `analytics.dashboard.configure`; `analytics.kpi.view`; `analytics.kpi.configure`; `analytics.kpi.approve`; `analytics.kpi.publish`; `analytics.forecast.run`; `analytics.forecast.view`; `analytics.forecast.approve`; `analytics.forecast.export`; `finance.budget.view`; `finance.budget.create`; `finance.budget.update_draft`; `finance.budget.submit`; `finance.budget.approve`; `finance.budget.revise`; `finance.budget.close`; `analytics.scenario.view`; `analytics.scenario.create`; `analytics.scenario.run` | Tenant/Branch/report/dataset/dimension/financial period. Row/field filtering, formula versions, sensitive fields and export permission are distinct. |
| Workflow, approval and notification configuration | `workflow.definition.view`; `workflow.definition.configure`; `workflow.definition.publish`; `approval.request.view`; `approval.request.submit`; `approval.action.approve`; `approval.action.reject`; `approval.policy.view`; `approval.policy.configure`; `notification.template.view`; `notification.template.configure`; `notification.template.publish`; `notification.preference.view`; `notification.preference.configure`; `notification.delivery.view`; `notification.delivery.retry` | Tenant/workflow/event/recipient/category. Approval delegation and provider delivery remain separate; notification never changes business authorization. |
| Files, documents, import and export | `document.file.upload`; `document.file.view`; `document.file.download`; `document.file.share`; `document.file.revoke_share`; `document.file.delete_draft`; `document.template.view`; `document.template.configure`; `integration.data_import.create`; `integration.data_import.validate`; `integration.data_import.commit`; `integration.data_import.cancel`; `integration.data_export.create`; `integration.data_export.approve`; `integration.data_export.download`; `integration.data_export.cancel` | Tenant/parent resource/data class/dataset/field scope. Parent-resource permission, sensitive-field permissions and download-time revalidation all apply. |
| Integrations, APIs and extensions | `integration.connection.view`; `integration.connection.configure`; `integration.connection.activate`; `integration.connection.suspend`; `integration.connection.revoke`; `integration.sync.execute`; `integration.sync.reconcile`; `integration.webhook.configure`; `integration.webhook.revoke`; `integration.api_credential.create`; `integration.api_credential.revoke`; `extension.package.install`; `extension.package.configure`; `extension.package.enable`; `extension.package.disable`; `extension.package.revoke`; `extension.data_access.grant`; `extension.data_access.revoke` | Named Tenant-bound connection/service identity and versioned contract. Extension installation never creates blanket access. |
| Audit, security and privacy operations | `audit.event.view`; `audit.event.export`; `security.session.view`; `security.session.revoke`; `security.incident.report`; `security.incident.review`; `security.incident.resolve`; `security.break_glass.activate`; `security.break_glass.review`; `privacy.data_subject_request.view`; `privacy.data_subject_request.process`; `privacy.legal_hold.apply`; `privacy.legal_hold.release` | Tenant/security boundary and declared incident/data scope. Sensitive investigation, export, security break-glass and legal-hold actions require explicit high-risk authority. `security.break_glass.*` is not the stock-specific `inventory.emergency_override.*` permission family. |

### 15.3 Permission metadata, scope and assignment defaults

Each key in section 15.2 is a catalogue entry, not just a string. Before a key is exposed for assignment, its metadata record must declare:

| Metadata | Required definition |
|---|---|
| Key identity | Stable key, owning domain, resource, action, catalogue version and lifecycle/deprecation state |
| Semantics | Exact allowed operation, state preconditions, prohibited side effects and owning domain specification |
| Scope | Allowed Tenant/Branch/OperatingLocation/StockLocation/record/task/person/report/financial-period/service-identity scopes |
| Limits | Quantity, value, frequency, time window, record count, purpose and other applicable bounds |
| Data sensitivity | Fields/data classes returned or changed, masking rules and export/download treatment |
| Read/query surface | Whether the key covers detail view, collection list, search/discovery, aggregation or drill-down; exact row/field filtering and pagination/query limits |
| Risk class | Standard, elevated, high or critical, with the reason and any mandatory safeguard |
| Delegation | Delegable only where the owning domain explicitly allows it; otherwise non-delegable |
| Approval | Whether approval is never required, policy-configurable or mandatory; required independence/maker-checker and approver scope |
| Assignment eligibility | Tenant-assignable, platform-only or extension-registered; which assignment mechanisms are allowed |
| Audit | Required event types, before/after evidence, denial reason and correlation/idempotency references |
| Execution channels | API, web, mobile/offline, POS, barcode/device, job, report/export and integration enforcement points |
| Compatibility | Introduction/version, aliases, deprecation and migration/sunset policy |

**Default scope rule:** A Tenant assignment is not automatically Tenant-wide in effect. Every key must declare whether the resource is Tenant-wide, Branch-attributed, location-bound, assigned-resource-bound, person-bound, dataset-bound or platform-only. If the exact row/field/resource scope cannot be resolved, deny access.

**Default risk and approval rules:**
- Ordinary non-sensitive view/list/discover is standard risk; sensitive-field view, bulk view and export are elevated/high risk depending on data class and volume.
- Draft creation/update and operational evidence capture are normally standard/elevated; they still require resource scope and valid lifecycle state.
- Approval, financial/stock posting, reversal, refunds, write-offs, stock adjustment, payroll finalization, period close/reopen, identity merge, access administration and compliance attestation are high risk or critical according to the domain.
- Permission/access administration, platform support data access, secret/credential operations, break-glass and legal-hold release are critical and require dedicated authority.
- High-risk permissions are non-delegable by default. A domain may mark an exact action delegable only with a documented rationale, scope/limit and safeguards. Delegated approval cannot bypass maker-checker, non-delegable actions or required independent approval.
- A customer-configurable approval policy may add stricter controls and risk thresholds. It cannot remove a fixed approval, authorization, audit, safety, stock ownership, segregation-of-duties or immutable-history invariant.

### 15.4 Tenant configurability and customization boundary

A Tenant may:
- Create and version permission bundles with its own names, labels, descriptions and translations.
- Assign eligible atomic permissions directly to named principals or governed groups.
- Grant or explicitly deny an eligible key within a permitted Tenant/Branch/location/resource/data scope, effective period, purpose and limits.
- Remove one permission without removing unrelated permissions.
- Configure starter bundles for owner-operated, retail-only, collection, delivery, processing, finance and multi-Branch operating profiles; starter bundles are editable examples and never fixed privilege classes.
- Set stricter approval thresholds, maker-checker requirements, field masks, report scopes, task limits, evidence requirements and review frequencies where the key declares those settings configurable.

A Tenant cannot:
- Change the semantic meaning of a platform permission key or create an unregistered core key.
- Grant an unknown, deprecated, platform-only or extension permission without its registration/eligibility checks.
- Use a wildcard, `*.admin`, `all_permissions` or job-title assignment to bypass atomic permission review.
- Give a principal broader scope, limits or delegation authority than the assigning actor is authorized to grant.
- Convert view into mutation, capture into approval, approval into posting, list into export or a feature entitlement into user access.
- Disable Tenant isolation, Branch ownership, audit, immutable posting history, required approval, segregation-of-duties, zero-grace grant revocation, safety or concurrency controls.

Customer-specific wording, menus, dashboard layouts, forms, permitted workflow sequences and report presentations are customizable only on supported upgrade-safe surfaces. They do not redefine keys or authorization semantics.

### 15.5 Extension permission registration

An extension that needs permissions must register namespaced keys such as `extension.<publisher>.<extension>.<resource>.<action>` under a versioned manifest declaring:
- Publisher/extension identity and version.
- Resource ownership and whether the resource is extension-owned or a core resource.
- Exact action semantics and state transitions.
- Tenant binding, scope types, limits and data sensitivity.
- Risk class, approval/delegation eligibility and mandatory audit events.
- API/event/job/client surfaces and compatibility/deprecation behavior.

A registered extension key is assigned explicitly by a Tenant and remains separate from core keys. Extension installation, activation, API credential creation or device pairing does not grant the extension read/write access. Access to core resources requires a separate explicit host-granted capability and least-privilege contract. Revocation disables subsequent extension access; historical extension actions remain auditable.

### 15.6 ODR-005 completion and implementation gates

The product-wide v1 inventory in section 15.2 covers the current documented capability map: SaaS/tenant administration; organizational structure; identity/access; parties/customers/suppliers/farmers; subscriptions/orders/sales/POS; delivery; procurement and collection; quality/food safety; products/pricing; inventory/traceability; processing; billing/payments/cash/bank; accounting/Tally; expenses; workforce/payroll; assets/maintenance; compliance; analytics/budgeting; workflow/notifications; files/import/export; integrations/extensions; audit/security/privacy.

Before any module is implemented:
1. Its domain owner maps every protected command, state transition, field/data category, report, export, job, device and integration operation to one or more keys from this catalogue.
2. Any missing key is proposed through the controlled catalogue-change process in section 14, not invented locally.
3. The domain documents exact scope, limits, approval/segregation, delegability, sensitive-field and audit metadata for each key, following ARC-IAM-CTRL-001 for policy/approval boundaries.
4. The FRS, ERS, state/exception matrices, Business Rules Catalogue and RTM reference the same permission keys and concepts.
5. Automated authorization tests prove allow, deny, scope mismatch, explicit deny, expiry/revocation, cross-channel equivalence and title/account-label neutrality.
6. The Quality Strategy records the module-specific test identifiers; no module passes readiness with unmapped privileged operations.

**Decision boundary:** The canonical key inventory is now selected as the recommended v1 baseline. Exact numeric thresholds, which actions require independent approval in each Tenant's operating profile, statutory document access, specific data retention and the final API-to-key map remain governed by the relevant domain ODR and pre-implementation acceptance gate. This avoids falsely declaring every business policy or legal applicability question resolved.
