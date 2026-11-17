# Dairy ERP SaaS

A comprehensive, India-focused, multi-tenant Dairy Enterprise Resource Planning (ERP) SaaS product.

## Product direction
The product supports dairy businesses of different sizes and operating models, from owner-operated local dairies to multi-branch businesses with dedicated procurement, processing, sales, distribution, finance, and administrative teams. Delivery may be phased; the comprehensive product scope must not be reduced by phase planning.

## Architectural principles
- API-first backend for web and mobile clients.
- SaaS multi-tenancy with strict tenant data isolation.
- One tenant may operate one or many branches.
- GST registration and branch management are independent concepts.
- Authentication, authorization, data integrity, and tenant isolation are platform-enforced.
- Business policies are classified as fixed, configurable, or extensible.
- Idempotency and transaction integrity are required for financially or operationally material workflows.
- Reporting, BI, historical analysis, forecasting, projections, budgeting, and scenario planning are first-class capabilities.
- Production architecture is designed for business needs and scale, not constrained by the development VPS.

## India-focused baseline
- INR is the required primary currency.
- One consistent Indian date-format convention; IST as the default business time zone.
- English is the primary language; Marathi, Hindi, and other Indian languages must be supportable.
- Support cash, UPI QR, bank transfer, and optional payment-gateway integrations.
- Payment recording is distinct from payment collection and provider-side verification.
- GST applicability must not be used as a proxy for branch eligibility.

## Documentation
See [Documentation Structure](docs/DOCUMENTATION_STRUCTURE.md) for the catalogue and governance workflow.

Initial foundation:
- [Project Vision and Scope](docs/01-product/PROJECT_VISION_AND_SCOPE.md)
- [Open Decisions Register](docs/01-product/OPEN_DECISIONS_REGISTER.md)

This is a new, independent project. Do not import decisions or implementation assumptions from unrelated projects without separate evaluation and explicit approval.
