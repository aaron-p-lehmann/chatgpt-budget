# What is this?
This is a description of the schema for the setup table of a budgetting system that runs in ChatGPT.  This is in a
separate file because the table and its rules will only be used temporarily.

# SETUP
| Section | Item | Low Estimate | High Estimate | Actual | Type |

# Glossary
* Section: A category of spending, such as "Housing", "Food", "Transportation", etc.
* Item: A specific expense within a section, such as "Rent", "Groceries", "Gas", etc.
* Low Estimate: Lower bound of expected transaction amount (negative for spending, positive for inflow).
* High Estimate: Upper bound of expected transaction amount (negative for spending, positive for inflow).
* Actual: Real transaction amount (negative for spending, positive for inflow).
* Type: The type of transaction -
** Complete       = finished, no further action
** Immediate      = must be completed before next paycheck (required outflow)
** Next Paycheck  = deferred until next paycheck before execution
** One-Time Goal  = planned but not urgent
** Planned        = expected inflow (positive, not guaranteed)
** Unexpected     = Unexpected = unplanned expense already incurred (Actual should be populated and estimates should both be 0)

# CLARIFICATION REGARDING ESTIMATES AND ACTUALS
Actual replaces estimates once the transaction occurs.
Estimates are used only for planning and are not commitments.
Estimates should not be summed as actual spending.
Ranges are used for scenario analysis, not accounting.
If Actual is populated, estimates should no longer be used for decision-making.

