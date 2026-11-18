# Domain Permission and Approval Control Matrix

**Document ID:** ARC-IAM-CTRL-001  
**Status:** Recommended v1 governance baseline selected under the product owner's delegated decision mandate — 2026-11-19  
**Authority:** ODR-002 sections 16.3–16.9; ODR-005 sections 16.39 and 16.41; ARC-IAM-001; ARC-IAM-CAT-001; ARC-AUTH-MATRIX-001; ARC-PV-001; FRS; ERS; Business Rules Catalogue.  
**Purpose:** Translate the canonical permission vocabulary into domain-level authorization and approval rules that work consistently for a one-person dairy, a growing multi-Branch business and an enterprise Tenant without using job titles, fixed account types or mandatory role hierarchies.

## 1. Governing decision

The canonical permission key inventory in [Permission Catalogue and Assignment Governance](PERMISSION_CATALOGUE_AND_ASSIGNMENT_GOVERNANCE.md) section 15 is the single source of truth for permission identifiers and their invariant semantics. This document governs how domain workflows consume those keys and how additional approval controls are layered over permissions.

**Permission and approval are separate controls.**
- A permission authorizes one named action on defined resources/data within its effective scope, limits and time window.
- A workflow approval is an additional decision required only when a fixed invariant or the effective Tenant approval policy requires it.
- An approval decision does not itself create general permissions, expand data visibility or bypass the action permission needed to commit the resulting operation.
- An assigned permission does not automatically imply an approval requirement, a second approver, an approval limit, or an ability to post/reverse.
- A permission bundle or optional Role is only a convenient editable package of atomic permissions. The same effective permissions and scope must produce the same authorization outcome regardless of bundle name, job title, user-account category or client.

The Tenant may grant, restrict, change, expire and revoke eligible permissions for individual principals or governed groups, directly or through editable bundles. A Tenant may configure approval policies, thresholds, evidence requirements and reviewer counts within the boundaries below. A Tenant cannot redefine a canonical key or weaken fixed platform, ownership, safety, audit, concurrency or segregation-of-duties invariants.

## 2. Mandatory authorization evaluation order

Every read, data disclosure, command, event handler, report, export, background operation, device operation and integration action must resolve the following checks. A failure at any step denies the protected operation; do not continue with a partial or permissive evaluation.

1. **Authenticate actor or service identity.** Resolve the actual user/service principal, session/device context and Tenant/security boundary.
2. **Validate Tenant membership or platform authority.** Tenant membership must be active. Platform-only actions require platform authority. A platform identity is not automatically a Tenant business actor.
3. **Validate capability availability separately.** Feature entitlement and Tenant capability activation decide whether the module is available; neither grants a user permission.
4. **Resolve a registered canonical permission key.** The exact action must map to a current key in ARC-IAM-CAT-001. Unknown, malformed, retired, incompatible or unmapped operations fail closed.
5. **Evaluate effective assignment.** Consider valid direct-principal, governed-group and optional permission-bundle assignments with explicit allow/deny, effective start/end, reason and version. Explicit deny wins over allow; missing or ambiguous assignment denies.
6. **Evaluate resource and data scope.** Resolve Tenant, Branch, OperatingLocation, StockLocation, route/run/task, person/party, financial book/period, dataset, field/data category or service-identity scope as declared by the key. A broad permission at one scope does not create a broader resource scope.
7. **Evaluate operational assignment and special grants.** Where required, confirm workforce assignment, delivery-run assignment, stock-task grant, approval delegation, support grant, device/service grant or custody responsibility. These are independent constraints; none creates a missing underlying action permission.
8. **Evaluate limits and contextual policy.** Enforce amount, quantity, UOM, count/volume, purpose, time, risk, status, product/quality, resource availability, approval and segregation-of-duties constraints.
9. **Validate lifecycle state and domain invariants.** A permission cannot force an invalid state transition, ownership change, stock movement, financial post, custody acceptance or legal status.
10. **Revalidate at the authoritative commit boundary.** For a state-changing action, recheck current membership, assignment, explicit deny, scope, grant, limits, approval and resource/state versions atomically or through an equivalent concurrency-safe mechanism immediately before commit. A stale screen, session, cache, offline queue or earlier pre-check cannot authorize a later write.

For reads and data disclosure, the same principle applies at the point of retrieval and again when generating or downloading a stored report/export. A previously authorized export job does not create a permanent download entitlement.

## 3. Read, list, search, sensitive data and export

The permission metadata must specify whether a key permits detail view, collection/list, search/discovery, aggregation, drill-down or a named dataset. A `.view` key grants only the query surfaces explicitly declared in that key's metadata. If list/search/discovery is not expressly included, the caller needs an explicit corresponding key or the query is denied.

Additional fixed rules:
- A row/record permission does not imply every field is visible. Sensitive fields (identity documents, bank/payee data, payroll, compensation, tax identifiers, personal information, security evidence and sensitive audit investigations) require their declared sensitive-data permissions.
- List/search returns only authorized rows and fields. Search indexes, autocomplete, counts, facets, suggestions and global lookup must not leak the existence or attributes of unauthorized records.
- A report permission does not imply dataset access, drill-down, export or download.
- Export requires a dedicated export permission plus all row/field permissions for the data requested. A single export key cannot bypass sensitive-field permission.
- Bulk size, frequency, purpose, destination, masking, retention and approval may be constrained by Tenant policy. High-sensitivity or high-volume export can require approval under the effective policy.
- Attachments, signed URLs, cached files, mobile downloads and generated report artefacts must revalidate the parent-resource and sensitive-data scope at access/download time.
- A user may have read-only permission without mutation. A user may have a mutation permission without approval, posting, reversal or export authority unless those exact capabilities are separately assigned.

## 4. Risk classes and approval defaults

Risk is a property of the action/resource/data and context, not of the actor's title or account category. Each canonical permission entry must declare its risk class and whether independent approval is fixed, policy-configurable or not applicable.

| Class | Description | Default control |
|---|---|---|
| R0 — Read/basic | Non-sensitive view, ordinary list/detail queries, draft creation, low-impact capture and non-posting observations | Exact permission and scope; no separate approval by default. Sensitive fields and bulk queries are classified separately. |
| R1 — Operational effect | Routine receipt, pick/issue, delivery dispatch, schedule execution, approved receipt capture, normal operational status changes | Exact permission, resource/task assignment, validation and audit. Separate approval only where a fixed domain rule or Tenant policy/limit triggers it. |
| R2 — High impact | Stock adjustment/variance, write-off, refunds/credits, pricing/rate overrides, supplier/farmer settlement, material payment execution, payroll finalization, financial posting/reversal, identity merge, high-impact configuration or sensitive bulk export | A separate approval is the recommended default for high-impact cases. Tenant policy may set thresholds and specify when a single authorized actor may execute without another approval, but cannot bypass a fixed domain rule or required independent review. |
| R3 — Critical privilege/safety | Permission/access administration that expands privilege, platform support access to Tenant data, credential/secret operations, emergency override, legal-hold release, highly sensitive data deletion/export, or security control changes | Dedicated critical permission, narrow scope, explicit purpose, independent approval/review where the governing policy requires it, short validity where applicable, and durable audit. Protective revocation/deny actions may be immediate and must not be delayed by approval. |

**Default approval semantics**
- For R0, no separate approval is required unless a specific policy says otherwise.
- For R1, operation-specific permission and validation are normally sufficient. A Tenant may add review/approval by risk, value, quantity, location or customer commitment.
- For R2, require a distinct approval step by default for actions that materially affect stock balances, financial balances, commercial terms, compensation or verified identity. Tenant policy can configure thresholds and proportionate controls, but a workflow marked as requiring independent approval cannot be self-approved or bypassed.
- For R3, require dedicated authority and control evidence. Where a fixed policy requires independent approval or retrospective review, Tenant configuration cannot remove it.
- An approval permission is not a substitute for the domain action permission. An actor approving a stock adjustment must hold the relevant `inventory.adjustment.approve` and the required workflow approval capability/scope; the generic `approval.action.approve` key alone is insufficient.
- Rejection, deny, quarantine/hold, session revocation and protective restriction actions may be permitted immediately to contain risk. They do not imply release, acceptance, reversal, write-off or other financial/stock effects.
- An initiator cannot approve their own action where an effective maker-checker or independent-approval policy applies.
- Delegated Approval is available only for actions explicitly marked delegable by their owning domain. It does not bypass independent approval, non-delegable actions, emergency override restrictions or multi-stage/quorum policy.

## 5. Tenant-configurable approval policy model

The Tenant may define multiple versioned ApprovalPolicyProfiles and apply them by domain, Branch, workflow, action, risk class, resource, amount/quantity, data sensitivity or period. Starter profiles may be offered for convenience:

| Starter profile | Intended operating scale | Recommended starting posture |
|---|---|---|
| Single-operator | Owner-operated or very small business | One authorized user may perform routine and configured low/medium-risk workflows without artificial second-party setup. Fixed stock-owning-Branch grant approval, Emergency Override, non-delegable restrictions and audit remain unchanged. |
| Small-team | Small dairy with a few users | Enable independent review on high-risk adjustments/refunds/settlements and access expansion; use practical amount/quantity thresholds. |
| Multi-Branch | Several Branches, shared sites and distributed operations | Default to maker-checker for high-risk financial/stock actions, Branch-sensitive changes, access expansion and material inter-Branch operations; require Branch/resource scope and accountable approvers. |
| Enterprise | High volume, many users/locations and formal controls | Support multi-stage approvals, quorum, risk/amount/percentage thresholds, separation-of-duties, delegated approval for eligible actions, scheduled access reviews and exception escalation. |

These are editable policy templates, not fixed privilege classes, employee types or role assignments. A Tenant may use a different configuration. The active profile and each effective policy version must be snapshotted with the affected business decision.

**Configurable policy parameters**
- Approval required: never, on configured threshold/condition, always, or according to a fixed domain invariant.
- Threshold type/value: absolute INR value; percentage deviation; quantity with UOM; stock variance; discount; rate change; credit exposure; payroll amount; export row/size/data sensitivity; time or frequency limit.
- Reviewer count/quorum and whether reviewers must be distinct from the initiator.
- Required approver permission key and permitted Tenant/Branch/resource scope.
- Maker-checker, multi-stage order, escalation, timeout and expiry behavior.
- Evidence and reason requirements; required attachment, measurement, source record or incident reference.
- Validity period for the approval and whether a material change invalidates/restarts it.
- Whether a domain action may use delegated approval; default is non-delegable unless explicitly enabled in the domain catalogue.
- Notification routing and escalation preferences under ODR-021.

Thresholds use canonical currency, UOM, precision and rounding rules. No policy may compare incompatible units, apply floating-point money calculations, or let a Tenant change fixed permission meanings.

**Fixed versus configurable**
- Tenant configuration may make controls stricter, add reviewers, lower thresholds, narrow scopes, require more evidence or shorten validity.
- Tenant configuration cannot remove mandatory ownership checks, required stock-owning-Branch grant approval, required independent approval, non-delegable safeguards, audit, immutable history, zero-grace expiry/revocation, server-side authorization, concurrency controls, or verified mandatory product-safety/legal requirements.
- If a fixed rule and a Tenant policy conflict, the stricter fixed rule prevails.
- If two Tenant policies conflict or are ambiguous, apply the more restrictive valid rule or deny and require policy correction; do not select the permissive interpretation.

### 5.1 Approval policy version lifecycle, weakening controls and in-flight requests

An ApprovalPolicyProfile is an editable Tenant configuration container. Each published ApprovalPolicyVersion is immutable, effective-dated and bound to one or more explicit domain/action/resource/risk scopes. A starter profile is copied into Tenant-owned configuration before it can govern Tenant transactions; its name never assigns permissions or creates a user category.

**Version lifecycle**
- Draft policy edits use `approval.policy.configure` and authorize no business approval decision.
- Submission freezes the candidate policy payload/version. Where the effective security policy requires review, use `approval.policy.submit` and route it to an independent reviewer.
- A version that weakens controls MUST receive a separate approval using `approval.policy.approve` by an authorized principal other than the policy change initiator. It becomes effective only through `approval.policy.publish` after all required approvals have committed.
- A version that only tightens controls may be published by a principal holding the exact scoped configure and publish permissions where no other fixed/Tenant rule requires independent approval. The change and impact preview remain auditable.
- Future-effective versions are Scheduled and authorize no decision until their effective start. Replaced versions become Superseded; retired versions cannot be newly matched. Historical versions and decisions remain reproducible.
- Material policy changes create a new immutable version. Editing a published policy in place, changing a policy payload after approval or reusing an approval for a different payload is prohibited.

**Policy weakening classification**
Changes that can weaken a control include raising an approval threshold, reducing reviewer count/quorum, removing a required stage or evidence item, broadening scope, shortening approval validity, making an action delegable, relaxing maker-checker/independence, increasing export/data limits, or changing a policy so fewer actions require approval. They require independent approval and cannot be self-approved. A change cannot weaken a fixed platform/domain safeguard even with independent approval.

Changes that only strengthen controls include lowering thresholds, adding reviewers/stages/evidence, narrowing scope, shortening validity or reducing permitted volume. They may take effect under the explicit policy-publish permission, without requiring a second person unless the active Tenant policy requires it.

**Policy selection and conflict handling**
- Match active policies using domain action, risk class, resource and declared Tenant/Branch/location scope, amount/quantity/UOM, data sensitivity and effective time.
- Fixed platform/domain safeguards are always applied. For overlapping Tenant rules, combine compatible requirements; where one rule is clearly stricter, apply the stricter rule.
- Where two applicable rules conflict, scope precedence is ambiguous or the system cannot compare thresholds/units safely, deny/hold the protected decision and require policy correction. Never choose the permissive interpretation.
- Tenant configuration cannot redefine permission-key semantics, create a permission, weaken required approval for a stock-task grant, bypass non-delegable actions, or relax immutable history, audit, custody, safety, ownership or commit-time authorization controls.

**In-flight ApprovalRequest policy binding — selected safe default**
- When an ApprovalRequest is created/submitted, snapshot its exact source payload/version, the matched ApprovalPolicyVersion(s), rule(s), threshold calculations, approval stages and evidence requirements. The request never silently changes its original snapshot.
- At each approval action and again at the protected commit boundary, evaluate the current effective policy for that same action/resource/scope in addition to the original request snapshot.
- If a newer effective policy is more restrictive, the stricter combined requirements apply. Any previously completed approval stage invalidated by the new requirement must be repeated; new reviewers/evidence are added and the change is audited. The protected action remains blocked until satisfied.
- If a newer policy is less restrictive, it MUST NOT reduce or remove an approval requirement already attached to a pending request. The pending request continues under the stricter original snapshot until completed, rejected or withdrawn. A new request created after the less restrictive policy becomes effective may use it, subject to all fixed safeguards.
- If the matched policy version is missing, revoked, incompatible, internally contradictory or cannot be safely compared, hold the request and deny commit until an authorized policy correction/review resolves it.
- A completed decision committed while its applicable policy and permissions were valid remains historically immutable. A later policy change is prospective and does not retroactively erase or invalidate completed transactions.

This control applies to financial, stock, workforce, pricing, procurement, identity, export, compliance and platform workflows. It is not limited to a particular module or size of Tenant.

## 6. Domain-level permission and approval matrix

The table below defines the logical action and approval boundary for the product's documented domains. Exact keys are in ARC-IAM-CAT-001 section 15.2. Each permission is independent; the table does not imply that one permission grants another.

| Domain | Action families / examples | Required permission boundary | Approval and separation baseline |
|---|---|---|---|
| Platform and SaaS administration | Tenant provision/suspend/close; plan and entitlement changes; platform support access | Platform-only keys; support grant keys; no Tenant business-data permission is implied | Privileged activation, support access and entitlement changes require purpose/scope/audit and configured independent control. Protective suspension/revocation may be immediate. |
| Tenant profile and business setup | Tenant business profile, capability activation, Branch/site setup, business calendar | Tenant/Branch/OperatingLocation scope; configuration permission is separate from access administration | Ordinary metadata setup may be direct. Changes affecting legal identity, active stock ownership, Branch mapping or downstream workflows require review under the owning domain policy and effective dating. |
| Membership and access administration | Invite/activate/suspend/revoke members; create bundles/groups; assign, restrict, expire or revoke permissions | Dedicated membership/access keys; assigner cannot exceed own delegation ceiling; platform-only keys cannot be Tenant-assigned | Privilege-expanding access assignment is R3 and should require independent review under the Tenant's security policy. Permission revocation/deny and session revocation may be immediate. No self-escalation or circular delegation. |
| Party, customer, supplier and farmer identity | Create/update party, contacts and addresses; verify identity/bank data; resolve/merge duplicate identity | Party/customer/supplier/farmer keys and sensitive-field keys with relationship scope | Normal draft changes/capture may be direct. Identity merge/unmerge involving verified identifiers, balances, posted transactions or bank/payee details requires impact review and independent approval. |
| Customer subscriptions, schedules and orders | Create/change subscription; pause/resume/skip/substitute; approve exceptional order | Subscription agreement, schedule, schedule_exception, occurrence and order keys are separate | Routine changes follow cut-off/effective-date policy. Retroactive, high-value, out-of-policy, credit-impacting or history-affecting exceptions may require approval; generated occurrences remain idempotent and versioned. |
| Sales and POS | Capture/post sale; apply discount; void/return; refund; reconcile POS session | Sale capture/post, discount apply/approve, return/refund and POS session keys are separate | Routine sale capture is R1. Discount overrides, voids after close, refunds, credit adjustments and cash shortages use amount/period/variance thresholds; maker-checker applies where policy triggers it. |
| Delivery and fulfilment | Plan/allocate/assign/dispatch/execute run; capture stop outcome/proof; hold/resume/close; assign transport resource | Route, plan, run, stop, participant, transport resource, proof and exception keys are distinct. Duties and vehicle assignment confer no permission | Routine dispatch may be R1 after readiness validation. Out-of-policy quantity/destination change, cash variance, product-safety incident or close-with-exceptions requires its specific exception/review permission and any configured approval. |
| Procurement and supplier invoices | Create/approve order; receive/accept/reject goods; return goods; verify/post supplier invoice | Request, order, receipt, return and supplier-invoice permissions are independent | Purchase approval thresholds may depend on INR value, supplier terms, rate variance and budget. Physical receipt/quality acceptance cannot be implied by purchase approval or supplier invoice approval. |
| Milk collection and measurement | Capture collection and measurement; verify device readings; sample/test/retest; accept/reject | Session, entry, measurement, sample and test permissions are separate; device/service identity is scoped | Routine capture is R1. Quality rejection, out-of-range or disputed measurements use quality-review rules; no settlement or payment is implied by raw measurement capture. |
| Quality, hygiene and food safety | Publish profile; capture/verify results; apply/release hold; resolve nonconformance; initiate/approve recall | Quality, food-safety, sanitation, corrective-action and recall keys are separate | Protective hold/quarantine may be immediate when permitted. Release, recall closure and final disposition require the exact permission and any fixed or configured independent review; mandatory controls cannot be disabled. |
| Farmer rates, advances and settlement | Configure/publish rate chart; calculate/review/approve/post settlement; advance/deduction | Rate chart, settlement, advance and deduction keys are independent | Rate changes and settlement/posting follow effective dates and configured financial thresholds. Deductions cannot be silently inferred from quality/custody allegations; separate evidence and approval are required. |
| Product, UOM, pricing and commercial terms | Create/update product; change UOM/conversion; configure price list; discount or price override | Product master, unit conversion, price list, tax profile and override keys are separate | Draft catalogue changes may be direct. Published UOM/product changes must not rewrite historical transactions. Price/rate/discount overrides use configured amount/percentage thresholds and required approvals. |
| Inventory, lots and stock control | Receive/put-away/pick/issue/transfer; reserve/release; count; approve variance; adjust/write-off; quarantine/release; value/export | Each stock movement, reservation, count, variance, adjustment, write-off, quarantine, valuation and export key is independent and scoped to stock-owning Branch/StockLocation/item/lot/task | Count capture never approves variance. Adjustment/write-off and valuation changes are R2; separate submit/approve/post controls and configurable thresholds apply. Quarantine is protective; release is separate. Inter-Branch ownership transfer requires its own workflow and cannot be done under a stock-task grant. |
| Cross-Branch stock-task grants and Emergency Override | Request/submit/approve/reject/withdraw/revoke/reconcile a grant; activate/extend/revoke/review emergency override | Stock-task grant keys are separate from inventory action keys; the exact `inventory.emergency_override.activate`, `inventory.emergency_override.extend`, `inventory.emergency_override.revoke` and `inventory.emergency_override.review` permissions are distinct from `security.break_glass.activate` and `security.break_glass.review` | Stock-owning-Branch approval is mandatory before grant activation. Emergency Override is the narrowly scoped ODR-002 break-glass path: 60-minute initial period, one independently authorized extension up to 120 minutes total, immutable history, immediate expiry and retrospective review of self-activation. |
| Traceability and recall | Trace lot/derived lots; apply/release hold; initiate/approve/execute/close recall | Lot view/trace/export, hold and recall keys are separate from stock disposition and customer data | Protective trace/hold is R1. Recall approval/closure and disposition require exact scoped review and configured/fixed independent controls; trace permission does not grant stock write-off. |
| Processing and packaging | Author recipe; approve/publish; execute batch; consume inputs; capture/verify output; approve yield variance/quality release | Recipe, order, batch, input/output, yield, variance, quality release and packaging keys are distinct | Routine batch execution requires readiness and stock availability. Recipe publication, material yield variance, quality release and adjustments follow configured independent review; posting remains a separate stock/financial effect. |
| Billing and receivables | Preview/create/issue/post document; issue credit/debit note; allocate/reconcile receivable | Billing document, note, statement, receivable allocation and reconciliation keys are separate | Routine invoice issue follows validated billable facts. Credit/debit notes, write-offs, manual adjustments and high-value credits use thresholds and independent approval where policy triggers. A statement does not create a new charge. |
| Payments, cash and bank | Record/capture/verify/allocate payment; refund/reverse; cash float count/reconcile/close; handover; bank import/match/reconcile | Payment record, external verification, allocation, refund/reversal, cash custody and bank reconciliation are distinct | Manual entry is not verification. Refund/reversal and cash shortages use amount/variance thresholds. Cash handover needs actual acknowledgement; bank reconciliation cannot be marked complete from unverified references. |
| Accounting and Tally | Configure CoA/posting rule; create/approve/post/reverse journal; close/reopen period; map/sync Tally | Accounting book, CoA, journal, posting rule, period, reconciliation, Tally mapping and sync keys are distinct | Routine source posting follows approved posting rules. Journal reversals, manual journals, closed-period reopen, mapping changes and bulk Tally synchronization use independent approvals/thresholds. Posted journal history is immutable. |
| Expenses and petty cash | Submit/approve/post expense; pay claim; manage/count/reconcile petty cash | Expense claim, expense payment and petty-cash keys are separate | Approval limits may depend on amount, category, cost centre and supporting evidence. Payment execution does not follow automatically from claim approval unless the configured payment workflow completes. |
| Workforce and HR | Maintain person/engagement; configure workforce/shift assignments; attendance capture/correction; leave decisions | Person, engagement, workforce assignment, shift, attendance, leave and sensitive document keys are separate | Routine attendance capture may be direct. Attendance correction, workforce allocation changes, sensitive documents and leave decisions require exact permission and configured review; stock grants cannot modify workforce terms. |
| Payroll | Prepare/review/approve/post/reverse payroll; view salary/bank details; publish payslip/export | Payroll action keys and sensitive salary/bank/payslip/export keys are separate | Payroll finalization should use independent review/approval under the effective Tenant policy; sensitive salary/bank access is separate and audited. Statutory treatment remains subject to external legal review. |
| Assets and maintenance | Register/assign/decommission asset; work order; cleaning/calibration/inspection; shared resource booking; disposal | Asset, maintenance, calibration, cleaning, inspection, shared-resource and disposal keys are independent | Routine use/capture may be direct. Disposal, calibration verification, maintenance closure and shared-cost allocation follow configured review and separate accounting posting where applicable. |
| Tax and compliance | Configure tax profile/classification; verify compliance evidence; renew/archive; export registers | Tax profile/classification/register and compliance record/document keys are separate | Configuration and upload do not establish legal applicability. Verification/attestation and statutory export require explicit scope, current evidence and qualified domain controls. |
| Reports, analytics, budgets and scenarios | View dashboard/report/dataset; configure KPI; run forecast; create/approve budget; export dataset | Report definition, report/dataset view, export, KPI, forecast, budget and scenario keys are separate | Ordinary dashboard use follows row/field scopes. Sensitive/bulk exports may require approval by data class, row volume or purpose. Budget approval is separate from scenario execution and ledger posting. |
| Workflow, notifications, files and integrations | Configure workflow/template; approval request/action; retry notification; file access; import/export; manage integration and credentials | Each workflow, notification, file, import/export, integration, API credential and extension key remains independent | Provider/credential changes, external data access, large imports/exports and extension core-data grants use least privilege, audit, limits and independent approval where required. No external callback or retry bypasses current permission checks. |
| Audit, security and privacy | View/export audit; revoke session; report/review security incident; security break-glass; process data-subject request; apply/release legal hold | Audit, security, privacy and legal-hold keys are separate and sensitive | Protective incident reporting/session revocation may be immediate. Sensitive investigation, legal-hold release, privileged support and bulk audit export require critical scoped authority and independent control where prescribed. |

## 7. Threshold catalogue and canonical measurement

Thresholds are Tenant-configurable only where the owning domain marks them configurable. The following threshold types are supported by design:

- Absolute financial amount in INR, with precision/rounding from the approved money policy.
- Percentage or absolute deviation from an effective supplier rate, price, budget, prior approved value or contractual limit.
- Quantity with its exact UOM/dimension, SKU/product/lot and stock-owning Branch/StockLocation context.
- Stock-count variance and adjustment/write-off volume, by item/lot/period.
- Discount, refund, credit/debit note, customer credit exposure and payment/refund amount.
- Procurement order, supplier invoice, farmer settlement and expense amount.
- Cash float variance, cash handover shortage and reconciliation difference.
- Payroll variance and salary/compensation change, subject to legal and privacy controls.
- Dataset sensitivity, record count, export size, time frequency and destination/purpose.
- Frequency, cumulative amount/quantity per period, transaction count and time-window limits.

All thresholds must declare scope, currency/UOM, comparison operator, effective dates, rounding, aggregation window, policy version, override permissions, approver requirements and historical snapshot behavior. No universal hard-coded INR amount, milk density conversion or quantity tolerance is selected by this document.

## 8. Fixed safeguards and explicit non-delegable actions

The following boundaries are fixed unless the relevant ODR explicitly records a stricter requirement:
- No title, account category, membership, Home/Primary Branch, feature entitlement, client type, device or physical location grants permission.
- No wildcard/all-permissions grant is created by default. Assignment authority cannot exceed the assigner's own delegation ceiling.
- Cross-Branch stock-task grants require approval by the stock-owning Branch's appropriately scoped authorized user or a currently valid permitted delegate. Grant and independent inventory permission remain separate.
- Emergency Override uses only the ODR-002-scoped inventory emergency-override permissions and validity/review controls. Security break-glass keys do not authorize stock operations.
- Delegated approval cannot be used for access/permission administration, Emergency Override, stock adjustment/write-off, financial-period close/reopen, sensitive data deletion or other actions marked non-delegable by their owning domain. It cannot approve the delegate's own initiated action or bypass maker-checker/quorum.
- Stock ownership, financial ownership, physical custody, stock ledger, actual handler, workforce assignment and permission remain distinct.
- Posted stock/financial facts and approved snapshots are immutable; correction uses linked events/reversals.
- Deny, expiry, revocation, grant hold and protective safety actions take effect immediately according to their fixed rules. In-flight writes cannot use stale authority.
- Required food-safety, hygiene, quality and verified statutory controls cannot be weakened by Tenant settings or extensions.
- A notification, failed provider, offline device or pending approval cannot prolong or create authority.

## 9. Approval workflow integrity

Every approval decision must reference:
- The exact source resource and immutable payload/version/hash being decided.
- The actual initiator, requested action, Tenant/Branch/resource scope and effective permission evaluation.
- The approval policy/version, threshold calculation, reviewer/quorum requirements and maker-checker result.
- The actual approver/delegate, effective approval permission and any delegation identifier/version.
- Decision, reason, timestamp, evidence, expiry and downstream authorized transition.
- Any change after approval. A material change invalidates/restarts approval; an approval cannot be reused for a different payload.

A workflow engine may route tasks, notify reviewers and preserve evidence. It cannot create permission or perform a domain mutation solely because a workflow task is assigned. Approval and business commit must be concurrency-safe and idempotent. Revocation/expiry racing with an uncommitted action must prevent a later unauthorized commit; a decision already committed while valid remains historically immutable.

## 10. Configuration governance, extensibility and scale

- Policy values are versioned, effective-dated configuration with stable keys, types, allowed values/ranges, units, scope, inheritance, permission, validation, impact preview, audit and rollback semantics under ARC-PV-001.
- Only explicitly overrideable keys may be narrowed or specialized at Branch/site/process scope. Tenant configuration cannot rewrite platform invariants or historical snapshots.
- Domain extensions may add new approval stages, evidence types or threshold calculators only through versioned contracts and registered permissions. Extensions cannot modify canonical permission semantics or write directly around the owning domain workflow.
- Approval routing, aggregate/large-volume review and notification fan-out should use bounded asynchronous processing; final authorization, threshold validation and stock/financial commit remain synchronous and authoritative where required.
- Small and Enterprise profiles use the same resource ownership, permission evaluation, state transitions and audit semantics. They differ in enabled capabilities, policy configuration, optional integrations, workflow complexity, thresholds and capacity—not in security semantics or code forks.

## 11. Acceptance and verification baseline

Use stable test identifiers AT-CTRL-001..022:

- **AT-CTRL-001:** An authenticated principal with no explicit permission cannot view or mutate a protected Tenant/Branch resource.
- **AT-CTRL-002:** Identical effective permission/scope/context yields identical outcomes regardless of job title, role/bundle label, account category or client.
- **AT-CTRL-003:** Direct, bundle and governed-group assignments resolve to canonical atomic keys; revoke/restrict/expire changes take effect at the effective time.
- **AT-CTRL-004:** Explicit deny prevails over applicable allow; ambiguous/incompatible policy fails closed.
- **AT-CTRL-005:** Tenant membership, feature entitlement, capability activation, permission and special task grant are independently evaluated.
- **AT-CTRL-006:** A list/search/detail/aggregate/drill-down endpoint is denied unless its permission metadata declares that query surface and row/field scope.
- **AT-CTRL-007:** Sensitive fields, attachment reads, report exports and downloads require their independent permissions; export cannot bypass field masking.
- **AT-CTRL-008:** A protected command lacking a canonical permission mapping is denied across every channel.
- **AT-CTRL-009:** An operation with a known key but wrong Tenant/Branch/location/task/resource scope is denied.
- **AT-CTRL-010:** A revoked permission racing with a protected write results in either a commit while valid or denial; no later unauthorized write commits.
- **AT-CTRL-011:** A routine R0/R1 operation can be completed by one authorized user where no fixed or configured approval is required, without artificial employee/approver setup.
- **AT-CTRL-012:** R2 actions trigger the configured amount/quantity/data-class approval policy; required independent approval cannot be self-approved or skipped.
- **AT-CTRL-013:** R3 privilege expansion, support access, credential and legal-hold operations enforce dedicated critical authority and configured independent control.
- **AT-CTRL-014:** Lowering a threshold or adding an approval stage takes effect by policy version; raising a threshold cannot weaken fixed safeguards.
- **AT-CTRL-015:** A material payload change after approval invalidates/restarts approval and cannot reuse a previous decision.
- **AT-CTRL-016:** Generic `approval.action.approve` alone cannot approve or post a domain transaction without the owning domain permission.
- **AT-CTRL-017:** Cross-Branch StockTaskAccessGrant approval and execution require the independent permissions and prerequisites in ODR-002; the grant cannot broaden data/action access.
- **AT-CTRL-018:** Stock emergency override permissions do not grant security/platform break-glass access, and security break-glass permissions do not authorize inventory emergency stock actions.
- **AT-CTRL-019:** Delegated approval is limited to permitted action/scope/limits and cannot bypass non-delegable, maker-checker or own-action restrictions.
- **AT-CTRL-020:** A protective deny/revoke/quarantine action can take effect immediately where authorized and does not accidentally perform a release, reversal, write-off or financial post.
- **AT-CTRL-021:** A small, multi-Branch and enterprise Tenant can operate using the same permission semantics with different versioned approval-policy configurations and no role-title privilege.
- **AT-CTRL-022:** Historical transactions and approval decisions can be reproduced using the policy, permission, configuration and payload versions effective at the time.

## 12. Decision status and dependencies

**Selected baseline:** The logical domain-level permission and approval-control matrix is selected as the recommended v1 baseline under the product owner's delegated decision mandate.

**Remaining implementation-readiness work:** Each module must map all actual commands, API endpoints, events, field disclosures, reports, jobs, devices and integration operations to the exact keys in ARC-IAM-CAT-001 section 15.2; define its final configurable numeric threshold values; classify any additional sensitive fields; and implement automated coverage. These details depend on the finalized domain/API workflow and Tenant configuration. They are not permission-key naming decisions and do not authorize implementation before the project's documentation readiness gates are met.

**External/legal dependencies:** Statutory requirements for approval, record access, payroll, tax, food safety, data privacy, retention and regulated transactions must be checked after project documentation completion under the product owner's stated legal-review scope. No compliance claim is made by this matrix.

## 13. Related authoritative documents

- [Open Decisions Register](../01-product/OPEN_DECISIONS_REGISTER.md) — ODR-002 sections 16.3–16.9 and ODR-005 sections 16.39 and 16.41.
- [Permission Catalogue and Assignment Governance](PERMISSION_CATALOGUE_AND_ASSIGNMENT_GOVERNANCE.md) — canonical keys, key metadata and assignment governance.
- [Authentication, Authorization and Access Control Architecture](AUTHENTICATION_AUTHORIZATION_AND_ACCESS_CONTROL_ARCHITECTURE.md).
- [Cross-Branch Permission Matrix](CROSS_BRANCH_PERMISSION_MATRIX.md).
- [Functional Requirements Specification](../03-requirements/FUNCTIONAL_REQUIREMENTS_SPECIFICATION.md).
- [Business Rules Catalogue](../03-requirements/BUSINESS_RULES_CATALOGUE.md).
- [State Transition Matrix](../03-requirements/STATE_TRANSITION_MATRIX.md).
- [Quality Engineering and Acceptance Test Strategy](../07-quality/QUALITY_ENGINEERING_AND_ACCEPTANCE_TEST_STRATEGY.md).
- [Product Configurability, Customization, Extensibility and Scale Policy](PRODUCT_CONFIGURABILITY_CUSTOMIZATION_EXTENSIBILITY_AND_SCALE_POLICY.md).
