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


