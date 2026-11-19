# Detailed Problem Statement & Requirements

Model corporate financial statement telemetry. Construct variance analyses comparing actual sales against budget targets across geographic segments, product lines, and manufacturing cost centers.

# Dataset audit- What is wrong?

**ISSUE 1** Missing values.

Discount column contains:
- High - 245
- Low - 160
- Medium - 242
- Blank - 53
- Total = 700
I investigated those 53 records.

**Important finding:** All 53 records have Discounts = 0.

**This means the missing category appears to represent transactions without discounts.**

---
**ISSUE 2** Negative profit.

I found 58 records where profit is negative.

The **formula is: Profit = Sales - COGS**

When COGS exceeds Sales, the business makes a loss on that record.

This is not necessarily bad data.

We don't delete these records. They are particularly important for profitability and root-cause analysis.

We should investigate which products, countries and segments generate these losses (negative profit).

---
**ISSUE 3:** Financial calculation validation

This is one of the most important checks.

I tested three financial relationships across all 700 records.

Gross Sales = Units Sold × Sale Price ->	700 passed

Sales = Gross Sales − Discounts	-> 700 passed

Profit = Sales − COGS	-> 700 passed

All 3 validations passed
This is good news. Our core financial calculations are internally consistent.  

---

# Data Preparation in powerBI before analysing, visualizing and reporting

**What are we doing in Power Query?**
Power Query is where we prepare and clean data before loading it into the Power BI data model.

1. Replaced None-> No Discount in Discount Band column.
   
2. Original column name has an unwanted leading space: ''' Sales'''
   
   Rename it to '''Sales''' (without the space)
   
   Why? Clean column names make DAX formulas easier to write and reduce errors when building your financial measures.

---

# DAX Measures in PowerBI

I created the following DAX measures in Power BI to analyse financial performance, revenue, profitability, discounts, costs, and sales volume.

### 1. Revenue and Financial Performance

| Measure | DAX Formula | Purpose |
|---|---|---|
| **Total Revenue** | `Total Revenue = SUM(financial[Sales])` | Calculates total net revenue after discounts. |
| **Total Gross Sales** | `Total Gross Sales = SUM(financial[Gross Sales])` | Calculates total sales before discounts. |
| **Total Profit** | `Total Profit = SUM(financial[Profit])` | Calculates the total profit generated. |
| **Total Discounts** | `Total Discounts = SUM(financial[Discounts])` | Calculates the total discount amount given to customers. |
| **Total COGS** | `Total COGS = SUM(financial[COGS])` | Calculates the total Cost of Goods Sold. |

### 2. Profitability and Cost Analysis

| Measure | DAX Formula | Purpose |
|---|---|---|
| **Profit Margin %** | `Profit Margin % = DIVIDE([Total Profit], [Total Revenue], 0)` | Revenue tells us how much the company earns. Profit Margin tells us how much profit it keeps from each unit of revenue. |
| **Discount Rate %** | `Discount Rate % = DIVIDE([Total Discounts], [Total Gross Sales], 0)` | This tells us what percentage of gross sales was given away as discounts. |
| **COGS Ratio %** | `COGS Ratio % = DIVIDE([Total COGS], [Total Revenue], 0)` | This tells us how much of the company's net revenue is spent on the cost of goods sold. |

### 3. Sales Volume and Average Performance

| Measure | DAX Formula | WHY? |
|---|---|---|
| **Total Units Sold** | `Total Units Sold = SUM(financial[Units Sold])` | This measure helps us understand product sales volume and compare demand across products, countries, and segments. |
| **Average Selling Price** | `Average Selling Price = AVERAGE(financial[Sale Price])` | This measure helps us compare the average selling price across different products, countries, and customer segments. |
| **Average Discount** | `Average Discount = AVERAGE(financial[Discounts])` | This helps us understand the average discount given per sales record. |
| **Average Profit** | `Average Profit = AVERAGE(financial[Profit])` | This shows the average profit per record and helps identify whether individual sales records are profitable. |
| **Total Transactions** | `Total Transactions = COUNTROWS(financial)` | This measure counts the number of sales records and will help us calculate average revenue per record. |

### Business Value

These measures support business analysis by helping answer questions such as:

- How much revenue did the company generate?
- How much profit did the company make?
- What percentage of revenue was retained as profit?
- How much did discounts reduce gross sales?
- How much did the company spend on production costs?
- Which products and countries generated the highest revenue?
- Which segments performed best?
- How many units were sold?
- What was the average profit per sales record?

**Technical note:** All measures are dynamic. They respond to report filters and slicers, allowing financial performance to be analysed by product, country, segment, discount band, and time period.

# KPI:
Business story: Corporate financial performance

1. What happened?

The company generated $127.93M in gross sales and provided $9.20M in discounts, resulting in $118.73M in net revenue.

2. What does it mean?

Discounts represent approximately 7.19% of gross sales. The company retained approximately 92.81% of gross sales as net revenue.

3. Where did the money go?

The company recorded $101.83M in COGS, leaving approximately $16.89M in profit.

4. How profitable is the company?

Its profit margin is approximately 14.23%. For every $100 in net revenue, approximately $14.23 remains as profit.

5. What should management investigate? ----- Data Analyst's Task
- Which products have the highest profit margins?
- Which countries generate the most profit?
- Are discounts reducing profitability?
- Which customer segments have the highest COGS ratios?
- Are some products generating revenue but losing money?

Dimensions: Country, Product, Segment, Discount Band, Year, Month


















