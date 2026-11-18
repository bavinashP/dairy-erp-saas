# Project Vision and Product Scope

**Status:** Product direction approved — comprehensive scope; detailed domain/legal/technology gates remain governed by the Open Decisions Register  
**Product:** Dairy ERP SaaS  
**Primary market:** India

## 1. Vision
Build one comprehensive, configurable, extensible, scalable and reliable Dairy ERP SaaS platform for Indian dairy businesses from the smallest owner-operated dairy to large multi-branch and enterprise operations. The same product must cover the complete business lifecycle from procurement and milk handling through processing, inventory, sales, subscriptions, distribution, billing, finance, people management, compliance, analytics and administration. It must scale down in complexity and cost for small operators and scale up in capability, workload, governance and infrastructure for large businesses without requiring separate product forks. It is not merely a milk-delivery, subscription or retail-billing application.

## 2. Outcomes
The platform is intended to help a dairy business:
- Operate one or multiple branches in one business system.
- Manage customers, recurring subscriptions, ad-hoc orders, and counter sales.
- Manage suppliers/farmers, milk procurement, collection, quality, processing, and packaging.
- Manage products, pricing, inventory, warehouses, routes, and delivery.
- Manage invoices, customer balances, receipts, payments, and receivables.
- Manage finance, employees, assets, maintenance, and compliance workflows.
- Monitor operations through reports, MIS, dashboards, KPIs, and financial analysis.
- Use historical data, trends, forecasts, projections, budgets, and scenario analysis.
- Configure business policies without weakening security or data integrity.

## 3. Target operating models
The product must accommodate owner-operated dairies, local milk vendors, retail distribution centres, doorstep delivery businesses, loose and packaged milk sellers, farmer-procurement businesses, branded-product resellers, processing/packaging businesses, single-branch and multi-branch businesses, and businesses with either combined or specialized staff roles. These models may coexist within one tenant.

## 4. Users and customer-access boundary
Authenticated users are authorized dairy-business personnel and authorized SaaS platform operators. Customer ERP accounts, customer portal access, and consumer e-commerce storefront functionality are outside the current scope. Customers, subscriptions, orders, balances, and delivery preferences are managed by authorized business personnel. A future customer self-service channel requires a separately approved scope decision.

Potential business functions include platform operations, business ownership, administration, accounting, sales, POS, procurement, inventory, delivery, HR and audit. These are illustrative responsibilities, not mandatory job titles or predefined privilege classes. Access is determined by effective explicit permissions and scope, never by job title, role title or account type. The permission-first model and cross-Branch stock-task matrix are approved. The canonical product-wide atomic permission-key inventory v1 is selected in ARC-IAM-CAT-001 section 15 / ODR-005 section 16.39; exact domain thresholds, resource/state/API mappings, sensitive-field classifications and automated acceptance coverage remain module readiness gates.

## 5. Comprehensive capability scope
1. SaaS Platform Administration
2. Business and Branch Management
3. User, Role, and Permission Management
4. Customer and Subscription Management
5. Supplier and Farmer Management
6. Milk Procurement and Collection
7. Milk Processing and Packaging
8. Product and Pricing Management
9. Inventory and Warehouse Management
10. Sales and POS Management
11. Distribution and Route Management
12. Billing and Receivables Management
13. Accounting and Financial Management
14. HR and Payroll Management
15. Quality Control and Compliance Management
16. Asset and Maintenance Management
17. Business Intelligence and Analytics
18. Reporting and MIS
19. Forecasting and Projections
20. Budgeting and Scenario Planning
21. Notifications and Workflow Management
22. Integration and API Management
23. Audit, Security, and Monitoring

Domain analysis may identify additional capabilities. Module names do not prescribe technical service boundaries. Capabilities are composable and may be activated according to business need and plan entitlement. Phased delivery must not imply permanent omission or reduction of this comprehensive scope.

## 6. Subscription, order, and sales patterns
Customer subscription schedules must be capable of representing daily, alternate-day, selected-weekday, every-two/three-day, morning, evening, and morning-plus-evening patterns, subject to detailed business-rule analysis. Customers may purchase additional products outside recurring subscriptions. The system must distinguish recurring schedules/entitlements from one-time or ad-hoc orders. Cut-off, pause, resume, holiday, skip, substitution, cancellation, and proration rules remain to be specified. Billing may occur month-end or on another approved business cycle. Counter/POS sales coexist with subscription and delivery operations.

## 7. SaaS and organizational model
The conceptual model includes SaaS platform administration, a dairy business as a tenant, one or many branches/operating locations per tenant, tenant-wide and branch-scoped operations, SaaS plans/entitlements, tenant business configuration, permission-first authorization and governed extension management. Plan entitlements govern capability availability and usage allowance; Tenant configuration governs how an enabled capability operates; permissions govern who may view or act; extensions add capabilities through approved contracts. These are independent control dimensions. GST registration and branch management are independent. A tenant may have multiple branches regardless of GST registration status, subject to the legal requirements applicable to its actual activities. Legal-entity, registration, invoicing, and branch relationships require further analysis.

## 8. API and client strategy
The backend must be API-first. Web portals and mobile applications use the same backend business capabilities and authorization model. No business role is inherently restricted to web or mobile; access depends on permission, session/device policy, and operational needs. The initial direction is one unified mobile application with permission-, task- and context-aware workspaces; separate apps require evidence of material security, usability, device, distribution or lifecycle benefit under ODR-019. Business logic and authorization must not be duplicated across clients.

## 9. India-focused baseline
- INR is the required primary currency; multi-currency is not required.
- Use one consistent Indian date-format convention; do not introduce alternative date-format settings.
- IST is the default business time-zone convention, with correct timestamp handling.
- English is the primary product language; Marathi, Hindi, and other Indian languages must be supportable.
- Consider Indian addresses, PIN codes, mobile numbers, banking, and payment practices.
- Payment methods include cash, UPI QR, bank transfer, and optional payment gateway.
- Payment recording is distinct from payment collection and provider-side verification.
- Manually recording a payment against a personal bank account or UPI QR must not imply automatic verification.
- GST registration/applicability must not be treated as a proxy for branch count.
- Legal, tax, privacy, labour, food-safety, and other compliance requirements require current authoritative research before being marked verified.

## 10. Fixed, configurable, customizable, and extensible boundaries
### Fixed platform controls
Tenant isolation, authentication/authorization enforcement, data integrity, transaction consistency, auditability of sensitive/material actions, concurrency safety, and idempotency for retryable operations where duplicates could cause harm. Tenant configuration cannot disable these controls.

### Configurable business policies
Examples include branch/location structure, enabled capabilities, pricing, delivery schedules, billing cycles, payment methods, stock handling, employee availability, approval thresholds, operational workflows, report settings and integration profiles. Configuration must use documented keys, allowed values/scopes, inheritance, effective dating, validation, versioning, permissions and audit. Branch/site overrides are allowed only for explicitly overrideable settings.

### Customizable customer experience
Supported customization may include branding, labels, local terminology, forms, document templates, dashboards, safe custom metadata fields, saved reports and supported workflow layouts. Customization must preserve canonical business meaning, data ownership, historical integrity and upgrade compatibility. It must not rely on per-customer code forks, arbitrary SQL/DDL or unrestricted runtime scripts.

### Extensible capabilities
Examples include payment providers, accounting integrations, messaging services, external systems, device adapters, new modules, reports and future clients. Extensions use versioned APIs/events/contracts, declared permissions, tenant binding, resource limits and lifecycle governance. Extensions must not bypass authorization, tenant isolation, audit or data-integrity controls.

The authoritative cross-product rules for configuration layering, customer customization, extension governance, module boundaries, tenancy tiers and scale evolution are in [Product Configurability, Customization, Extensibility and Scale Policy](../04-architecture/PRODUCT_CONFIGURABILITY_CUSTOMIZATION_EXTENSIBILITY_AND_SCALE_POLICY.md).

## 11. Quality attributes
Requirements and architecture must address scalability, performance, configurability, extensibility, modularity, maintainability, reusability, backward compatibility, reliability, availability, concurrency safety, transaction integrity, idempotency, security, privacy, auditability, observability, backup/restore, disaster recovery, localization, accessibility, integration/API lifecycle, and relevant offline/poor-connectivity operation. Numeric SLOs, recovery objectives, performance thresholds, and offline guarantees remain open until quantified.

## 12. Environment and infrastructure constraints
The currently available VPS CPU/RAM/storage limits apply to development only. They must not reduce production scope or production architecture capabilities. Production must be designed against expected business scale, workload, availability, security, compliance, and operations. Development should use available resources efficiently. Docker is not mandatory and is not selected. Direct VPS deployment remains a candidate pending evaluation.

## 13. Explicit non-decisions
Programming language, framework, database, cache, queue, search and analytics engine remain subject to technology evaluation and ADR. The initial modular-monolith direction is selected, with service extraction evidence-gated under ARC-PV-001. Hosting provider, physical tenant-isolation topology, Docker/orchestration, API protocol/versioning details, provider selections, detailed domain policies, legal interpretations and numeric SLO/RPO/RTO/retention targets remain governed by their specific ODRs.

## 14. Scope integrity
Phasing is a delivery strategy, not permission to omit approved capabilities. A proposed scope change must be recorded, impact-assessed, and approved. Unknowns remain visible in the Open Decisions Register rather than being filled by assumption.
