# Migration Status

This document tracks the migration from Monthly Budget Raw into the finalized monthly budgeting tables.

# Complete

## Debt

- Debt rows have been migrated from Monthly Budget Raw into Debt Table.
- Debt timing has been migrated into Schedule Table.
- Debt financial data now lives in Debt Table.
- Debt rows have been removed from Monthly Budget Raw.
- Debt Table has been updated to remove timing columns.

## Recurring Bills

- Recurring Bills have been migrated from Monthly Budget Raw and transitional Schedule data into Recurring Bills Table.
- Housing recurring bill rows were migrated from Monthly Budget Raw.
- Utilities recurring bill rows were migrated from Monthly Budget Raw.
- Transportation | Auto insurance was migrated from Monthly Budget Raw.
- Individual subscriptions were migrated from Schedule into Recurring Bills using the individual subscription name as Item.
- Gerber insurance rows were migrated from Schedule into Recurring Bills.
- Migrated recurring bill rows have been removed from Monthly Budget Raw.
- Subscription rows in Schedule were renamed from Lifestyle | Subscriptions to Subscriptions | individual subscription name.
- Migrated financial values in Schedule were cleared or marked as migrated where appropriate.

# In Progress

## Schedule cleanup

- Schedule has been updated to the current schema: Domain | Item | Month | Day | Paycheck | Notes.
- Schedule financial columns have been removed.
- Schedule now uses Domain + Item references.
- Schedule reserve rows have been updated for the current Reserves design.
- Schedule includes reserve funding rows for Food, Fuel, Family discretionary, HSA - company, allowance accounts, HSA - employee, and internal reserves.
- HSA - company is scheduled for 5/11 with Paycheck = UNKNOWN because the paycheck column is not meaningful for that annual contribution.
- External-account reserves are scheduled for every payday, except HSA - company.
- Internal reserves are scheduled for Payday 1.
- Schedule still needs final validation against finalized domain tables using Domain + Item references.

# Complete / Created

## Reserves

- Reserves Table has been created.
- Reserves now uses the schema: Item | Allocation | Actual | Cap | Balance | Paused | Notes.
- Reserves include allowance buckets, food, fuel, vehicle maintenance, personal care, household supplies, medical, gifts, emergency fund, Spain fund, and HSA buckets.
- Reserves migration from Monthly Budget Raw is complete enough that Raw is no longer an active source of truth.

## Planned Spending

- Planned Spending Table has been created.
- Planned Spending replaced the earlier Monthly Spending concept.
- Planned Spending uses the schema: Item | Reserve | Expected | Actual | Recurring Bill | Notes.
- Food, fuel, personal care, household, family discretionary, and adult allowance subscription rows have been migrated into Planned Spending.
- Adult allowance subscriptions are marked as Recurring Bill = TRUE.

# Not Started / Unresolved

## Adjustments

- Adjustments schema exists, but no adjustment data has been migrated yet.
- Future receipt parsing may populate tax, fees, deposits, discounts, and rounding differences here.

# Raw cleanup already done

- Removed Debt rows from Monthly Budget Raw.
- Removed timing columns from Monthly Budget Raw.
- Removed Interest column from Monthly Budget Raw.
- Removed migrated Recurring Bills rows from Monthly Budget Raw.
- Moved allowance rows from Lifestyle to Allowances in Monthly Budget Raw.
- Migrated remaining Raw rows into Reserves and Planned Spending.

# Monthly Budget Raw status

- Monthly Budget Raw migration is complete.
- All Raw rows have been migrated, transformed, or intentionally excluded.
- Monthly Budget Raw is no longer an active source of truth.
- Keep Monthly Budget Raw only as a historical migration reference until deleted.

# Next likely steps

- Validate Schedule Table against finalized domain tables using Domain + Item.
- Validate Reserves Table against Monthly Schema.
- Validate Planned Spending Table against Monthly Schema.
- Audit Monthly Schema and Command Design for stale wording from earlier names, such as Monthly Spending, Scheduled Transactions, Transaction Adjustments, or Reserves / Savings.
- Decide whether Monthly Budget Raw should be deleted or kept as a historical reference.
- Continue validating Schedule, Reserves, Planned Spending, Recurring Bills, Debt, and Adjustments as a full table set.

