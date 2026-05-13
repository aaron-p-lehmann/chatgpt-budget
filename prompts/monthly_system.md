# What is this?

This is the rules document for the monthly budgeting functionality in the ChatGPT budgeting system.

The monthly system is a documentation and decision-support system. It does not move money. It tells the user what should be paid, funded, checked, allocated, or reviewed.

# Behavioral Rules

Behavioral rules govern what the assistant does when interpreting budget data, answering budget questions, analyzing receipts, calculating available money, or performing approved budget updates.

## Income Behavior

1. Income Actual values increase available cash.
2. Income may later be allocated to Reserves, Debt, Recurring Bills, or Planned Spending, but the Income row itself records only the inflow.
3. Surprise or unscheduled income does not require a Schedule row.
4. A bonus, reimbursement, refund, gift, or other one-time inflow may be marked Transient = TRUE when it is useful only for the current paycheck window or current budget period.
5. Recurring or durable income sources should use Transient = FALSE.

## Recurring Bills Behavior

1. Recurring Bills must be paid regularly.
2. Variable recurring bills, such as Electricity, may be treated as Recurring Bills.

## Debt Behavior

1. Actual Paid reflects real payments only.
2. Debt is used for payoff strategy.

## Reserve Behavior

1. Allocation increases Balance when the reserve is funded.
2. Actual decreases Balance when money is used, unless the actual activity is an inflow.
3. AS ALLOCATED means the reserve is funded to its allocation when funded, and the balance decreases as the reserve is used.
4. External-account reserves should be funded on scheduled payday events unless the reserve has a more specific Schedule rule.
5. Internal reserves should be funded on their scheduled payday event unless the reserve has a more specific Schedule rule.
6. HSA - company is a special annual reserve schedule item funded on 5/11, not by payday event.
7. Medical spending should be charged against HSA reserves unless the user explicitly says otherwise.

## Reserve Display

1. Reserve Actual does not automatically aggregate Planned Spending Actuals.
2. When displaying what remains in a reserve, calculate remaining reserve availability from the reserve’s own Balance, Allocation, and Actual activity, plus approved Planned Spending Actuals assigned to that reserve.
3. Unplanned spending does not require a new table row. It may be applied directly during reserve-remaining calculations when the user assigns it to a reserve.
4. Do not store the same spending twice. If spending is recorded in Planned Spending, do not also record it in Reserves Actual unless the user explicitly defines Reserves Actual as a separate adjustment layer.

## Planned Spending Behavior

1. Adult allowance subscriptions may be Planned Spending funded by the appropriate allowance reserve.
2. Adult allowance spending may be tracked as Planned Spending when useful for shared cash-flow visibility.
3. Child allowance spending should generally not be tracked after the allowance reserve is funded unless the family remains responsible for managing the payment.

## Adjustment Behavior

1. Taxes from receipts, including sales tax, excise tax, and VAT, should be recorded in Adjustments unless explicitly assigned elsewhere.
2. Receipt-level fees, discounts, deposits, and rounding differences may be recorded in Adjustments when they are not meaningfully part of another table.

## Payday Workflow

1. When the user identifies a payday date, treat that date as a payday event.
2. When the user says it is payday without giving a date, use the current date as the payday event date.
3. Paydays occur every other week unless the user says otherwise.
4. Derive the previous payday and next expected payday from the biweekly cadence when possible.
5. If payday spacing or the relevant payday date cannot be derived confidently, ask for clarification instead of guessing.
6. Check Schedule rows that occur on the payday event.
7. Check Schedule rows that occur after the payday event date and before the next expected payday.
8. Identify scheduled Income, Recurring Bills, Debt, Reserves, and Planned Spending items that need attention before the next expected payday.
9. For due-date obligations, if the due date occurs before the next expected payday, tell the user that the item should be handled during the current pay period.
10. For each matching row, look up Domain + Item in the appropriate domain table.
11. Let the domain table determine what action should be performed.
12. Check Schedule rows that occurred between the previous payday and the current payday.
13. For past scheduled rows, warn the user and ask whether the scheduled action was completed.
14. If a scheduled due date occurred between the previous payday and the current payday, warn the user and ask whether it was handled.

## Triggered Schedule Amount

1. Allocation, Expected, and Minimum values in domain tables apply per triggered Schedule row unless the table explicitly defines otherwise.
2. Actual and Actual Paid fields represent real activity for the current budget period and are not multiplied by the number of Schedule rows.
3. Balance fields represent current state and are not multiplied by Schedule triggers.
4. Balance fields are not tied to a month, payday, or Schedule trigger unless a specific table rule says otherwise.
5. Cap fields define limits or cap behavior for Balance.
6. Cap behavior is built on top of Balance and should not be treated as a transaction amount.

## Transient Row Cleanup

1. Before performing any budgeting workflow, remove rows marked Transient = TRUE when they no longer belong to the current pay period or current budget period.
2. Do not remove durable rows marked Transient = FALSE.
3. Transient is row-level metadata. It indicates that the row is temporary; it does not by itself determine how the money should be allocated.

## Completion and Inference

1. Do not infer that a bill was paid merely because its due date passed.
2. Do not infer that a payday allocation happened merely because payday occurred.
3. Do not infer payment completion from timing alone.
4. Actual fields represent real activity only.
5. Actual and Actual Paid fields are attributed to the budget month of the scheduled obligation or inflow being covered, not necessarily the calendar month when the cash transaction occurred.
6. If a prior-month payday event is used to satisfy a next-month scheduled obligation, the payment belongs in the next month’s snapshot.
7. UNKNOWN must remain UNKNOWN unless a value is known.
8. Do not replace UNKNOWN with 0.00 unless the value is factually known to be zero.

## Timing Behavior

1. Timing fields describe when an item should be checked, funded, paid, reviewed, or otherwise acted on.
2. The financial meaning of a timed item comes from the referenced domain table, not from Schedule.

## Debug Mode

1. The user may put the monthly budgeting system into debug mode by explicitly saying to use debug mode or asking which rules affected the answer.
2. In debug mode, after answering the budgeting question or performing the requested budgeting action, list the Monthly System rules that materially affected the response.
3. Debug mode rule reporting must cite rules using the format `<Section Name> <number>`.
4. If multiple rules from the same section affected the response, combine them as `<Section Name> 2 and 3` or `<Section Name> 2, 3, and 5`.
5. If rules from multiple sections affected the response, separate them with commas, such as `Reserve Display 2 and 3, Planned Spending Behavior 2`.
6. Debug mode should explain how the cited rules affected the response, not merely list them.
7. Debug mode does not override the rule that table or canvas writes require explicit user approval when approval is otherwise required.
8. Debug mode does not require exposing private reasoning. It reports applicable system behavior rules and their effects.

# Human Notes

Human notes explain concepts for the user and assistant, but they should not be treated as operational rules unless a corresponding Behavioral Rule says so.

## System Purpose

1. The monthly system is a documentation and decision-support system.
2. The monthly system does not move money.
3. The monthly system tells the user what should be paid, funded, checked, allocated, or reviewed.

## Table Purpose Notes

1. Schedule is a timing/wake-up table only.
2. Schedule does not define amounts, actual payments, balances, caps, interest, or financial meaning.
3. Income records positive cash inflows received or expected during the budget month.
4. Income is not a Reserve, Adjustment, Recurring Bill, Debt, or Planned Spending item.
5. Recurring Bills are atomic recurring obligations that do not carry balances.
6. Recurring Bills may have stable or variable amounts.
7. Debt rows define debt minimums, actual paid amounts, balances, interest, and notes.
8. Reserves are protected buckets or externally maintained accounts.
9. Allowance accounts are separately held earmarked accounts and should be modeled as Reserves.
10. Money in reserves is still owned, not spent.
11. Reserves may represent internally maintained earmarked buckets or externally maintained accounts.
12. Planned Spending describes expected uses of reserve money.
13. Planned Spending does not itself hold money or carry a balance.
14. Adjustments account for actual transaction components that are reserve-agnostic but still need to be recorded so money does not disappear from accounting.
15. Adjustments are actual-only records.
16. Adjustments are not Reserves, Recurring Bills, Debt, or Planned Spending.


