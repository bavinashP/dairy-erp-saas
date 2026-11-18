# Canonical Business Glossary

**Status:** Working baseline — terminology control  
**Authority:** Product Vision and Scope governs product intent. This glossary standardizes terms; it does not silently settle open decisions.

| Term | Working definition | Distinction / rule |
|---|---|---|
| SaaS Platform | The software service operated by the platform owner | Separate from each dairy tenant's business |
| Platform Owner | Organization/person operating the SaaS product | Platform-level authority; not a dairy tenant owner |
| Tenant | Isolated customer/business workspace within the SaaS | Tenant is a technical and commercial boundary; it is not automatically a legal entity |
| Dairy Business | The real-world dairy operation using the product | May have one or multiple operating locations |
| Tenant Owner | Person accountable for a tenant's dairy business | May personally perform operational duties |
| Branch | A tenant-owned management/operating unit within a dairy business | May be associated with multiple OperatingLocations; it is not itself necessarily a physical premises |
| OperatingLocation | An actual physical/operational site or premises owned by one Tenant | May optionally be associated with a Branch; one Branch may have multiple OperatingLocations; shared use across Branches requires explicit governed association and access rules |
| Operational Capability | A function enabled/performed at an OperatingLocation, such as retail sales, milk collection, storage, processing or packaging | Multiple capabilities may coexist at one physical site; capability labels do not imply separate premises |
| Outlet / Retail Counter | Retail/counter-sales capability or the relevant counter area at an OperatingLocation | Does not require a separate OperatingLocation when retail occurs at a shared premises |
| Collection Centre | Milk-receiving capability or designated receiving area at an OperatingLocation | Does not require a separate OperatingLocation when collection occurs at a shared premises |
| Warehouse / Storage | Storage capability or designated storage area at an OperatingLocation | A physical site may also support retail, collection or processing |
| Stock Location | Inventory-control holding position or subdivision, such as a refrigerator, room, shelf, bin or zone | Separate from OperatingLocation; it need not be an independently addressable physical site |
| Processing / Packaging | Processing or packaging capability at an OperatingLocation | Does not require a separate OperatingLocation when performed at a shared premises |
| Delivery Area / Service Area | Geographic area in which one or more authorized branches serve customers | A Branch may serve multiple ServiceAreas and a ServiceArea may be served by multiple Branches through an explicit effective-dated association; reassignment must not rewrite historical delivery context. Geographic coverage is not a physical OperatingLocation or Branch |
| Route | Ordered or planned set of delivery stops for a run/shift | Route plan can change without changing the customer's subscription |
| Delivery Run | An execution instance of a route/dispatch plan for a date and shift | Distinct from reusable route template |
| Party | Tenant-owned counterparty identity root for a person or organization that may hold one or more business roles | Shared identity does not imply unrestricted branch visibility; see DBR-003 |
| Party Role | Effective business role assigned to a Party, such as customer, supplier, farmer/milk producer or vendor | Role-specific accounts, balances, terms and lifecycle remain distinct |
| Customer | Party acting as a buyer under a customer relationship/account | No customer self-service login is in current product scope |
| Supplier | Party providing goods/services under a supplier relationship/account | A supplier may also hold customer or farmer roles |
| Farmer / Milk Producer | Party acting as a raw-milk producer/supplier | May be an individual or organization; collection and settlement use measured quantity and approved quality/rate rules |
| Product | Sellable or procurable good/service definition | Product identity is distinct from a stock lot or package instance |
| Variant | Defined product option such as size, fat category, flavour, or pack | Only use when it has a distinct operational/commercial identity |
| SKU | Stock-keeping identity used to distinguish inventory-managed items | SKU policy and uniqueness scope require final design |
| Unit of Measure (UOM) | Standard measurement such as litre, kilogram, piece, or pack | Conversion and precision must be explicit and controlled |
| Pack Size | Declared quantity/content of a packaged product | Not interchangeable with selling unit or stock base unit |
| Subscription | Customer agreement/configuration for recurring supply | Not itself a delivery, sale, invoice, or payment |
| Subscription Schedule | Rule describing when and how much is planned for supply | Generates dated occurrences; must be versioned/effective-dated |
| Schedule Occurrence | One dated, shift-specific planned supply instance | Can be changed, skipped, fulfilled, or exceptioned under policy |
| Shift | Operational time window, e.g. morning or evening | Tenant-configurable; avoid hardcoding only two shifts |
| Skip / Pause | Customer-authorized or staff-recorded suppression of planned supply | Must record effective dates, reason/source, and cut-off outcome |
| Fulfilment | Recorded outcome of supplying an order/occurrence | Actual quantity can differ from planned quantity |
| Delivery Attempt | An attempt to complete a delivery stop | Not equal to successful fulfilment |
| POS Sale | Sale recorded at a counter/register | May be immediate-paid or account/credit sale subject to policy |
| Order | Request to supply specified goods at a future or defined time | Distinct from recurring subscription schedule |
| Invoice | Tax/commercial document issued under applicable policy | Not every internal monthly statement is necessarily a tax invoice |
| Customer Statement | Period summary of transactions, charges, credits, and receipts | Statement and invoice have different legal/accounting meanings |
| Receivable | Amount owed to the dairy by a customer | Derived from posted financial events and adjustments |
| Receipt | Record that money was received or acknowledged | Receipt capture does not by itself prove bank settlement |
| Payment Method | Cash, UPI QR, bank transfer, card, or other means used to pay | Method is distinct from payment gateway/provider |
| Payment Gateway | Third-party service processing online payments | UPI QR or direct bank transfer does not imply gateway integration |
| Reconciliation | Matching recorded transactions with external evidence/settlement | Manual reconciliation must remain supported |
| Procurement | Acquisition of milk, products, services, or materials | Milk collection may require a specialized workflow |
| Milk Collection | Recording raw milk received from a farmer/producer | Capture quantity, time, source, and relevant quality evidence |
| Quality Test | Measurement/assessment of a sample or product | Parameters and acceptance policy depend on product/process |
| Milk Collection Entry | Evidence record of raw milk received from a producer | Distinguish submitted, measured, accepted and rejected quantities |
| Sample | Material portion taken for testing | Must retain linkage to source collection/lot |
| Quality Result | Recorded value and outcome for a test parameter | Not tested, missing, zero and failed are distinct states |
| Rate Chart | Versioned rule for calculating supplier/milk procurement rates | Effective-dated; historical calculations retain applied version |
| Farmer Settlement | Periodic calculation of amount payable to a farmer/producer | Separate from individual collection and payment transaction |
| Quarantine | Restricted stock status pending review or test | Not available for normal allocation until released |
| Raw Milk Lot | Traceable group of accepted raw milk | May aggregate multiple collection entries under lineage rules |
| Raw Milk | Milk received before the tenant's processing/packing step | Distinct from packaged finished goods |
| Batch / Lot | Traceable group of material or products sharing production/receipt identity | Traceability depth depends on activity profile and applicable rules |
| Processing | Transformation of raw materials into another product | Optional capability; requires yield, loss, and traceability controls |
| Stock Movement | Auditable change in stock quantity/location/status | Source document and movement should be linked |
| Stock Ledger | Authoritative record of posted stock movements | Stock balances are derived from controlled movements |
| Stock Status | Availability classification of inventory | Available, reserved, quarantined, damaged, expired and in-transit must remain distinct |
| Stock Reservation | Commitment of available stock to planned demand | Not automatically a physical issue or sale |
| Stock Transfer | Movement of stock custody between authorized locations | Not a sale or purchase; dispatch and receipt may be separate events |
| Physical Stock Count | Recorded physical quantity verification | Variances require controlled adjustment and audit |
| Supplier Batch | Original batch reference assigned by supplier/manufacturer | Not assumed globally unique |
| Production Batch | Traceable execution of a defined production process | Retains process version, inputs, outputs and deviations |
| Finished Goods Lot | Traceable quantity produced/packed as a lot | Retains source and production lineage |
| FEFO | First Expiry, First Out allocation strategy | Candidate strategy; product and tenant policy may vary |
| FIFO | First In, First Out allocation strategy | Receipt chronology-based strategy; not equivalent to FEFO |
| Yield | Output quantity relative to defined input quantity | Formula must state units, process stage and by-product treatment |
| Work in Progress (WIP) | Material currently within a production process | Distinct from raw material and released finished goods |
| By-product | Secondary output generated by a process | Requires explicit quantity and cost treatment |
| Process Loss | Input quantity not recovered as saleable output/by-product | Must be measured or estimated under a defined method and reason |
| Stock Adjustment | Controlled correction for count variance, damage, loss, or other reason | Must not silently overwrite stock balance |
| Accounts Payable | Amount owed to suppliers | Distinct from supplier master and payment record |
| Chart of Accounts (CoA) | Structured hierarchy of ledger accounts | Tenant/books-specific and version-controlled |
| General Ledger (GL) | Accounting record of posted financial effects | Operational subledgers should reconcile to GL under defined rules |
| Journal Entry | Balanced set of debit and credit ledger lines | Posted entries are immutable; corrections use reversal/adjustment |
| Voucher | Business/accounting document representing a transaction | Voucher type and numbering are configurable and mapped externally |
| Cost Centre | Analytical dimension for attributing income/cost | Not automatically a legal entity or branch |
| Trial Balance | Period-end listing of ledger balances | Total debits must equal total credits |
| Financial Period | Configured accounting interval | Closed-period posting requires controlled authorization |
| Reversal | Linked accounting entry that negates a prior posting | Does not delete or rewrite the original journal |
| Tally Mapping | Versioned mapping between SaaS identity and Tally master/voucher identity | External mapping; never primary SaaS identity |
| Synchronization Batch | Auditable set of records exchanged with an external system | Must support idempotency, checksums, results and retry |
| General Ledger (GL) | Accounting record of posted financial effects | Operational subledgers should reconcile to GL under defined rules |
| Business Date | Tenant-defined operational date used for shifts/cut-offs | May differ from server timestamp/calendar date |
| Effective Date | Date/time from which a rate, rule, or configuration applies | Historical transactions retain the applied version |
| Entitlement | Feature/limit granted by a SaaS plan or approved override | Entitlement does not replace authorization |
| Configurability | Ability to change supported settings without code changes | Must be bounded by validated rules and safe defaults |
| Extensibility | Ability to add capabilities/integrations without destabilizing core contracts | Not permission to create arbitrary inconsistent business rules |
| Projection / Forecast | Estimated future outcome based on data and assumptions | Never present as actual or guaranteed value |
| KPI | Defined measurable indicator with formula, grain, and owner | Every KPI needs consistent calculation semantics |

| Expense Claim | Request to reimburse an employee or authorized person for business spending | Distinct from vendor bill and actual payment |
| Petty Cash Float | Controlled small cash balance assigned to a custodian/location | Balance is derived from recorded cash movements |
| Cash Custodian | Authorized person responsible for a cash float/drawer | Custody and accounting ownership are distinct |
| Bank Statement Line | External bank transaction evidence | Distinct from an internally recorded receipt/payment |
| UTR | Unique Transaction Reference supplied for a bank transfer | User-entered reference is not proof of settlement until verified |
| Bank Reconciliation | Matching internal cash/bank records with external statement evidence | Matching must not rewrite source transactions |
| Payroll Period | Defined interval for calculating employee compensation | Finalized periods require controlled correction |
| Payroll Component | Configurable earning, deduction, contribution or reimbursement element | Effective-dated with calculation and ledger mapping |
| Statutory Rule | Versioned rule with applicability, source and effective date | Requires authoritative verification and review |
| Tax Classification | Effective-dated tax treatment metadata for a product or transaction | Must not be inferred solely from product name |
| Budget Version | Approved or draft plan for a defined period and dimension set | Revisions retain previous approved versions |
| Forecast | Estimated future outcome based on historical/current data and assumptions | Distinct from actual and approved budget |
| Scenario | Alternative set of assumptions used to compare possible outcomes | Must not create or alter posted transactions |
| KPI Definition | Versioned formula and metadata for a key performance indicator | Includes source, grain, date basis and inclusion rules |
| Reconciliation Exception | Unmatched, duplicate, inconsistent or unsupported item requiring review | Must retain evidence and resolution history |

## Terminology rules

1. Use Tenant for the SaaS isolation boundary and Dairy Business for the real-world operation.
2. Do not use Branch, Outlet, Warehouse, Collection Centre, Processing Unit, and Delivery Area interchangeably.
3. Distinguish plan, schedule, occurrence, order, fulfilment, invoice, statement, receipt, and reconciliation.
4. Distinguish payment capture/recording from provider confirmation and bank settlement.
5. Distinguish actual, target, budget, forecast, and projection in every report.
6. Where a term has legal meaning, verify it against applicable Indian law and official guidance before using it in customer-facing documents.
7. New domain terms must be added here before being independently redefined in module documents.
