<h1 align = "Center">
Lotus Group Customer Behavior Analysis

<h3 align = "Center">
Data Inventory Documentation
</h3>

</h1>

# Step 1: Data Inventory Findings

## 1. Dataset completeness

All nine expected CSV files are present.

Here is the consolidated inventory:

|Table	|Rows	|Columns	|Initial assessment|
|---|---:|---:|---|
|Customers	|3,050	|10	|Customer demographic and profile data|
|Date	|1,096	|13	|Calendar dimension|
|Employees	|216	|8	|Employee and compensation data|
|Products	|345	|10	|Product catalogue|
|Stores	|15	|8	|Store information|
|Order details	|25,099	|10	|Individual product transactions|
|Orders 2022–2023	|7,942	|10	|Historical transaction records|
|Orders 2024	|4,058	|10	|Recent transaction records|
|Returns	|1,056	|7	|Product return information|
|Total	|42,885	|94	|Nine tables|


A few observations:

- We have 3,050 customer records, which provides a reasonable starting point for customer-level analysis.

- There are 12,000 order records across the two order tables.

- The 25,099 order-detail records give us an opportunity to investigate individual purchasing decisions rather than relying exclusively on order-level totals.

- The returns table provides additional information about post-purchase behavior.

However, these numbers do not necessarily represent 3,050 unique customers or 12,000 unique orders. We will establish that when we investigate key integrity.

2. Initial data type assessment

There is need to distinguish between three types of columns:

1. Correctly typed columns: Their current representation is appropriate for their intended use.

2. Incorrectly typed columns: Their current representation does not reflect the underlying data.

3. Columns requiring validation: Their current representation may be acceptable, but we need to establish whether it is appropriate.

Let's examine the notable findings.

### A. Customers table

|Column	|Current type	|Expected type	|Assessment|
|---|---|---|---|
|`customer_id`	|String	|String	|Appropriate|
|`full_name`	|String	|String	|Appropriate|
|`gender`	|String	|String	|Appropriate|
|`birth_date`	|String	|Date	|Requires conversion|
|`phone`	|Integer	|String	|Requires conversion|
|`email`	|String	|String	|Appropriate|
|`city`	|String	|String	|Appropriate|
|`region`	|String	|String	|Appropriate|
|`loyalty_tier`	|String	|String	|Appropriate|
|`registration_date`	|String	|Date	|Requires conversion|

Important observation: Phone numbers should not be stored as integers.

* Phone numbers are identifiers, not numerical measurements. Treating them as integers can remove leading zeros and makes numerical operations meaningless.

We should eventually convert this column to a string while preserving the original values wherever possible.

### B. Date table

Most columns have appropriate numerical or categorical representations.

However:

* full_date is currently stored as a string.

    - It should eventually be converted to a proper datetime format.

There is another observation worth investigating:

* is_weekend and is_ramadan are stored as integers.

    - This is not necessarily incorrect. They may contain binary values (0 and 1), which are perfectly usable. We will need to verify their actual values before deciding whether to represent them as Boolean variables.

### C. Employees table

Two columns deserve attention:

|Column	|Current type	|Expected type	|Reason|
|---|---|---|---|
|`store_id`	|Integer	|String or categorical	|Identifier, not a measurable quantity|
|`monthly_salary_egp`	|Integer	|Numeric	|Salary is a quantitative variable|

- The salary column being an integer is not necessarily a problem. We only want to convert it to a decimal if the underlying data contains fractional amounts or our analytical requirements justify it.

### D. Products table

This table is particularly interesting.

|Column	|Current type	|Assessment|
|---|---|---|
|`product_id`	|String	|Appropriate|
|`product_name_raw`	|String	|Appropriate|
|`category`	|String	|Appropriate|
|`subcategory`	|String	|Appropriate|
|`brand`	|String	|Appropriate|
|`unit_price_text`	|String	|Requires investigation|
|`unit_price`	|Integer	|Validate against actual prices|
|`unit_cost`	|Integer	|Validate against actual costs|
|`stock_qty`	|Integer	|Appropriate|
|`is_active`	|Integer	|Validate binary values|


The presence of both `unit_price_text` and `unit_price` is interesting.

We need to establish whether the text column contains inconsistent price representations, currency symbols, formatting issues, or values that disagree with the numeric column.

### E. Stores table

The most notable observation is:

`store_id` is stored as an integer.

- This is not automatically wrong. We would want to retain it as an integer if the identifiers are consistently represented and there are no formatting issues.

Unlike phone numbers, store identifiers do not necessarily require string representation.

### F. Order details table

The current data types appear generally appropriate.

However, we would want to investigate the following:

- Are quantities always positive?

- Are discount percentages within an acceptable range?

- Do selling prices agree with unit prices and discounts?

- Do line revenues reconcile with quantities and selling prices?

- Do line costs reconcile with quantities and unit costs?

These checks will be particularly important when constructing customer spending features.

### G. Orders tables

Both tables have identical structures, which is encouraging.

The main areas requiring attention are:

- `order_date` is stored as a string.

- `store_id` is stored as an integer.

- `date_id` is stored as an integer.

- `total_revenue` and `total_cost` are floating-point numbers.

The monetary columns have appropriate numerical representations, although we still need to establish their underlying business definitions.

### H. Returns table

`return_date` is stored as a string and will require date validation.

The remaining columns appear appropriately represented.

We will also need to investigate whether multiple return records can legitimately belong to the same order.

## 3. Preliminary conclusions

At this point, we have record of the following observations in our project documentation.

|Finding	|Implication|
|---|---|
|Nine expected tables available	|Initial source inventory complete|
|3,050 customer records	|Customer-level analysis is potentially feasible|
|12,000 order records	|Historical purchasing analysis is potentially feasible|
|Date columns stored as strings	|Date conversion and validation required|
|Phone numbers stored as integers	|Potential loss of leading zeros|
|Product price represented in both text and numeric columns	|Requires reconciliation|
|Identifiers have mixed data types	|Relationship compatibility must be verified|
|Two order tables have matching structures	|Potentially suitable for consolidation|


