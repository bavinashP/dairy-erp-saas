# Procurement Lifecycle and Controls

**Status:** Working baseline — domain requirements  
**Related open decisions:** ODR-001, ODR-002, ODR-008, ODR-009, ODR-010, ODR-011, ODR-012, ODR-016, ODR-017  
**Scope:** Procurement of raw milk from farmers/producers and goods from companies, distributors, wholesalers, and other suppliers.

## 1. Objectives and operating profiles

The procurement capability must support materially different business profiles without forcing every tenant through a manufacturing-grade process.

| Operating profile | Typical workflow |
|---|---|
| Small retailer buying sealed branded products | Supplier bill/receipt → stock → supplier payable/payment |
| Dairy buying loose milk for repacking | Farmer/milk collection → quantity and optional quality capture → accepted raw milk → packing/processing or direct dispatch |
| Dairy processing milk into products | Collection → quality/acceptance → raw milk inventory → processing batch → finished goods |
| Multi-branch operator | Central or branch procurement, inter-location transfers, branch-level accountability |
| Owner-operated business | Owner performs receipt, verification, and settlement without mandatory employee setup |

Feature availability may be configurable, but core stock and financial integrity controls must not be disabled.

## 2. Procurement categories

Keep separate business paths for:
1. Raw milk procurement / milk collection.
2. Packaged and non-milk goods procurement.
3. Consumables and packaging materials.
4. Services and operating expenses (may use a finance/expense workflow rather than stock receipt).

A purchase order is useful for planned or approval-based purchasing, but must not be mandatory for small cash-and-carry suppliers. Direct purchase/receipt must remain possible under tenant policy.

## 3. Standard goods procurement lifecycle

Candidate workflow:

1. Supplier and supplier terms are selected.
2. Purchase request or purchase order is created where the tenant uses it.
3. Goods arrive at a specified receiving location.
4. Receiver records supplier document reference, receipt time, products, quantities, units, rates and applicable charges.
5. Received quantity is checked against ordered quantity where an order exists.
6. Short, excess, damaged, wrong, expired or rejected goods are recorded separately.
7. Accepted quantity is posted to the appropriate stock location.
8. Supplier liability is recorded from the approved source document/policy.
9. Supplier payment and allocation are recorded separately.
10. Supplier statement/reconciliation and reporting are updated.

The system must distinguish ordered, received, accepted, rejected, returned, and invoiced quantities. Differences must be visible and reason-coded.

## 4. Controls and exceptions

Support configurable controls for:
- Purchase approval thresholds and role limits.
- Supplier/product restrictions and active status.
- Duplicate supplier invoice/reference detection.
- Purchase order versus receipt variance.
- Rate variance against approved supplier terms.
- Quantity/UOM validation and conversion.
- Damaged, expired, short, excess, and rejected goods.
- Supplier returns and debit/credit adjustments.
- Backdated entries and closed-period restrictions.
- Branch/location authorization.
- Attachments such as invoice, delivery challan, weighment evidence, or quality document.

Duplicate detection must be robust but must not assume supplier invoice numbers are globally unique. Define a tenant/supplier/document-type uniqueness policy and allow controlled override with reason where justified.

## 5. Stock and payable posting

A goods receipt and supplier liability are related but distinct effects:
- Stock increases only for accepted stock quantity under the inventory policy.
- Rejected goods do not become available stock.
- A supplier invoice may arrive before, with, or after physical receipt; supported matching rules must be defined.
- Advance payments are not automatically proof of goods receipt.
- Supplier payment must be allocated to one or more payable items or recorded as an unapplied advance.
- Returns and purchase corrections must create linked reverse/adjustment movements and financial effects.

No transaction should update only a displayed balance while omitting its auditable source movement.

## 6. Branch and central procurement

The system should support:
- Procurement by a branch.
- Central procurement on behalf of branches.
- Receipt into a central stock location followed by transfer.
- Direct receipt into a branch.
- Supplier serving multiple branches under shared or branch-specific terms.

The system must explicitly record purchasing responsibility, receiving location, stock-owning scope, and payable-owning scope. These may differ and must not be inferred from the logged-in user's branch.

## 7. Supplier commercial terms

Potential terms include:
- Standard purchase rate or price list.
- Effective dates and currency fixed to INR for this India-focused product.
- Credit period and due date.
- Advance and deposit terms.
- Discounts, freight, handling and other charges.
- Tax treatment based on verified applicability.
- Return and quality claim terms.

Rates and terms must be snapshotted on the transaction. Later supplier master changes must not rewrite historical purchase calculations.

## 8. Reporting requirements

Minimum procurement views:
- Purchases by supplier, product, branch, and period.
- Ordered versus received versus accepted quantity.
- Purchase price and rate variance.
- Rejected/returned/damaged quantity and value.
- Supplier outstanding, ageing, advances, and payments.
- Purchase concentration and supplier dependency.
- Raw milk procurement quantity/rate/quality trends (where captured).
- Procurement cost contribution to product margin.

Every report must define whether it uses document date, receipt date, posting date, or payment date.

## 9. Acceptance scenarios

1. Record direct purchase without purchase order.
2. Receive against an existing order with short/excess quantity.
3. Record damaged/rejected goods without increasing available stock.
4. Prevent duplicate invoice posting or require controlled override.
5. Receive centrally and transfer to a branch.
6. Record supplier invoice separately from physical receipt when workflow requires.
7. Record advance payment and later allocate it.
8. Return goods and generate linked stock and payable adjustments.
9. Change supplier rate prospectively without changing prior transactions.
10. Prevent unauthorized branch user from receiving or viewing another branch's restricted records.
11. Retry a posting request without duplicate stock or payable effects.
12. Detect concurrent receipt edits and require conflict resolution.

## 10. Open questions

- Which procurement profiles are enabled by default for each tenant type?
- Can suppliers have shared tenant terms and branch overrides?
- What are approval thresholds and tolerance bands?
- How are supplier invoice, receipt, and payable matching handled?
- Which tax/accounting documents are required for each transaction type?
- Which goods require batch, expiry, serial, or lot tracking?
- How are packaging materials and reusable containers accounted for?
- Which procurement actions can be performed offline?
- What supplier payment methods and reconciliation evidence are supported?

No statutory tax treatment, mandatory approval threshold, or universal purchase-order requirement is decided here.
