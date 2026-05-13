# Migration Status

This document tracks the migration from Monthly Budget Raw into the finalized budgeting system tables.

# Status

Migration is complete.

Monthly Budget Raw is no longer an active source of truth. The finalized system now uses the table and rules canvases listed below.

# Complete

## Core rule and schema documents

- System defines global budgeting-system behavior, precedence, formatting rules, sign convention, and shared data types.
- Monthly Schema defines table purpose, table columns, allowed values, relationships, and semantic validation rules.
- Monthly System defines monthly budgeting behavior, including payday workflows, triggered schedule behavior, income behavior, transient-row cleanup, budget-month attribution, and receipt/adjustment behavior.
- Command Design has been reduced to a non-authoritative scratchpad and must not be used as a source for runtime rules, schema rules, validation rules, workflow rules, or budgeting behavior unless explicitly instructed.

## Schedule

- Schedule Table has been updated to the current schema: Domain | Item | Month | Day | Payday | Notes.
- Schedule is now a timing table only.
- Schedule financial columns have been removed.
- Schedule uses Domain + Item references to finalized domain tables.
- Schedule includes rows for Recurring Bills, Debt, Reserves, Planned Spending, and Income.
- Schedule includes one Regular paycheck Income row with Payday = TRUE.
- Schedule includes reserve funding rows for Food, Fuel, Family discretionary, HSA - company, allowance accounts, HSA - employee, and internal reserves.
- HSA - company is scheduled for 5/11 with Payday = FALSE because it uses date timing rather than payday-event timing.
- Schedule rows no longer use Notes for payday timing, funding source, or migration status.
- Schedule Month and Day values have been normalized as Month/Day values, FALSE, or UNKNOWN. Rows with Payday = TRUE use Month = FALSE and Day = FALSE.
- Schedule has been validated against finalized domain tables using Domain + Item references.

## Income

- Income Table has been created.
- Income uses the schema: Item | Expected | Actual | Transient | Notes.
- Income includes Regular paycheck as a durable row with Transient = FALSE.
- Income has been added as a valid Schedule domain.
- Income rows may be scheduled when expected by date or payday event, but Income rows do not require Schedule rows.

## Debt

- Debt rows have been migrated from Monthly Budget Raw into Debt Table.
- Debt timing has been migrated into Schedule Table.
- Debt financial data now lives in Debt Table.
- Debt rows have been removed from Monthly Budget Raw.
- Debt Table has been updated to remove timing columns.
- Debt Table includes Mom and Church as additional debt rows.
- Debt Table Notes cells are blank when there is no human-reference note.
- Debt rows have been validated against Schedule Table.

## Recurring Bills

- Recurring Bills have been migrated from Monthly Budget Raw and transitional Schedule data into Recurring Bills Table.
- Housing recurring bill rows were migrated from Monthly Budget Raw.
- Utilities recurring bill rows were migrated from Monthly Budget Raw.
- Transportation | Auto insurance was migrated from Monthly Budget Raw.
- Individual subscriptions were migrated from Schedule into Recurring Bills using the individual subscription name as Item.
- Gerber insurance rows were migrated from Schedule into Recurring Bills.
- Migrated recurring bill rows have been removed from Monthly Budget Raw.
- Subscription rows in Schedule were renamed from Lifestyle | Subscriptions to Subscriptions | individual subscription name.
- Migrated financial values in Schedule have been removed.
- Recurring Bills rows have been validated against Schedule Table.

## Reserves

- Reserves Table has been created.
- Reserves uses the schema: Item | Allocation | Actual | Cap | Balance | Paused | Notes.
- Reserves include allowance buckets, food, fuel, vehicle maintenance, personal care, household supplies, medical, gifts, emergency fund, Spain fund, and HSA buckets.
- Reserves migration from Monthly Budget Raw is complete.
- Active Reserves rows have been validated against Schedule Table.
- Spain fund is paused and therefore does not require a Schedule row.

## Planned Spending

- Planned Spending Table has been created.
- Planned Spending replaced the earlier Monthly Spending concept.
- Planned Spending uses the schema: Item | Reserve | Expected | Actual | Recurring Bill | Notes.
- Food, fuel, personal care, household, family discretionary, and adult allowance subscription rows have been migrated into Planned Spending.
- Adult allowance subscriptions are marked as Recurring Bill = TRUE.
- Planned Spending rows have been validated against Reserves Table.
- Scheduled Planned Spending rows have been validated against Schedule Table and have Recurring Bill = TRUE.

## Adjustments

- Adjustments Table has been created.
- Adjustments uses the schema: Item | Actual | Notes.
- Adjustments is ready for receipt-level components such as tax, fees, deposits, discounts, and rounding differences.
- No adjustment rows were required from Monthly Budget Raw.
- Adjustments Table currently contains one approved Sales tax adjustment row from receipt processing.
- No Schedule rows reference Adjustments.

## Setup documents

- Setup Budget Cells, Setup Schema, and Setup System remain available for setup-related budgeting data and rules.
- Setup documents are separate from the finalized monthly budgeting table set.

## Initializer

- Initializer includes the finalized loadable file manifest.
- Initializer includes income\_table.md / Income Table.
- Initializer excludes initializer.md and initializer.bak.md from the loadable manifest.
- Initializer verification still expects system.md / System as the final manifest file and inferred canvas name.

# Current source-of-truth table set

The finalized monthly budgeting system consists of these monthly canvases:

- Adjustments Table
- Debt Table
- Income Table
- Planned Spending Table
- Recurring Bills Table
- Reserves Table
- Schedule Table

The finalized rule/schema canvases are:

- System
- Monthly Schema
- Monthly System

Command Design is non-authoritative scratchpad only.

# Completed validations

- Schedule Table references finalized domain tables using Domain + Item.
- Schedule entries do not mix date timing with payday-event timing.
- Month and Day values are normalized as date values, FALSE, or UNKNOWN. For payday-event rows, Month and Day are FALSE.
- Notes columns may be blank and should not use UNKNOWN.
- Notes columns must not duplicate information already provided by another column.
- Recurring Bills rows have matching Schedule rows.
- Debt rows have matching Schedule rows.
- Active Reserves rows have matching Schedule rows unless paused.
- Planned Spending rows reference valid Reserves rows.
- Scheduled Planned Spending rows have Recurring Bill = TRUE.
- Income rows use the finalized Income schema and may be scheduled when appropriate.
- Adjustments Table exists and contains one valid Sales tax adjustment row.
- Monthly Schema and Monthly System have been separated by role: schema/data integrity vs runtime behavior.
- Command Design has been pruned of migrated and obsolete rules.

# Maintenance notes

- Re-run validation after any schema, system, or table edit.
- Recompute file hashes and update Initializer manifest after exporting changed canvases.

