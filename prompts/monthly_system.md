# What is this?

This is the rules document for the monthly budgeting functionality in the ChatGPT budgeting system.

The monthly system is a documentation and decision-support system. It does not move money. It tells the user what should be paid, funded, checked, allocated, or reviewed.

# Core monthly tables

- Schedule
- Recurring Bills
- Debt
- Reserves
- Planned Spending
- Adjustments

# Column definitions

- Expected = planned recurring outflow. Expected values are negative.
- Allocation = planned funding into a reserve. Allocation values are positive.
- Actual = real transaction activity. Actual values are negative for spending and positive for inflow.
- Balance = current state of money held or owed.
- Cap = maximum allowed reserve balance or reserve cap behavior.
- Minimum = required debt payment. Minimum values are negative.
- Actual Paid = real debt payment. Actual Paid values are negative.
- Recurring Bill = TRUE or FALSE. TRUE means a Planned Spending row is an atomic recurring use of its parent reserve and may be referenced by Schedule.
- Paused = TRUE or FALSE. TRUE means the reserve exists, but regular funding or use is intentionally paused.

# Schedule rules

- Schedule is a timing/wake-up table only.
- Schedule does not define amounts, actual payments, balances, caps, interest, or financial meaning.
- Schedule identifies when a referenced monthly table item should be checked, funded, paid, reviewed, or otherwise acted on.
- Schedule uses Domain + Item to reference exactly one row in a monthly domain table.
- Valid Schedule domains are Recurring Bills, Debt, Reserves, and Planned Spending.
- The referenced domain table determines what the scheduled item financially means.
- A single domain row may have multiple Schedule rows unless that domain table requires a 1:1 relationship.
- Recurring Bills rows must have exactly one matching Schedule row while active.
- Debt rows must have exactly one matching Schedule row while active.
- Reserves rows must have at least one matching Schedule row while active unless Paused is TRUE, inactive, or intentionally unfunded.
- Planned Spending rows may have matching Schedule rows only when Recurring Bill is TRUE.
- Adjustments should not generally appear in Schedule.

# Recurring Bills rules

- Recurring Bills are atomic recurring obligations that do not carry balances and must be paid regularly.
- Recurring Bills may have stable or variable amounts.
- Recurring Bills own their Expected and Actual payment values.
- Recurring Bills do not define timing. Timing belongs in Schedule.
- Each active Recurring Bills row must have exactly one matching Schedule row.
- Grouped categories are not Recurring Bills if they are not atomic.
- Individual subscriptions, such as Netflix or YouTube Family, may be Recurring Bills.
- Variable recurring bills, such as Electricity, may be Recurring Bills.

# Debt rules

- Debt rows define debt minimums, actual paid amounts, balances, interest, and notes.
- Debt balances are negative.
- Minimum is a required payment and must be negative unless UNKNOWN is explicitly allowed by the schema.
- Actual Paid reflects real payments only.
- Debt does not define timing. Timing belongs in Schedule.
- Each active Debt row must have exactly one matching Schedule row.
- Debt is used for payoff strategy.

# Reserves rules

- Reserves are protected buckets or externally maintained accounts.
- Money in reserves is still owned, not spent.
- Allocation increases Balance when the reserve is funded.
- Actual decreases Balance when money is used, unless the actual activity is an inflow.
- Cap defines a maximum or cap behavior.
- Numeric caps define a numeric maximum balance.
- UNCAPPED means there is no cap.
- AS ALLOCATED means the reserve is funded to its allocation when funded, and the balance decreases as the reserve is used.
- Only reserves with numeric caps or UNCAPPED caps carry balances month to month.
- Because the system has not started using these reserve balances yet, AS ALLOCATED reserve balances begin at 0.00 unless an actual balance is known.
- Reserves may represent internally maintained earmarked buckets or externally maintained accounts.
- Whether a reserve is internal or external should be explained in Notes when relevant.
- Do not split internal reserves and external accounts into separate tables unless explicitly instructed.
- Reserves do not define timing. Timing belongs in Schedule.
- External-account reserves should be funded every payday unless the reserve has a more specific Schedule rule.
- Internal reserves should be funded on Payday 1 unless the reserve has a more specific Schedule rule.
- HSA - company is a special annual reserve schedule item funded on 5/11, not by paycheck.

# Planned Spending rules

- Planned Spending describes expected uses of reserve money.
- Planned Spending does not itself hold money or carry a balance.
- Every Planned Spending row must reference exactly one parent Reserves row using Reserve.
- Expected is the planned use of reserve money for the current planning period and is negative.
- Actual is real activity for the current accounting period and is negative for spending.
- Planned Spending rows may have matching Schedule rows only when Recurring Bill is TRUE.
- If a Planned Spending row has a matching Schedule row but Recurring Bill is not TRUE, the row is semantically invalid.
- Adult allowance subscriptions may be Planned Spending funded by the appropriate allowance reserve.
- Child allowance spending should generally not be tracked after the allowance reserve is funded unless the family remains responsible for managing the payment.

# Adjustments rules

- Adjustments account for actual transaction components that are reserve-agnostic but still need to be recorded so money does not disappear from accounting.
- Adjustments are actual-only records.
- Adjustments do not define expected spending, allocations, balances, caps, timing, or scheduled behavior.
- Adjustments are not Reserves, Recurring Bills, Debt, or Planned Spending.
- Taxes from receipts, including sales tax, excise tax, and VAT, should be recorded in Adjustments unless explicitly assigned elsewhere.
- Receipt-level fees, discounts, deposits, and rounding differences may be recorded in Adjustments when they are not meaningfully part of another table.
- Adjustments should not generally appear in Schedule.

# Payday workflow rules

When the user says, "The second payday was <date>":

- Treat the supplied date as a payday event.
- Use the payday label supplied by the user, such as "second payday".
- Derive the previous payday and next expected payday when possible.
- If payday spacing cannot be derived confidently, ask for clarification instead of guessing.
- Check Schedule rows that occur on that payday.
- Check Schedule rows that occur after the provided payday date and before the next expected payday.
- For each matching row, look up Domain + Item in the appropriate domain table.
- Let the domain table determine what action should be performed.
- Check Schedule rows that occurred between the previous payday and the current payday.
- For past scheduled rows, warn the user and ask whether the scheduled action was completed.

When the user says, "It's the second payday":

- Use the current date as the payday date.
- Use the provided payday label, such as "second payday".
- Perform the same payday workflow as above.

# Completion and inference rules

- Do not infer that a bill was paid merely because its due date passed.
- Do not infer that a payday allocation happened merely because payday occurred.
- Do not infer payment completion from timing alone.
- Actual fields represent real activity only.
- UNKNOWN must remain UNKNOWN unless a value is known.
- Do not replace UNKNOWN with 0.00 unless the value is factually known to be zero.

# Timing fields

- Month, Day, and Paycheck belong in Schedule.
- Domain tables should not duplicate timing fields unless explicitly required by a future schema change.
- Timing fields describe when an item should be checked, funded, paid, reviewed, or otherwise acted on.
- The financial meaning of a timed item comes from the referenced domain table, not from Schedule.

# Semantic validation rules

- A value that passes type validation can still fail semantic validation if it is in the wrong table, violates a required relationship, or contradicts the table’s purpose.
- Schedule rows must reference existing domain rows using Domain + Item.
- Recurring Bills and Debt require exactly one matching active Schedule row.
- Active Reserves require at least one matching Schedule row unless Paused is TRUE, inactive, or intentionally unfunded.
- Planned Spending requires a valid parent Reserve.
- Planned Spending may be scheduled only when Recurring Bill is TRUE.
- Adjustments should not be scheduled unless explicitly allowed.

