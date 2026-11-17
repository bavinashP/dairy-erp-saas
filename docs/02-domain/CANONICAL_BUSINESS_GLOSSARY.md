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
| Branch | A named operating unit/location of a dairy business | Exact relationship to legal registration and other location types remains open |
| Outlet / Retail Counter | Physical point where walk-in/counter sales occur | May be located at a branch but should not be assumed identical to a branch |
| Collection Centre | Location where milk is received from farmers/suppliers | May be a branch or a separately modelled operational location |
| Warehouse / Stock Location | Place where stock is stored and controlled | A branch may contain multiple stock locations |
| Processing Unit | Facility where raw material is processed or packed | Optional for businesses that only resell packaged products |
| Delivery Area / Service Area | Geographic area in which a branch serves customers | Not necessarily a physical branch |
| Route | Ordered or planned set of delivery stops for a run/shift | Route plan can change without changing the customer's subscription |
| Delivery Run | An execution instance of a route/dispatch plan for a date and shift | Distinct from reusable route template |
| Customer | Person or organization buying goods/services from the dairy | No customer self-service login is in current product scope |
| Supplier | Party providing goods/services to the dairy | Farmer is a supplier subtype or related party, not necessarily a separate identity |
| Farmer / Milk Producer | Person/entity supplying raw milk | May be paid based on measured quantity and quality/rate rules |
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
| Raw Milk | Milk received before the tenant's processing/packing step | Distinct from packaged finished goods |
| Batch / Lot | Traceable group of material or products sharing production/receipt identity | Traceability depth depends on activity profile and applicable rules |
| Processing | Transformation of raw materials into another product | Optional capability; requires yield, loss, and traceability controls |
| Stock Movement | Auditable change in stock quantity/location/status | Source document and movement should be linked |
| Stock Adjustment | Controlled correction for count variance, damage, loss, or other reason | Must not silently overwrite stock balance |
| Accounts Payable | Amount owed to suppliers | Distinct from supplier master and payment record |
| General Ledger (GL) | Accounting record of posted financial effects | Operational subledgers should reconcile to GL under defined rules |
| Business Date | Tenant-defined operational date used for shifts/cut-offs | May differ from server timestamp/calendar date |
| Effective Date | Date/time from which a rate, rule, or configuration applies | Historical transactions retain the applied version |
| Entitlement | Feature/limit granted by a SaaS plan or approved override | Entitlement does not replace authorization |
| Configurability | Ability to change supported settings without code changes | Must be bounded by validated rules and safe defaults |
| Extensibility | Ability to add capabilities/integrations without destabilizing core contracts | Not permission to create arbitrary inconsistent business rules |
| Projection / Forecast | Estimated future outcome based on data and assumptions | Never present as actual or guaranteed value |
| KPI | Defined measurable indicator with formula, grain, and owner | Every KPI needs consistent calculation semantics |

## Terminology rules

1. Use Tenant for the SaaS isolation boundary and Dairy Business for the real-world operation.
2. Do not use Branch, Outlet, Warehouse, Collection Centre, Processing Unit, and Delivery Area interchangeably.
3. Distinguish plan, schedule, occurrence, order, fulfilment, invoice, statement, receipt, and reconciliation.
4. Distinguish payment capture/recording from provider confirmation and bank settlement.
5. Distinguish actual, target, budget, forecast, and projection in every report.
6. Where a term has legal meaning, verify it against applicable Indian law and official guidance before using it in customer-facing documents.
7. New domain terms must be added here before being independently redefined in module documents.
