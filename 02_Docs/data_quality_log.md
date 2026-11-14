<h1 align = "Center">
Lotus Group Customer Value & Profitability Analysis

<h2 align = "Center">
Data Quality Log
</h2>

</h1>

# Missing Values and Duplicate Records

## 1. Missing-value assessment

Our results from 01_data_inventory.ipynb show missing values in three places.

|Table	|Column	|Missing records	|Missing %|
|---|---|---:|---:|
|Customers	|`email`	|382	|12.52%|
|Orders 2022–2023	|`employee_id`	|427	|5.38%|
|Orders 2024	|`employee_id`	|205	|5.05%|


### A. Customers — Missing email addresses

We have 382 customers without email addresses, representing approximately 12.52% of the customer records.

Potential implications:

- We cannot assume that all customers can be reached through email marketing.

- Email-based customer segmentation or campaign analysis would exclude these customers unless we account for missing contact information.

- It does not necessarily affect purchasing behavior analysis because customer transactions are linked through customer_id, not email.

Our preliminary decision:

* Retain these records. We don't want to delete customers simply because their email addresses are unavailable.

We want to investigate whether the missing values are genuine nulls or whether additional records contain empty strings or placeholders.

### B. Orders — Missing employee IDs

This is another interesting finding.

|Period	|Missing employee IDs	|Percentage|
|---|---:|---:|
|2022–2023	|427	|5.38%|
|2024	|205	|5.05%|
|Combined	|632	|5.27%|


This means approximately 5.27% of recorded orders have no associated employee ID.

Possible explanations include:

- Some transactions were processed without an assigned employee.

- Some employee information was not captured during transaction recording.

- The missing values were introduced during data generation.

We cannot determine the actual explanation from the current evidence.

Our preliminary decision:

* Retain these transactions.

Employee identification is not essential to our customer value and profitability analysis. We may eventually use employee information for supplementary analysis, but missing employee IDs should not cause us to discard otherwise valid transactions.

## 2. Duplicate-record assessment

This is where we have a more significant finding.

Our results show:

|Table	|Fully duplicated records|
|---|---:|
|Customers	|50|
|Date	|0|
|Employees	|0|
|Products	|0|
|Stores	|0|
|Order details	|0|
|Orders 2022–2023	|0|
|Orders 2024	|0|
|Returns	|0|


The customers table contains 50 completely duplicated records.

This is important because customer-level analysis depends on correctly identifying individual customers.

However, it's important to distinguish between two different problems here:

1. Full-row duplicates: Two or more records have identical values across every column.

2. Primary-key duplicates: Multiple records share the same customer ID, even if other attributes differ.

We have identified the first problem, but we haven't yet established the extent of the second.

For our project, the distinction is particularly important because duplicated customer IDs could distort:

- Purchase frequency.

- Customer lifetime spending.

- Customer segmentation.

- Customer revenue and profit contribution.

We must resolve customer identity before constructing customer-level features.

## 3. Summary

|Table	|Issue	|Evidence	|Potential impact	|Status|
|---|---|---|---|---|
|Customers	|Missing email addresses	|382 records (12.52%)	|Limits contactability analysis	|Identified|
|Orders 2022–2023	|Missing employee IDs	|427 records (5.38%)	|Limits employee-level analysis	|Identified|
|Orders 2024	|Missing employee IDs	|205 records (5.05%)	|Limits employee-level analysis	|Identified|
|Customers	|Fully duplicated records	|50 records	|Could inflate customer counts and distort analysis	|Requires investigation|
|Other six tables	|No full-row duplicates detected	|0 duplicates in each	|No full-row duplication identified	|Preliminary check complete|

# Primary Key Integrity Assessment

## 1. Customers table: Confirmed duplication

Our results show:

|Metric	|Result|
|---|---:|
|Total records	|3,050|
|Distinct customer IDs	|3,000|
|Missing customer IDs	|0|
|Duplicate customer ID records	|50|
|Fully duplicated records	|50|

The duplicated customer ID investigation returned 100 rows.

This tells us something important.

- There are 50 customer IDs appearing twice each, and the corresponding records are identical.

The same pattern applies to the other duplicated IDs.

What does this mean?

We have established that:

1. There are 3,050 customer records.

2. There are 3,000 distinct customer identifiers.

3. Fifty customer records are additional copies of existing records.

4. We have no evidence of conflicting customer attributes among the duplicated IDs displayed.

This is a relatively straightforward duplication problem.

- Our preliminary cleaning decision: Remove the 50 exact duplicate records during the data cleaning phase, retaining one occurrence of each customer record.

We are not doing this just yet. We are still assessing the raw data.

After deduplication, we would expect the customers table to contain 3,000 records, assuming no other issues emerge.

This will also give us a reliable starting point for customer-level analysis.

## 2. Primary-key assessment across all tables

Let's consolidate our results.

|Table	|Primary key	|Records	|Distinct keys	|Duplicate keys	|Assessment|
|---|---|---:|---:|---:|---|
|Customers	|`customer_id`	|3,050	|3,000	|50	|Failed|
|Date	|`date_id`	|1,096	|1,096	|0	|Passed|
|Employees	|`employee_id`	|216	|216	|0	|Passed|
|Products	|`product_id`	|345	|345	|0	|Passed|
|Stores	|`store_id`	|15	|15	|0	|Passed|
|Order details	|`detail_id`	|25,099	|25,099	|0	|Passed|
|Orders 2022–2023	|`order_id`	|7,942	|7,942	|0	|Passed|
|Orders 2024	|`order_id`	|4,058	|4,058	|0	|Passed|
|Returns	|`return_id`	|1,056	|1,056	|0	|Passed|


* Interpretation: The results are encouraging.

    - All tables except customers have unique, non-null candidate primary keys within their respective tables.

This means their expected grain has passed its initial uniqueness test.

However, there are two important distinctions.

* First: The two order tables have passed their individual checks, but we haven't established whether their order IDs overlap across tables.

* Second: A unique primary key does not automatically establish that the records are valid or that their foreign-key relationships are intact.

We need to investigate both before proceeding.

## 3. Summary

|Table	|Issue	|Evidence	|Potential impact	|Status|
|---|---|---|---|---|
|Customers	|Duplicate customer records	|50 additional records; 3,000 distinct IDs across 3,050 rows	|Inflated customer counts and potentially distorted customer-level analysis	|Confirmed|
|Date	|None identified	|1,096 unique, non-null date IDs	|No primary-key issue identified	|Passed|
|Employees	|None identified	|216 unique, non-null employee IDs	|No primary-key issue identified	|Passed|
|Products	|None identified	|345 unique, non-null product IDs	|No primary-key issue identified	|Passed|
|Stores	|None identified	|15 unique, non-null store IDs	|No primary-key issue identified	|Passed|
|Order details	|None identified	|25,099 unique, non-null detail IDs	|No primary-key issue identified	|Passed|
|Orders 2022–2023	|None identified	|7,942 unique, non-null order IDs	|No primary-key issue identified	|Passed|
|Orders 2024	|None identified	|4,058 unique, non-null order IDs	|No primary-key issue identified	|Passed|
|Returns	|None identified	|1,056 unique, non-null return IDs	|No primary-key issue identified	|Passed|


# Foreign Key Integrity Assessment

## 1. Combined order table: Passed

|Check	|Result	|Interpretation|
|---|---:|---|
|Total order records	|12,000	|Both order tables combined successfully|
|Distinct order IDs	|12,000	|Every order has a unique identifier|
|Null order IDs	|0	|No orders are missing their identifier|
|Repeated order IDs	|0	|No overlap between the two source tables|


Conclusion: We can safely treat the combined orders DataFrame as our consolidated order table. There is no evidence of duplicate order IDs across the two source periods.

## 2. Foreign-key integrity: passed, with one exception

|Relationship	|Missing foreign keys	|Unmatched records	|Result|
|---|---:|---:|---|
|Orders → Customers	|0	|0	|Pass|
|Orders → Stores	|0	|0	|Pass|
|Orders → Employees	|632	|0	|Partial|
|Orders → Date	|0	|0	|Pass|
|Employees → Stores	|0	|0	|Pass|
|Order Details → Orders	|0	|0	|Pass|
|Order Details → Products	|0	|0	|Pass|
|Returns → Orders	|0	|0	|Pass|


What this tells us:

- **Customer relationship**: Every order references an existing customer. This is particularly important because customer behavior is the central focus of the project.

- **Store relationship**: Every order references a valid store.

- **Employee relationship**: 632 orders have no employee ID, representing approximately 5.27% of all orders. However, none of the populated employee IDs points to a nonexistent employee.

- **Date relationship**: Every order references an existing date record.

- **Order details**: Every order-detail record references a valid order and product.

- **Returns**: Every return references an existing order.

The missing employee IDs should not cause us to discard those orders. Employee information is not essential to the customer segmentation or the loss-making order model.

Preliminary decision: Retain all 632 records, preserve the missing employee IDs, and document the issue. We can investigate whether the missing assignments have any meaningful relationship with store, date, or order status later if necessary.

## 3. Summary

### 3. Referential Integrity Assessment

**Combined Orders Table**

The two order tables were successfully consolidated into a single dataset containing 12,000 records. All 12,000 order IDs were distinct, with no null or repeated identifiers. This confirms that the two source tables do not contain overlapping order IDs.

**Foreign-Key Validation**

Eight foreign-key relationships were assessed across the dataset. Seven relationships passed without missing or unmatched references. These included:

- Orders to Customers

- Orders to Stores

- Orders to Date

- Employees to Stores

- Order Details to Orders

- Order Details to Products

- Returns to Orders

The Orders-to-Employees relationship contained 632 missing employee IDs out of 12,000 orders (approximately 5.27%). However, no populated employee ID referenced a nonexistent employee record.

**Data Quality Decision**

All 632 orders with missing employee assignments will be retained. Employee assignment is not required for the primary customer behavior analysis, and deleting these records would unnecessarily reduce the available purchasing history.

Overall, the dataset demonstrates strong referential integrity, with missing employee assignments identified as the only issue in the relationships assessed so far.

# Investigating Transaction Dates

All 12,000 orders have valid dates, and every transaction's `order_date` agrees with the corresponding `full_date` in the date dimension.

## 1. Interpretation

|Validation check	|Result	|Assessment|
|---|---:|---|
|Total orders	|12,000	|—|
|Unparseable order dates	|0	|Pass|
|Unparseable dimension dates	|0	|Pass|
|Matching dates	|12,000	|Pass|
|Mismatching dates	|0	|Pass|


**Why this matters**: Our future analysis will depend heavily on time. We'll use transaction dates to calculate customer recency, purchase frequency and spending patterns, and to split orders chronologically (2022–2023 for training, 2024 for testing) in the loss-making order model.

- If the order dates disagreed with the date dimension, our time-based analysis could become unreliable. Fortunately, there are no inconsistencies.

## 2. Summary

### Transaction Date Validation

The `order_date` field in the consolidated orders table was validated against the `full_date` field in the date dimension using `date_id` as the joining key.

Results:

- Total orders assessed: 12,000

- Unparseable order dates: 0

- Unparseable date dimension values: 0

- Matching dates: 12,000

- Mismatching dates: 0

**Data Quality Decision:** All transaction dates are valid and consistent with the date dimension. No corrections are required.

# Return Date Validation

All 1,056 return records have valid, interpretable dates, and none occurs before its corresponding order date.

## 1. Interpretation

|Validation check	|Result	|Assessment|
|---|---|---|
|Total return records	|1,056	|—|
|Unparseable return dates	|0	|Pass|
|Returns before order date	|0	|Pass|
|Chronologically valid returns	|1,056	|Pass|


This is important because returns will influence how we interpret customer spending. For example, a customer who purchases frequently but also returns a substantial proportion of their purchases may have a different value to the business than gross sales figures suggest.

We have not yet established whether the return amounts reconcile with the original transactions. That will be a separate assessment.

## Summary

The `return_date` field was assessed for interpretability and chronological consistency against the original order dates.

Results:

- Total return records assessed: 1,056

- Unparseable return dates: 0

- Returns occurring before the original order date: 0

- Chronologically valid returns: 1,056

**Data Quality Decision**: All return dates are valid and occur on or after their corresponding order dates. No corrections are required.

# Investigating Categorical Variables

Summary of categorical quality findings:

|Table	|Column	|Issue	|Preliminary action|
|---|---|---|---|
|Customers	|gender	|Inconsistent capitalization	|Standardize case|
|Products	|subcategory	|Potentially overlapping categories	|Investigate|
|Orders	|payment_method	|Leading spaces	|Trim whitespace|
|Other categorical fields	|Various	|No obvious issues	|Retain pending further validation|


Importantly, we are not cleaning anything yet. We are documenting the issues so that our eventual cleaning decisions are deliberate and reproducible.

# Product Category and Subcategory Consistency

The relationship between product categories and subcategories was investigated to identify potential classification inconsistencies.

The dataset contains:

- 2 parent categories: Clothing and Electronics.

- 15 distinct subcategories.

- 345 products.

All six Clothing subcategories belong exclusively to Clothing, while all nine Electronics subcategories belong exclusively to Electronics.

The similarly named subcategories `Accessories` and `Accessories & Storage` were confirmed to belong to different parent categories and therefore represent legitimate classifications.

**Data Quality Decision:** No corrections are necessary. The existing category-subcategory hierarchy will be retained.

# Investigating Geographical Consistency

The geographical classifications in the customer and store tables were examined to establish whether cities consistently map to their respective regions.

Findings:

- 13 distinct cities were identified in both tables.

- 7 distinct regions were represented.

- Every city mapped to exactly one region.

- Customer and store tables contained identical city-region combinations.

- No conflicting geographical classifications were identified.

**Data Quality Decision:** The geographical classifications are internally consistent across both tables. No corrections are necessary.

# Numerical Data Quality Assessment

All seven numerical fields contain complete, positive values, except that zero discounts are present—which is perfectly legitimate.

However, descriptive statistics alone cannot establish whether the transaction calculations are correct. We now need to test the mathematical relationships between these fields.

## 1. Interpretation of our results

|Field	|Minimum	|Maximum	|Initial assessment|
|---|---:|---:|---|
|Quantity	|1	|4	|No zero or negative quantities|
|Unit price	|80	|18,600	|No zero or negative prices|
|Discount (%)	|0	|30	|Within a plausible range|
|Selling price	|56	|18,600	|No zero or negative prices|
|Unit cost	|40	|14,900	|No zero or negative costs|
|Line revenue	|56	|74,400	|No zero or negative revenue|
|Line cost	|40	|59,600	|No zero or negative costs|


A few observations:

- All 25,099 order-detail records are complete across these fields.

- Approximately 38% of records have no discount. This is not a data-quality problem.

- The maximum discount is 30%, which is reasonable, although we will verify the calculations.

- The maximum unit price is substantially higher than the median. 
    - This may reflect expensive electronics products, so we shouldn't automatically classify it as an outlier.

# Validating Transaction Calculations

**Assessment**: Transaction arithmetic consistency

The 25,099 records in `tbl_order_details` were assessed to establish whether selling prices, line-level revenue, and line-level costs were mathematically consistent.

Three validation rules were applied:

1. Expected selling price = Unit price × (1 − Discount percentage / 100).

2. Expected line revenue = Quantity × Selling price.

3. Expected line cost = Quantity × Unit cost.

**Findings:** All three validations returned zero mismatches.

**Conclusion:** The transaction-level financial calculations are internally consistent. No corrective action is currently required.

# Reconciling Order Details Against Order-level Financial Records

**Assessment**: Order-level versus transaction-level financial reconciliation.

The 12,000 records in the consolidated orders table were reconciled against aggregated financial values from the 25,099 order-detail records.

**Findings:**

- Revenue mismatches: 0.

- Cost mismatches: 0.

- Orders without corresponding order details: 0.

**Conclusion:** Every order has corresponding transaction-level records, and both recorded revenue and cost agree with the sum of their respective product-line values. No corrective action is required.

# Order Status Investigation

## 1. Order status findings

|Order status	|Orders	|Total revenue (EGP)|
|---|---:|---:|
|Completed	|10,442	|39,652,714.00|
|Returned	|1,063	|4,020,337.50|
|Cancelled	|495	|1,677,927.50|
|Total	|12,000	|45,350,979.00|

The concerning finding is that cancelled orders have recorded revenue of EGP 1.68 million.

This doesn't necessarily mean the data is wrong. It means that total_revenue records the order's financial value, not necessarily money actually received by the business.

For our project, cancelled orders should not count as qualifying purchases.

## 2. Return findings

|Return status	|Returns	|Return amount (EGP)|
|---|---|---|
|Refunded	|819	|3,206,996.50|
|Pending	|150	|402,529.50|
|Rejected	|87	|403,586.50|
|Total	|1,056	|4,013,112.50|


We can distinguish three situations:

- Refunded: Money was returned to customers.

- Pending: Refund has not yet been completed.

- Rejected: Refund was not approved.

This distinction matters when calculating actual customer spending.

## 3. Preliminary rules for revenue and profit

Proposed way forward

|Situation	|Counts as a sale?	|Revenue and profit|
|---|---|---|
|Completed order	|Yes	|Include|
|Cancelled order	|No	|Exclude|
|Returned order	|Requires return treatment	|Depends on return status|


These are preliminary rules. We need one more small investigation before finalizing them. The final rules are set in **Final Business Rules for Revenue and Profit** below.

# Checking Whether Rreturn Records Correspond to Returned Orders

**Assessment:** Consistency between order statuses and return records.

Of the 1,063 orders marked as Returned, 1,056 have corresponding return records, while seven have no matching return record.

No Completed orders were found with associated return records.

**Conclusion:** Order and return records are generally consistent. Seven returned orders require attention because their corresponding return details are unavailable. These records will be flagged rather than assigned assumed refund amounts.

## Proposed way forward

The seven returned orders without return records cannot be assigned a refund outcome. They are treated as returned (not a sale) in the final rules below.

# Customer Dates Validation

**Assessment:** Customer birth-date and registration-date validity.

The customer date fields were assessed for parsing errors and chronological inconsistencies.

**Findings:**

- Invalid birth dates: 567 (approximately 18.6%).

- Invalid registration dates: 0.

- Birth dates after registration dates: 0.

- Registration dates after the final transaction date: 0.

**Conclusion:** Invalid birth dates will be converted to missing values during cleaning rather than estimated or artificially reconstructed.

Registration dates are valid as dates, but 2,034 orders (17% of revenue) are dated before the customer's registration date. See **Orders Placed Before Customer Registration** below. Customer tenure will therefore be measured from the **first purchase date**, not the registration date.

# Product Margin Structure

**Assessment:** Price and cost consistency between order lines and the product table, and the margin available in each category.

**Findings:**

- Order-line unit prices and unit costs match the product table for all 25,099 lines (0 mismatches).

- No zero or negative prices, costs or quantities. Discounts take only the values 0–30% in 5% steps; quantities range from 1 to 4.

|Category	|Margin before discount (min – max)|
|---|---|
|Clothing	|50% – 60%|
|Electronics	|12.5% – 33%|

**Conclusion:** No correction is required. The margin gap between categories is a key business finding: a discount of 20% or more can remove most or all of an Electronics product's margin, so some discounted Electronics sales are sold at a loss. This is investigated in the exploratory analysis and the loss-making order model.

# Orders Placed Before Customer Registration

**Assessment:** Chronological consistency between order dates and customer registration dates.

**Findings:**

- 2,034 orders (17% of order value, EGP 7.67M) are dated before the customer's registration date.

- All of these orders link to valid customers, products and stores, and their financial values reconcile.

**Decision:** Retain these orders as valid sales. Registration most likely records sign-up to the loyalty programme, which can happen after a first purchase. Excluding them would remove 17% of revenue and understate customer value. Customer tenure will be measured from the first purchase date instead of the registration date.

# Customers Without Purchases

**Findings:**

- 55 of the 3,000 customers have no orders at all.

- A further 37 customers have orders, but all of them were cancelled or refunded.

- In total, 92 customers have no qualifying sale.

**Decision:** Keep all 92 in the customer table with zero revenue and profit. Exclude them from the clustering, because there is no purchase behaviour to segment, and report them separately as **"Registered, never purchased"**.

# Final Business Rules for Revenue and Profit

These rules define a **qualifying sale** and are applied consistently throughout the project.

|Order situation	|Orders	|Order value (EGP)	|Counts as a sale?	|Reason|
|---|---:|---:|---|---|
|Completed	|10,442	|39,652,714.00	|Yes	|Goods sold and paid for|
|Returned, return **rejected**	|87	|403,586.50	|Yes	|Refund refused; the customer kept the goods, so the sale stands|
|Returned, return **refunded**	|819	|3,206,996.50	|No	|Money returned to the customer|
|Returned, return **pending**	|150	|402,529.50	|No	|Outcome unknown; excluded as the cautious option|
|Returned, **no return record**	|7	|—	|No	|Refund outcome cannot be established|
|Cancelled	|495	|1,677,927.50	|No	|No money changed hands; the value is booked, not earned|

**Definitions:**

- **Revenue** = sum of `line_total_revenue` for qualifying sales.

- **Cost** = sum of `line_total_cost` for qualifying sales.

- **Profit** = Revenue − Cost. **Margin** = Profit ÷ Revenue.

- **Loss-making order** = a qualifying order whose cost exceeds its revenue.

Returned goods are assumed to go back into stock, so excluded returned orders carry neither revenue nor cost.

**Sensitivity:** if every pending return were later rejected, qualifying revenue would rise by EGP 0.40M (about 1%). This is too small to change the findings.

**Output:** `01_Data/processed/sales_clean.csv`, one row per product line of every qualifying sale, with revenue, cost, profit and margin.

# Data Quality Assessment Summary

We've now completed the checks necessary to establish whether our data is suitable for the intended analysis.

Our key findings include:

|Issue	|Finding	|Planned treatment|
|---|---|---|
|Duplicate customers	|50 duplicated records	|Remove exact duplicates, retaining one record per customer.|
|Missing customer emails	|382	|Retain missing values.|
|Missing employee IDs	|632 orders	|Retain; not essential for this analysis.|
|Inconsistent gender capitalization	|Identified	|Standardize categories.|
|Whitespace in payment methods	|Identified	|Remove leading/trailing whitespace.|
|Invalid birth dates	|567	|Convert to missing values.|
|Missing return records	|7 returned orders	|Treat as returned; not a sale.|
|Transaction arithmetic	|No mismatches	|No correction required.|
|Order-level financial reconciliation	|No mismatches	|No correction required.|
|Foreign-key integrity	|No unmatched populated keys	|No correction required.|
|Date consistency	|No mismatches	|No correction required.|
|Product price and cost consistency	|No mismatches	|No correction required.|
|Orders before customer registration	|2,034 orders (17% of value)	|Retain as valid sales; measure tenure from first purchase.|
|Customers without a qualifying sale	|92 (55 with no orders)	|Keep; exclude from clustering and report separately.|
|Cancelled orders	|495 (EGP 1.68M booked)	|Exclude from sales.|
|Returned orders	|1,063	|Rejected returns count as sales; refunded, pending and unrecorded returns do not.|
