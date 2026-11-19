# Detailed Problem Statement & Requirements

Model corporate financial statement telemetry. Construct variance analyses comparing actual sales against budget targets across geographic segments, product lines, and manufacturing cost centers.

## Dataset audit- What is wrong?

**ISSUE 1** Missing values.

Discount column contains:
High - 245
Low - 160
Medium - 242
Blank - 53
Total = 700
I investigated those 53 records.

**Important finding:** All 53 records have Discounts = 0.

**This means the missing category appears to represent transactions without discounts.**

**ISSUE 2** Negative profit.

I found 58 records where profit is negative.
The **formula is: Profit = Sales - COGS**

When COGS exceeds Sales, the business makes a loss on that record.
This is not necessarily bad data.
We don't delete these records. They are particularly important for profitability and root-cause analysis.
We should investigate which products, countries and segments generate these losses (negative profit).


**Issue 3:** Financial calculation validation
This is one of the most important checks.

I tested three financial relationships across all 700 records.
Gross Sales = Units Sold × Sale Price ->	700 passed
Sales = Gross Sales − Discounts	-> 700 passed
Profit = Sales − COGS	-> 700 passed

All 3 validations passed
This is good news. Our core financial calculations are internally consistent.  
