# Open Decisions Register

**Status:** Initial register  
**Rule:** An open item is not an approved requirement. Do not silently resolve it through implementation or documentation wording.

| ID | Area | Open decision / question | Why it matters | Required evidence / investigation | Status |
|---|---|---|---|---|---|
| ODR-001 | Business identity | Which legal/business structures must a tenant support (proprietorship, partnership, company, cooperative, etc.)? | Profile, compliance, ownership, reporting | Indian business practice and legal research; representative scenarios | Open |
| ODR-002 | Branch model | Define branch, outlet, collection centre, warehouse, processing unit, and delivery area. | Avoids conflating operational locations and legal registrations | Domain glossary and workflow analysis | Open |
| ODR-003 | Tenant hierarchy | Can one tenant contain multiple legal entities or GST registrations? | Tax, accounting, ownership | Legal/tax research and business scenarios | Open |
| ODR-004 | User membership | Can a user belong to multiple tenants or branches? What is the default assignment? | Identity, authorization, session context | Access scenarios and threat analysis | Open |
| ODR-005 | Authorization | What roles, permission actions, scopes, delegation, and approval limits are needed? | Cross-module access and segregation of duties | Actor catalogue and permission matrix | Open |
| ODR-006 | Subscription lifecycle | Define pause, resume, skip, holiday, temporary change, substitution, cancellation. | Delivery, billing, stock, balances | Customer lifecycle scenarios and exceptions | Open |
| ODR-007 | Subscription schedule | Define cut-offs, schedule changes, alternate-day cycles, branch/route calendars. | Delivery planning and correctness | Schedule examples and exception analysis | Open |
| ODR-008 | Units and precision | Which units, conversions, precision, rounding, and pack sizes are supported? | Procurement, inventory, pricing, billing | Product and measurement-domain analysis | Open |
| ODR-009 | Milk quality | Which quality parameters, sampling, acceptance/rejection, and traceability are required? | Procurement settlement, processing, compliance | Dairy operations and food-safety research | Open |
| ODR-010 | Procurement settlement | Define rates, quality incentives/penalties, deductions, advances, settlement cycles. | Supplier balances and accounting | Real procurement models and worked calculations | Open |
| ODR-011 | Processing | Define stages, batches, yields, losses, packaging, and traceability. | Manufacturing, stock, cost, recall | Process mapping for target dairy sizes | Open |
| ODR-012 | Inventory | Define stock ownership, warehouses, transfers, expiry, batch, wastage, adjustments. | Stock accuracy and audit | Inventory lifecycle and exception analysis | Open |
| ODR-013 | Pricing | Define price lists, customer rates, effective dates, discounts, deposits, approvals. | POS, subscriptions, profitability | Pricing scenarios and policy analysis | Open |
| ODR-014 | Billing | Define billing cycles, invoice/statement model, advances, credit limits, proration. | Receivables and financial reporting | End-to-end scenarios and legal review | Open |
| ODR-015 | Payment verification | Which payment sources can be reconciled automatically versus manually recorded? | Avoids false verification claims | Bank/provider integration capability research | Open |
| ODR-016 | GST and invoicing | Determine tax treatments, invoice formats, e-invoicing/e-way bill applicability, retention. | Legal and financial correctness | Current authoritative Indian tax sources and applicability matrix | Open |
| ODR-017 | Accounting | Define ledgers, vouchers, chart of accounts, tax, bank reconciliation, statements, integrations. | Finance domain and integration boundaries | Accountant workflows and statutory research | Open |
| ODR-018 | HR/payroll | Define attendance, shifts, leave, salary, deductions, statutory payroll, contractor workflows. | Scope and legal obligations vary | Indian labour/payroll research and scenarios | Open |
| ODR-019 | Mobile apps | One role-aware app or multiple role-focused apps? | UX, distribution, support, release management | User journeys and maintenance comparison | Open |
| ODR-020 | Offline operation | Which workflows must work offline, for how long, and how do conflicts synchronize? | Field connectivity and operational continuity | Field workflow and conflict-resolution analysis | Open |
| ODR-021 | Notifications | Define channels, templates, consent, retries, delivery tracking, provider fallback. | Reliability, cost, privacy | Provider and legal research | Open |
| ODR-022 | Analytics | Define standard KPIs, dimensions, drill-downs, history, custom report capability. | Data and reporting architecture | Role-based MIS catalogue and sample reports | Open |
| ODR-023 | Forecasting | Which demand, procurement, sales, cash-flow, inventory, and staffing forecasts and horizons? | Data quality and planning value | Forecast use cases and validation metrics | Open |
| ODR-024 | Budgeting | Define budget dimensions, approvals, scenarios, and variance controls. | Finance and management planning | Budget workflows and worked scenarios | Open |
| ODR-025 | SaaS plans | Define plans, entitlements, limits, trials, upgrades, downgrades, grace, suspension. | SaaS commercial operations | Platform billing and entitlement scenarios | Open |
| ODR-026 | Data lifecycle | Define retention, archival, export, deletion, offboarding, legal holds. | Privacy, compliance, storage | Applicable law and customer-contract research | Open |
| ODR-027 | Scale targets | Estimate tenants, branches, users, transactions, concurrency, and data volumes. | Capacity and architecture | Conservative/base/high growth scenarios | Open |
| ODR-028 | Availability/recovery | Define uptime, RPO, RTO, backup frequency, disaster-recovery objectives. | Operational design and cost | Business impact analysis and service tiers | Open |
| ODR-029 | Integrations | Which external systems and APIs are priority? | API, event, adapter boundaries | Integration inventory and prioritization | Open |
| ODR-030 | Technology | Which languages, frameworks, databases, queues, caches, analytics, deployment model? | Maintainability and operating cost | Requirements-led comparison and proof of concept | Deferred |
| ODR-031 | Deployment topology | Define production topology, regions, network boundaries, tenant isolation tiers. | Security, resilience, scale, cost | Threat model, workload model, provider evaluation | Open |
| ODR-032 | Localization | Which languages, translation workflow, number formats, localized templates are needed? | Consistent UX and compliance | User research and terminology glossary | Open |

## Decision protocol
For each item: gather real scenarios and exceptions; research authoritative sources where legal, tax, regulatory, or provider claims are involved; identify alternatives and consequences; recommend only when evidence is sufficient; record decision owner and explicit approval; update dependent requirements, architecture, and acceptance criteria.
