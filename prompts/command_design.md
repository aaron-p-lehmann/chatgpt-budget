# Command design

This document captures command/workflow requirements for the budgeting system, especially payday-driven behavior and Schedule behavior.

# Core model

- Schedule is a timing/wake-up table.
- Schedule does not define financial meaning, balances, caps, actual payments, or allocation amounts.
- Domain tables define what the scheduled item means financially.
- A Schedule row tells the system when to look up and execute/check the referenced item.
- The referenced domain table determines the correct action.

# Schedule lookup rule

- Each Schedule row MUST reference exactly one row in a monthly domain table using Domain + Item.
- Domain + Item in Schedule is a foreign-key-style reference, not necessarily a unique primary key inside Schedule.
- A single domain row MAY have multiple Schedule rows unless that domain table requires a 1:1 relationship.
- Recurring Bills rows MUST have exactly one matching Schedule row while active.
- Debt rows MUST have exactly one matching Schedule row while active.
- Reserves rows MUST have at least one matching Schedule row while active, unless explicitly marked paused, inactive, or intentionally unfunded.
- Planned Spending rows MAY have matching Schedule rows only when the Planned Spending row's Notes include Recurring Bill.
- Adjustments rows SHOULD NOT have matching Schedule rows unless explicitly allowed.

# Domain table behavior

- Recurring Bills define atomic recurring obligations that do not carry balances and must be paid regularly.
- Debt defines debt minimums, actual paid amounts, balances, and interest.
- Reserves defines allocation amounts, actual activity, caps, balances, and reserve notes.
- Planned Spending defines expected and actual usage patterns for money from a parent Reserves row.
- Adjustments define reserve-agnostic actual transaction components such as tax, fees, deposits, discounts, or rounding differences.

# Reserves and Planned Spending relationship

- Reserves represents earmarked buckets where money is protected and carries forward.
- Planned Spending represents expected use patterns or detailed spending categories for a referenced reserve bucket.
- Every Planned Spending row MUST reference a parent Reserves row using Reserve.
- Planned Spending does not itself hold money or carry a balance.
- Example: Food subcategories may be Planned Spending details under a broader Food reserve.
- Not every Capped row in Raw should automatically become a Reserves row.
- Recurring Bills such as rent should not be modeled as flexible reserve usage merely because they are recurring.
- Before finalizing Reserves, define which broad buckets are sacred carry-forward buckets and which rows are Planned Spending detail.
- Adult allowance subscriptions may be modeled as Planned Spending funded by an allowance reserve when they are recurring spending from a separate allowance account.
- Child allowance spending should generally not be tracked after the allowance reserve is funded unless the family remains responsible for managing the payment.

# Recurring Bills definition

- Recurring Bills are atomic recurring obligations that do not carry balances in this budgeting system.
- Recurring Bills must be paid regularly.
- Recurring Bills may have stable or variable amounts.
- A bill can be a Recurring Bill even if the amount varies, such as Electricity.
- Grouped categories are not Recurring Bills if they are not atomic.
- Example: `Lifestyle | Subscriptions` is not a Recurring Bill because it groups multiple bills.
- Individual subscriptions, such as Netflix or YouTube Family, may be Recurring Bills.
- Recurring Bills own expected/actual payment amounts.
- Timing for Recurring Bills belongs in Schedule.

# Allowance account behavior

- Allowances are separately held earmarked accounts.
- Allowance funding should be modeled as Reserves behavior.
- Allowance rows should be treated as reserve-like buckets because money is held separately and remains earmarked for the individual.
- Adult allowance spending MAY be tracked when it is useful for shared cash-flow visibility.
- Child allowance spending should generally not be tracked after the allowance reserve is funded unless the family remains responsible for managing the payment.
- Adult allowance subscriptions may be modeled as Planned Spending funded by the appropriate allowance reserve.
- Child allowance subscriptions should generally not be modeled after allowance funding unless the family remains responsible for managing the payment.

# Planned Spending scheduling exception

- Planned Spending rows MAY have matching Schedule rows only when Notes includes Recurring Bill.
- Scheduled Planned Spending must still reference a parent Reserves row using Reserve.
- The Recurring Bill note indicates that the Planned Spending row is an atomic recurring use of a reserve bucket.
- Example: `ChatGPT` may be Planned Spending with Notes = Recurring Bill and Reserve = `Joseph`.
- Example: `Food | Fruits` should not have a Schedule row unless explicitly approved and marked appropriately.
- If a Planned Spending row has a Schedule row but Notes does not include Recurring Bill, the system should flag it as semantically invalid.

# Adjustments

- Adjustments account for reserve-agnostic actual transaction components that must be recorded so money does not disappear from accounting.
- Examples include sales tax, excise tax, VAT, receipt-level fees, bottle deposits, discounts, and rounding differences.
- Adjustments are actual-only.
- Adjustments should not be treated as Reserves, Recurring Bills, Debt, or Planned Spending.
- Adjustments should not generally appear in Schedule.

# Amount timing semantics

- Allocation, Expected, and Minimum values in domain tables apply per triggered Schedule row unless the table explicitly defines otherwise.
- Actual fields represent real activity for the current accounting period/month.
- Actual fields are not multiplied by the number of Schedule rows.
- Balance fields represent current state.
- Balance fields are not tied to a month, payday, or Schedule trigger.
- Cap fields define limits or cap behavior for Balance.
- Cap behavior is built on top of Balance and should not be treated as a transaction amount.

# Payday command behavior

When the user says, "The second payday was <date>":

- The system treats the supplied date as a payday event.
- The system records or uses the payday label supplied by the user, such as "second payday".
- The system derives the previous payday and next expected payday when possible.
- If payday spacing cannot be derived confidently, the system asks for clarification instead of guessing.
- The system checks Schedule rows that occur on that payday.
- The system checks Schedule rows that occur after the provided payday date and before the next expected payday.
- For each matching row, the system looks up Domain + Item in the appropriate domain table.
- The domain table determines what action should be performed.
- The system also checks Schedule rows that occurred between the previous payday and the current payday.
- For past scheduled rows, the system warns the user and asks whether the scheduled action was completed.

When the user says, "It's the second payday":

- The system uses the current date as the payday date.
- The system uses the provided payday label, such as "second payday".
- The system performs the same payday workflow as above.

# Due-date/payday decision behavior

- Recurring Bills and debts may have due-date-based timing via Schedule.
- Reserves and savings may have payday-based timing via Schedule.
- On payday, the system should identify bills, debts, reserves, savings, and other scheduled items that need attention before the next expected payday.
- For due-date obligations, if the due date occurs before the next expected payday, the system should tell the user that the item should be handled now.
- If a scheduled due date occurred between the previous payday and current payday, the system should warn the user and ask whether it was handled.
- Do not infer that a scheduled item was completed merely because its date passed.
- Do not infer that a payday allocation happened merely because payday occurred.

# Examples

Example: HSA - employee funded each paycheck

- Reserves row defines the allocation amount per trigger.
- Schedule may contain one row for Payday 1 and another row for Payday 2.
- The two schedule rows both reference the same Reserves + HSA - employee domain row.
- The allocation amount is applied once for each triggered schedule row.
- Actual remains actual activity for the current accounting period and is not multiplied automatically.
- Balance remains current state and is not multiplied.

Example: HSA - company funded annually

- Reserves row defines the annual allocation amount and cap.
- Schedule contains one annual row for HSA - company on 5/11.
- Paycheck is UNKNOWN for this row because the paycheck column is not meaningful for the annual company contribution.

Example: Rent paid once per month

- Recurring Bills row defines the expected rent amount.
- Schedule contains the due-date row for rent.
- When payday occurs, the system checks whether rent is due before the next expected payday.
- If there is no later payday before rent is due, the system tells the user rent should be paid now.

# Migration notes

- Reserves migration is more complex than a direct extraction from Raw.
- Recurring Bills should be migrated before Reserves because their financial meaning is clearer.
- Recurring Bills are expected to be simpler because they are atomic recurring obligations with expected/actual payment amounts, while Reserves may require broader bucket design and Planned Spending relationships.

# Open design questions

- Whether to keep Schedule as a permanent timing table after migration is complete.
- How payday spacing should be recorded or derived over time.
- How to represent paused or intentionally unfunded reserves, such as Spain fund, using the Reserves Paused field and Notes.

