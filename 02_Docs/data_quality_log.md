<h1 align = "Center">
Lotus Group Customer Behavior Analysis

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

Employee identification is not essential to our primary customer behavior models. We may eventually use employee information for supplementary analysis, but missing employee IDs should not cause us to discard otherwise valid transactions.

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

- Repeat-purchase labels.

- Regression predictions.

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
