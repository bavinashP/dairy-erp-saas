# Party, Customer, Supplier and Farmer Domain Analysis

**Document ID:** DBR-003  
**Status:** Decision-support analysis — proposed conceptual baseline; owner decisions remain open  
**Date:** 2026-11-17  
**Authority reviewed:** ERS-001, FRS-001, Core Domain Entity Catalogue, Canonical Business Glossary, Actor and Access Model, ARC-IAM-001, ARC-TEN-001, procurement and farmer-settlement workflows, subscription/sales/billing workflows, accounting controls, Tally integration specifications, and ODR-004/005/009/010/013/014/017.

## 1. Purpose and boundaries

This document analyses the shared identity and role model for customers, suppliers, farmers/milk producers and other business counterparties. It also records the branch-level visibility rule explicitly supplied by the product owner.

This is a logical domain and security analysis. It does not define SQL tables, DDL, ORM models, migrations, APIs, physical indexes or implementation code. It does not close ODR items or approve legal, tax, accounting, KYC, credit or operating policies.

## 2. Findings from current authoritative documents

1. ERS-001 already proposes a tenant-owned `Party` identity root, `PartyRole` assignments, and role-specific `CustomerAccount`, `SupplierAccount` and optional `FarmerProfile` concepts.
2. ERS-001 states that a supplier and farmer may be the same party and that multiple roles should not force duplicate identity records.
3. FRS-001 requires staff-maintained customer profiles, multiple service addresses and contacts; supplier/farmer records with contacts, terms, payment details and status; and distinct packaged-goods procurement and raw-milk collection workflows.
4. Farmer collection records must retain producer, collection point, date/shift, measured quantity, unit, sample/test evidence and acceptance outcome. Farmer settlement is separate from collection and actual payment.
5. Customer self-service login is outside the current approved scope. A Party or CustomerAccount is not a login identity.
6. The current Actor and Access Model and ARC-IAM-001 require backend authorization by tenant, branch/location, action and resource scope. A role alone is not proof of record visibility.
7. The current specifications do not yet fully define Party kind, household/cooperative representation, branch ownership of party relationships, duplicate resolution, merge governance or cross-branch party visibility.

## 3. Recommended conceptual model (not a final owner decision)

### 3.1 Distinct concepts

| Concept | Responsibility | Boundary |
|---|---|---|
| Party | Tenant-owned counterparty identity root | Shared identity; not a login and not a transaction account |
| PartyKind | Classifies an identity as person, organization or another explicitly supported form | Do not assume every farmer is an individual |
| PartyRole | Effective-dated role such as customer, supplier, farmer/milk producer or vendor | One Party may hold multiple roles |
| CustomerAccount | Customer-specific commercial relationship | Pricing, credit, billing and customer status |
| SupplierAccount | Supplier-specific commercial/procurement relationship | Terms, payable/advance policy and supplier status |
| FarmerProfile | Optional producer-specific extension | Collection identifiers, producer attributes and approved operational data |
| PartyContact | Contact point/person associated with a Party | Multiple contacts and purposes; not automatically a login |
| PartyAddress | Address record with purpose, verification and effective dates | Address is not a branch or operating location |
| CustomerServiceAddress | Link between customer account and service address/servicing branch or area | Service routing and subscription context |
| PartyExternalIdentifier | Typed identifier from an issuing authority or external system | Sensitive; issuer/type/scope and verification must be explicit |
| PartyRelationship | Explicit relationship between parties, e.g. household member, authorized representative, cooperative member | Relationship does not imply ownership, authority or shared balances |
| PartyMergeCase / alias history | Controlled duplicate-resolution and merge evidence | Preserve historical references and reversibility/correction path |

The exact entity names and cardinalities must be reconciled into ERS-001 only after the relevant owner and privacy decisions are resolved.

### 3.2 Party versus Person versus UserAccount versus PersonnelRecord

- **Party** represents a business counterparty in the tenant's operational records.
- **Person** represents a human identity where required by the business.
- **UserAccount** authenticates a system user.
- **PersonnelRecord** represents workforce engagement.
- A person may be a Party, a workforce Person, an authorized contact, or more than one of these, but those relationships must be explicit and must not collapse credentials into business identity.
- A customer, farmer or supplier does not receive login access merely because a Party record exists.

### 3.3 One Party with multiple roles

Recommended baseline: maintain one Party identity root per actual counterparty within a tenant, with independent role assignments and role-specific account/profile data.

Examples:
- A farmer who also purchases feed from the dairy may be both supplier/farmer and customer.
- A local shop may buy packaged dairy products and also supply packaging material.
- A farmer may have a SupplierAccount and FarmerProfile; the farmer role must not create a second unrelated supplier identity.
- A customer with multiple service addresses remains one customer relationship unless commercial/legal policy requires distinct accounts.

Role-specific balances, pricing, credit limits, settlement terms, approvals and lifecycle statuses remain separate. A shared Party identity does not imply netting customer receivables against supplier/farmer payables.

## 4. Party types and real-world scenarios

The model must not assume that all counterparties are individual persons.

| Scenario | Modelling implication |
|---|---|
| Individual farmer delivering milk | Person-kind Party with supplier/farmer roles and FarmerProfile |
| Household/family-managed farm | Model the actual contracting/payee party and explicit family/representative relationships; do not silently combine family members |
| Farmer cooperative or producer organization | Organization-kind Party; individual members and milk suppliers require explicit relationships and settlement rules |
| Proprietorship or shop | Party kind and legal/business identity must follow approved business identity policy; proprietor and trade name must not be conflated without evidence |
| Customer household with several delivery recipients | Customer account plus service addresses and named delivery contacts/recipients; recipient is not automatically account owner |
| Business customer with multiple locations | One or more customer accounts/service addresses as commercial policy requires; each service location is linked to an authorized servicing branch/area |
| Supplier with multiple collection points | Supplier/farmer identity remains distinct from collection point; each collection event records its actual point and branch context |
| Same mobile number used by family members | Shared mobile is a contact signal, not proof that records represent the same person |
| One person uses multiple phone numbers | Phone number is not a stable identity key |
| Same party works with multiple branches | Party may be tenant-wide in identity ownership, but branch staff visibility is restricted by branch assignment and explicit relationship scope |

Household, cooperative, producer group and legal entity semantics require business/legal confirmation where they affect contracting, payment, tax, consent or liability.

## 5. Branch-level visibility and isolation requirements

### 5.1 Product-owner rule

The product owner has explicitly stated:

- Ordinary employees may view only information related to their own assigned branch.
- They must not view data belonging to other branches.
- Specific authorized roles, with business owner given as an example, may view all branches' operational details.
- This is a branch-level isolation requirement, not merely a user-interface filtering preference.

### 5.2 Recommended authorization baseline

1. Tenant isolation remains the outer boundary; no tenant actor can access another tenant's records.
2. For ordinary branch employees, the default effective data scope is exactly their assigned branch. The default must not silently expand to every branch in the tenant.
3. Branch-scoped access applies to reads and writes, including lists, detail lookups, search, aggregates, reports, exports, attachments, downloads, notifications, caches, background jobs, analytics drill-down and integrations.
4. A branch-attributed record is visible only when its branch is within the actor's effective authorized branch scope and the actor has the required action permission.
5. A tenant-wide master record (such as Party) is not automatically visible in full to every branch employee. Its operational association, purpose and branch scope must determine which subset is discoverable. For example, a branch employee may see a customer relationship/service address assigned to that branch without gaining visibility into unrelated branches' customers, transactions or contacts.
6. A tenant-wide record that genuinely has no branch attribution requires an explicit policy defining which roles may view it; absence of a branch ID must never be treated as unrestricted access.
7. Tenant Owner is the explicit example of a role that may receive all-branch operational visibility. This visibility is a scope grant, not an implicit permission to perform every action.
8. Other roles (including business administrators, branch managers, accountants and auditors) do not receive all-branch access merely because of their title. Any cross-branch scope must be expressly assigned, purpose-limited and auditable.
9. Cross-branch read/report access and cross-branch transaction mutation must be separate capabilities. A person may compare reports across branches without being able to post, edit, approve or reverse another branch's transactions.
10. Sensitive fields (identity documents, bank details, payroll, credentials and other restricted personal data) continue to require their own field-level permissions even when the actor has all-branch operational visibility.
11. A user with multiple branch assignments may access only those specifically assigned branches; multi-branch assignment must not be inferred from tenant membership.
12. Role changes, branch transfers, suspension and revocation must update effective scope for subsequent requests and invalidate stale sessions/cached authorization decisions according to the approved revocation policy.

### 5.3 Branch ownership for Party-related data

A shared Party identity root may be tenant-owned, but branch-specific business relationships and activities must retain their own scope. The logical model should distinguish:

- Tenant-wide identity facts that are genuinely shared and permitted for use across the tenant.
- CustomerAccount/SupplierAccount/FarmerProfile role relationships and their branch/operating-location associations.
- Customer service address and servicing branch/area.
- Farmer collection point and the branch/session at which milk was received.
- Branch-specific rates, credit terms, account servicing, procurement settings or assignments where policy requires them.
- Transactional records such as sales, collection entries, receipts, settlements and invoices with their actual business and accounting scope.

Do not copy the entire Party record into every branch merely to simulate isolation. Conversely, do not expose the full tenant-wide Party master to branch staff by default. A future data design must support safe shared identity with branch-scoped relationship visibility.

## 6. Duplicate detection and identity resolution

### 6.1 Matching signals

Duplicate detection may use several signals, with different confidence levels:
- Verified government/business identifier where collection and use are lawful and necessary.
- Verified phone/email contact point.
- Name or normalized business/trade name.
- Address and geospatial proximity where appropriate.
- Bank account/payee identity, subject to strict access and verification.
- External system identifiers and historical aliases.

No single name, mobile number, address or bank account should be treated as a universal identity key. Shared family phones, common names, changed numbers, branch counters and duplicate imports create false positives.

### 6.2 Suggested match classification

| Match outcome | Recommended treatment |
|---|---|
| Exact same verified identifier, same issuing authority and compatible party kind | Strong candidate; still validate tenant scope and conflicting facts |
| Same verified phone only | Possible match; manual confirmation required |
| Similar name and address | Weak candidate; never auto-merge |
| Same external Tally mapping | Investigate mapping/company scope; not proof of identity by itself |
| Conflicting verified identifiers or legal names | Block automatic merge and require review |

Cross-tenant matching must not reveal whether another tenant has a matching Party. No global identity lookup should be exposed to tenant users.

## 7. Merge, correction and audit controls

A duplicate merge must be a governed operation, not physical deletion or blind foreign-key reassignment.

Minimum conceptual controls:
1. Initiator, reviewer/approver, reason and timestamp.
2. Source and surviving target Party IDs, with a preview of affected accounts, contacts, addresses, subscriptions, collection entries, settlements, invoices, receipts, postings, external mappings and documents.
3. Validation that both records belong to the same tenant and that the actor has the required scope across all affected branch records.
4. Detection of incompatible role states, verified identifiers, open balances, legal/tax identity, bank payee, active subscriptions and unresolved disputes.
5. Preserve original identifiers as aliases/merge history and retain the source-to-target resolution map.
6. Preserve immutable transaction/document snapshots and original actor/time/audit evidence.
7. Do not rewrite posted accounting, stock or settlement history. Use linked corrections/reversals where needed.
8. Prevent a merge from moving records across tenant boundaries or bypassing branch visibility.
9. Provide a controlled correction/unmerge or compensating procedure where technically and legally feasible; document limits before approving merge.
10. Log access to sensitive data used in the review.

Whether merge is reversible, who can approve it, and which identity fields are mandatory remain open policy decisions.

## 8. Data protection and sensitive identifiers

- Collect only identifiers needed for a defined business/legal purpose.
- Store identifier type, issuing authority, verification state, verified-at/source and effective status distinctly.
- Apply masking, encryption, least-privilege access, audit and retention controls to sensitive identifiers and bank details.
- Do not use Aadhaar or another sensitive identifier as a default universal Party key; any use requires current legal applicability and purpose review.
- Contact details require purpose, consent/communication preference where applicable, verification and lifecycle handling.
- Contact person, account holder, payee, producer and authorized representative are distinct relationships unless verified otherwise.

This is a design safeguard, not a legal conclusion about the permissibility of collecting any particular identifier.

## 9. ERS-001 refinement work required

Before physical database design, ERS-001 should be refined to specify:
- PartyKind and supported person/organization/household/cooperative scenarios.
- PartyRole lifecycle and whether role assignment can be branch-scoped or tenant-wide.
- CustomerAccount/SupplierAccount/FarmerProfile cardinality and branch association.
- PartyContact, PartyAddress, service address and contact-person relationships.
- Typed external identifiers and verification/retention rules.
- PartyRelationship semantics and authorized representative/payee handling.
- Duplicate detection confidence and merge workflow/audit references.
- Branch ownership/visibility rules for Party-linked accounts, addresses, transactions and search.
- Financial separation of customer receivable and supplier/farmer payable subledgers.
- Tally external master mapping and stable SaaS identity behavior.

No physical schema should be generated from this analysis alone.

## 10. Worked scenarios and acceptance cases

1. **Farmer is also a customer:** One Party with supplier/farmer and customer roles; separate commercial accounts and balances. Branch A employee sees only the permitted Branch A relationship and transactions; Tenant Owner can view both branches under the all-branch grant.
2. **Farmer delivers at two collection centres:** Each collection entry records the actual collection point, branch/session and measurement evidence. Branch A staff cannot inspect Branch B collection entries or settlement detail unless explicitly granted.
3. **Household shares a phone:** Duplicate detection flags a possible match; it does not auto-merge family members.
4. **Cooperative supplies milk from member farms:** Cooperative and individual producers remain separately representable; who contracts with and receives payment from the dairy must be explicitly modelled.
5. **Customer has addresses served by two branches:** Each service address/subscription has a servicing scope. Staff assigned to one branch cannot browse the other branch's address, delivery or account activity.
6. **Duplicate farmer imported twice:** Merge preview identifies all affected collections, settlements, supplier payments and Tally mappings; no posted financial history is rewritten.
7. **Branch employee guesses another branch's Party/account ID:** Backend lookup returns no unauthorized data and does not disclose whether the record exists.
8. **Owner requests consolidated report:** Owner receives the permitted all-branch view; export remains separately authorized and audited.
9. **Branch manager has one assigned branch:** Manager title alone does not grant access to every branch.
10. **Employee moves branches:** Effective assignment and subsequent request authorization change; stale cached or offline data must not extend old access beyond the defined policy.

## 11. Open decisions and dependencies

- ODR-001: legal/business identity types and supported structures.
- ODR-002: branch and operating-location taxonomy.
- ODR-003: multiple legal entities/registrations under one tenant.
- ODR-004: multi-tenant and multi-branch membership/assignment rules.
- ODR-005: final role, permission, scope, delegation and approval matrix.
- ODR-009/010: milk quality and farmer settlement policies.
- ODR-013/014/017: pricing, billing and accounting relationship/offset policy.
- Privacy and retention applicability: verify against current authoritative legal sources before approving identifier collection and retention.

The branch-level isolation rule itself is a product-owner constraint. The final role catalogue, detailed owner permissions, cross-branch mutation rights, branch assignment cardinality and sensitive-field matrix still require controlled specification.

## 12. Consistency review

- Consistent with FRS-001's tenant and branch authorization requirements, customer/supplier/farmer workflows, customer-login exclusion and branch-aware reporting.
- Consistent with ERS-001's proposed Party/PartyRole/CustomerAccount/SupplierAccount/FarmerProfile concepts; this document refines open relationships but does not silently replace ERS cardinalities.
- Consistent with the Actor and Access Model and ARC-IAM-001: roles do not imply scope; backend authorization and cross-branch read/mutate separation are required.
- Consistent with ARC-TEN-001: tenant is the outer isolation boundary and branch scope is independently enforced.
- Does not change ODR statuses or make legal, tax, accounting, privacy or KYC determinations.

## 13. Non-goals

- No application code, SQL, DDL, ORM model, migration, API contract or physical ERD.
- No approval of a final role hierarchy or universal employee/branch assignment rule beyond the stated branch-isolation constraint.
- No legal determination of identity-document collection or retention.
- No customer login, consumer portal or self-service account.
- No destructive merge or automatic identity consolidation.

## 14. Exit criteria

This analysis can feed the next ERS refinement only after:
1. Party kinds and household/cooperative representation are reviewed against target operating scenarios.
2. ODR-004/005 define membership and authorization details without weakening the explicit branch-isolation constraint.
3. Privacy/legal evidence supports any proposed sensitive identifier use.
4. Customer/supplier/farmer account and branch associations are traceable through representative workflows.
5. Duplicate resolution and financial-history preservation are accepted by the relevant business/accounting owner.
6. The revised ERS is cross-checked against FRS, glossary, catalogue, procurement, billing, accounting and Tally documents.
