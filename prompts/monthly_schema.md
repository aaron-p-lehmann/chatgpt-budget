# What is this?

This is a description of the schema of a budgeting system that runs in ChatGPT.

# SCHEDULE

\| Domain | Item | Month | Day | Paycheck | Notes |

Schedule is a timing table only.
It does not define amounts or actual payments.

During migration, Schedule may temporarily contain financial columns copied from older source data. Those values may be used to populate the correct domain tables. Once migrated, the financial data should be removed or marked as migrated before the final schema is enforced.

The types of the columns are:

- Domain - the monthly domain table that the scheduled row references. Allowed values are Recurring Bills, Debt, Reserves, and Planned Spending.
- Item - the row identifier in the referenced domain table. Together with Domain, Item MUST correspond to exactly one row in the referenced monthly domain table.
- Month - a Month value.
- Day - a Day value.
- Paycheck - a Paycheck value.
- Notes - a Freeform value. Notes are purely for human consumption, may be blank, should not use UNKNOWN, and MUST NOT include information that is already provided by another column.

Tables used to validate this section:

- Schedule Table
- Recurring Bills Table
- Planned Spending Table
- Reserves Table
- Debt Table

# RECURRING BILLS

\| Item | Expected | Actual | Notes |

Recurring Bills are atomic recurring obligations that do not carry balances and must be paid regularly.
They may have stable or variable amounts.

The types of the columns are:

- Item - the row identifier. Item MUST be unique within Recurring Bills.
- Expected - a negative Money value, or UNKNOWN if the expected amount is not known.
- Actual - a non-positive Money value, or UNKNOWN if the actual payment amount is not known.
- Notes - a Freeform value. Notes are purely for human consumption, may be blank, should not use UNKNOWN, and MUST NOT include information that is already provided by another column.

Notes:

- Recurring Bills intentionally has no Month, Day, or Paycheck fields.
- Timing for Recurring Bills belongs in Schedule.
- Each active Recurring Bills row MUST have exactly one matching Schedule row.
- Grouped categories are not Recurring Bills if they are not atomic.
- Example: Subscriptions is not a Recurring Bill because it groups multiple bills.
- Individual subscriptions, such as Netflix or YouTube Family, may be Recurring Bills.
- Subscription rows should use the individual subscription name as Item.
- Variable recurring bills, such as Electricity, may be Recurring Bills.
- The raw table currently shows positive values in Expected for many recurring bill rows. Under the sign convention, these should become negative once normalized.
- During migration, Schedule may contain transitional financial data. That data may be used to populate Recurring Bills. After migration, Schedule MUST lose financial columns.

Tables used to validate this section:

- Recurring Bills Table
- Schedule Table

# PLANNED SPENDING

\| Item | Reserve | Expected | Actual | Recurring Bill | Notes |

The types of the columns are:

- Item - the row identifier. Item MUST be unique within Planned Spending.
- Reserve - the Item value of the Reserves row that funds this Planned Spending row. Reserve MUST correspond to exactly one row in Reserves.
- Expected - a non-positive Money value, or UNKNOWN if the expected amount is not known.
- Actual - a negative Money value, or UNKNOWN if the actual spending amount is not known.
- Recurring Bill - TRUE or FALSE. TRUE means this Planned Spending row is an atomic recurring use of its parent reserve and MAY have a matching Schedule row.
- Notes - a Freeform value. Notes are purely for human consumption, may be blank, should not use UNKNOWN, and MUST NOT include information that is already provided by another column.

Notes:

- Planned Spending intentionally has no Month, Day, or Paycheck fields.
- Planned Spending describes expected use patterns or detailed spending categories for a referenced reserve bucket.
- Planned Spending does not itself hold money or carry a balance.
- Every Planned Spending row MUST reference a parent Reserves row using Reserve.
- A Planned Spending row MAY have a matching Schedule row only when Recurring Bill is TRUE.
- If a Planned Spending row has a matching Schedule row but Recurring Bill is not TRUE, the assistant MUST flag it as semantically invalid.
- The raw table currently shows positive values in Expected for many planned spending rows. Under the sign convention, these should become negative once normalized.

Tables used to validate this section:

- Planned Spending Table
- Reserves Table
- Schedule Table

# RESERVES

\| Item | Allocation | Actual | Cap | Balance | Paused | Notes |

The types of the columns are:

- Item - the row identifier. Item MUST be unique within Reserves.
- Allocation - a non-negative Money value, or UNKNOWN if the planned funding amount is not known.
- Actual - a Money value.
- Cap - a Cap value.
- Balance - a Money value, or UNKNOWN if the current balance is not known.
- Paused - TRUE or FALSE. TRUE means the reserve exists but regular funding or use is intentionally paused.
- Notes - a Freeform value. Notes are purely for human consumption, may be blank, should not use UNKNOWN, and MUST NOT include information that is already provided by another column.

Notes:

- Money in reserves is still owned, not spent.
- Allocation increases Balance.
- Actual decreases Balance when money is used.
- Cap may be a Money value, AS ALLOCATED, or UNCAPPED.
- Only reserves with numeric caps or UNCAPPED caps carry balances month to month.
- Reserves with Cap = AS ALLOCATED are funded to their Balance when funded, and Balance decreases as they are used.
- Because the system has not started using these reserve balances yet, AS ALLOCATED reserve balances begin at 0.00 unless an actual balance is known.
- Reserves may represent internally maintained earmarked buckets or externally maintained accounts.
- Whether a reserve is internal or external should be explained in Notes when relevant.
- Do not split internal reserves and external accounts into separate tables unless explicitly instructed.
- The raw table currently uses Expected for reserve funding. Under the schema, those values should become Allocation once normalized.

Tables used to validate this section:

- Reserves Table
- Schedule Table

# ADJUSTMENTS

\| Item | Actual | Notes |

Adjustments account for actual transaction components that are reserve-agnostic but still need to be recorded so money does not disappear from the accounting.

The types of the columns are:

- Item - the adjustment type, such as Sales tax, Excise tax, VAT, Bag fee, Bottle deposit, Rounding difference, or Receipt discount. Item MUST be unique within Adjustments unless multiple rows intentionally represent separate occurrences.
- Actual - a Money value.
- Notes - a Freeform value. Notes are purely for human consumption, may be blank, should not use UNKNOWN, and MUST NOT include information that is already provided by another column.

Notes:

- Adjustments are actual-only records.
- Adjustments do not define expected spending, allocations, balances, caps, timing, or scheduled behavior.
- Adjustments are not Reserves, Recurring Bills, Debt, or Planned Spending.
- Taxes from receipts, including sales tax, excise tax, and VAT, should be recorded here unless explicitly assigned elsewhere.
- Receipt-level fees, discounts, deposits, and rounding differences may be recorded here when they are not meaningfully part of another table.
- Adjustments should not generally appear in Schedule.

Tables used to validate this section:

- Adjustments Table
- Receipt imports or parsed receipts, when available

# DEBT

\| Item | Minimum | Actual Paid | Balance | Interest | Notes |

The types of the columns are:

- Item - the row identifier. Item MUST be unique within Debt.
- Minimum - a negative Money value, or UNKNOWN if the minimum required payment is not known.
- Actual Paid - a negative Money value, or UNKNOWN if the actual amount paid is not known.
- Balance - a Money value. Debt balances should be negative or 0.00.
- Interest - an APR value.
- Notes - a Freeform value. Notes are purely for human consumption, may be blank, should not use UNKNOWN, and MUST NOT include information that is already provided by another column.

Notes:

- Debt balances are negative.
- Minimum is a required payment.
- Actual Paid reflects real payments only.
- Each active Debt row MUST have exactly one matching Schedule row.
- The raw table currently shows positive values in Expected for debt payments. Under the debt schema, those values should become negative Minimum values once normalized.

Tables used to validate this section:

- Debt Table
- Schedule Table

# SEMANTIC VALIDATION RULES

- Schedule is a timing table only and MUST NOT contain amount, actual payment, balance, cap, or interest columns.
- Every Schedule row MUST reference exactly one row in exactly one monthly domain table using Domain + Item.
- Domain + Item does not need to be unique inside Schedule unless the referenced domain table requires a 1:1 relationship.
- Schedule entries MUST NOT have both payday timing and date timing. A Schedule row may use Month/Day timing, or Paycheck timing, or neither if timing is unknown, but it MUST NOT use both.
- It is permissible for Month, Day, and Paycheck to all be UNKNOWN when the timing is not known.
- A Schedule row MAY reference a Planned Spending row only when the Planned Spending row's Recurring Bill value is TRUE.
- A Schedule row MUST NOT reference an Adjustments row unless explicitly allowed.
- Each active Recurring Bills row MUST have exactly one matching Schedule row.
- Each active Debt row MUST have exactly one matching Schedule row.
- Each active Reserves row MUST have at least one matching Schedule row unless Paused is TRUE or the row is explicitly marked inactive or intentionally unfunded.
- Planned Spending rows MAY have matching Schedule rows only when Recurring Bill is TRUE.
- Adjustments rows MUST NOT have matching Schedule rows unless explicitly allowed.
- Recurring Bills rows MUST be atomic. Grouped categories such as Subscriptions MUST be split into their individual bills before becoming Recurring Bills.
- Every Planned Spending row MUST reference exactly one Reserves row using Reserve.
- If a Planned Spending row has a matching Schedule row but Recurring Bill is not TRUE, the row is semantically invalid.
- Notes columns in any table are purely for human consumption, may be blank, should not use UNKNOWN, and MUST never include information that is provided by another column.
- A value that passes type validation can still fail semantic validation if it is in the wrong table, violates a required relationship, or contradicts the table’s purpose.

# Glossary

- Item: The row identifier within a table. Item names MUST be unique within each domain table.
- Expected: Planned recurring outflow for the month (negative value).
- Actual: Real transaction amount (negative for spending, positive for inflow).
- Reserve: The Item value of the Reserves row that funds a Planned Spending row.
- Month: The month this expense is due, or UNKNOWN if the month is not known or the row uses Paycheck timing instead.
- Day: The day of the month this expense is due, or UNKNOWN if the day is not known or the row uses Paycheck timing instead.
- Paycheck: Identifies which paycheck cycle this item is associated with for interaction or funding, or UNKNOWN if the paycheck is not known or the row uses Month/Day timing instead.
- Notes: Human-readable reference information only. Notes may be blank. A blank Notes cell means there is no human-reference note for that row. Notes should not use UNKNOWN. Notes MUST never duplicate or restate information that is provided by another column.
- Allocation: Planned funding into a bucket (positive value, does not represent spending).
- Cap: The maximum amount of money that can be earmarked for that item. "UNCAPPED" means there is no cap.
- Balance: Money currently held in the bucket (positive) or owed (negative for debt).
- Paused: A TRUE/FALSE value indicating whether regular reserve funding or use is intentionally paused.
- Schedule: A timing entry that tells the system when a referenced monthly table item should be checked, funded, paid, reviewed, or otherwise acted on. It does not define amounts or actual payments.
- Recurring Bill: An atomic recurring obligation that does not carry a balance and must be paid regularly. Recurring Bills may have stable or variable amounts.
- Adjustment: An actual transaction component that is reserve-agnostic but must be recorded so accounting remains complete, such as tax, fees, deposits, discounts, or rounding differences.

