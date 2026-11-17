# Payroll and Employee Financial Transactions

**Status:** Working baseline — domain requirements

## 1. Design principles
Support businesses with no employees, a few informal/part-time workers, and larger teams with structured payroll. HR attendance and payroll are related but distinct. Payroll rules must be effective-dated, explainable and versioned. Statutory applicability depends on establishment, headcount, wage, location, worker classification and current law; do not assume every tenant is subject to every regime.

## 2. Employee and engagement data
Reference the employee/personnel master and engagement type (employee, part-time, temporary, contractor or other reviewed category). Store only necessary personal, bank/payment and identity data with restricted access and retention rules. Avoid collecting sensitive identifiers unless a documented requirement exists. Track joining/exit dates, branch, role, pay group, compensation version and payment preference.

## 3. Compensation structure
Support configurable earnings, deductions, employer contributions and reimbursements. Components may include fixed pay, hourly/daily wages, attendance-linked pay, overtime where lawful, incentives, allowances, advances, recoveries and approved deductions. Each component has calculation basis, effective dates, taxable/statutory classification metadata, rounding and accounting mapping. Do not hardcode one universal salary formula.

## 4. Payroll period and processing
Draft period → collect attendance/leave/approved variable inputs → calculate → exception review → approval → lock/finalize → payslip → payment record → accounting posting → reconciliation. Provide preview and variance against prior period. Recalculation after approval requires controlled reopen/versioning and audit.

## 5. Attendance and operational linkage
Consume approved attendance/shift/leave data, but do not infer attendance solely from a delivery or POS login. Support manual correction with reason and approval. Missing attendance, overtime, leave, joining/exit mid-period and unpaid absence need explicit policy.

## 6. Advances, loans and reimbursements
Employee advance/loan is a separate receivable or recoverable balance, not a negative salary component with no history. Track disbursement, schedule, authorized deductions, partial recovery, waiver and final settlement. Reimbursements flow through expense management and should not be duplicated in payroll unless configured as a payroll component.

## 7. Statutory configuration
Provide a versioned statutory rules layer with jurisdiction, effective date, applicability conditions, thresholds, rates, caps, rounding, required evidence and source references. Potential regimes such as EPF, ESI, professional tax, labour welfare fund, income-tax withholding and applicable wage/working-time rules require establishment-specific legal assessment. The application must not present a tenant as compliant solely because a toggle is enabled. Keep statutory calculation engine and filing/export adapters independently testable and updateable.

## 8. Accounting
Approved payroll creates a balanced journal according to configured chart and policy: compensation expense, employer contribution expense, employee payable, statutory payable, advance recovery and bank/cash settlement. Payroll register, payslip, payment and journal must share period and batch references. Salary payment can be partial; payable remains until settled.

## 9. Access and privacy
Restrict salary, bank details, identity documents and payslips to authorized roles and employee self-access only if separately approved in product scope. Customer-facing access is not implied. Log sensitive record access and exports. Mask sensitive fields by default.

## 10. Reports
Payroll register, payslip, salary component breakdown, branch/department labour cost, attendance variance, advance outstanding, reimbursement, payroll payable, statutory liability summary, payment reconciliation and period-to-period variance.

## 11. Acceptance scenarios
1. Run payroll for a small tenant with no statutory applicability configured.
2. Calculate hourly and fixed compensation using effective-dated rules.
3. Prevent unauthorized edits after payroll finalization.
4. Correct attendance with approval and recalculate through controlled workflow.
5. Recover an employee advance partially.
6. Post payroll journal and reconcile payable to employee-level detail.
7. Handle employee exit and final settlement.
8. Restrict payroll visibility independently from general HR access.
9. Update statutory rules without rewriting historical finalized payroll.

## 12. Open decisions
Employee versus contractor boundaries; supported payroll frequencies; attendance integration; statutory scope and source maintenance; payslip format/languages; employee self-service; bank payout integrations; payroll final settlement rules.