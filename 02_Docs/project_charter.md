<h1 align = "Center">
Lotus Group Customer Value & Profitability Analysis

<h3 align = "Center">
Who drives Lotus Group's profit, and where are discounts eroding it?
</h3>

<h3 align = "Center">
Project Understanding and Problem Definition
</h3>

</h1>

# Project Context

Lotus Group is a retail business operating across Egypt. The available dataset contains customer, transaction, product, store, employee, date and returns information.

The key change from our earlier Power BI project is the analytical focus.

**Previous project:** *Business intelligence*

- Helped us understand historical performance through descriptive analysis, KPIs and interactive dashboards.

**Current project:** *Customer value and profitability analytics with machine learning*

- We want to understand which customers create the most value, how pricing and discounts affect profit, and use machine learning to turn these patterns into customer segments and practical discount rules.

> Our earlier dashboards showed *how much* the business sold, whereas

> This project explains *who* and *what* makes that revenue profitable, and *where* profit is being lost.

## Business Problem Statement

> *"We've grown to 15 stores and EGP 40M in completed sales, but we don't know which customers really drive our business or whether our discounts are paying off. We need to know who to prioritise, and which discounts we should stop."*

Lotus Group keeps detailed records of its customers, orders, products, costs and returns. These records describe what was sold, but the business lacks a structured view of where its value and profit come from.

The analytical challenge is to turn these raw, potentially inconsistent datasets into reliable customer-level and order-level tables. From these we can measure how concentrated revenue and profit are, group customers by value, and identify the basket and discount combinations that lead to loss-making sales.

The findings should support customer prioritisation, marketing investment and discount policy.

Because the dataset is synthetic, the findings are only intended to demonstrate analytical methods and potential business applications, not to establish facts about actual retail customers in Egypt.

### General Objective

* To identify the customer groups and pricing practices that create or destroy profit at Lotus Group, and to turn them into practical rules for marketing and discounting.

### Specific Objectives

1. Clean and check the customer, order, product and cost data so that revenue and profit figures can be trusted.

2. Measure how concentrated revenue and profit are across customers, product categories and stores.

3. Group customers into distinct value segments using unsupervised machine learning.

4. Build a model that flags loss-making orders from basket contents and discounts, and express it as plain discount rules.

5. Estimate the financial impact of acting on the findings, and make evidence-based recommendations.

## Research Questions

|No.	|Research question	|Analytical approach|
----|----|----|
|1	|How much of revenue and profit comes from the top 10% and top 20% of customers?	|Exploratory data analysis and Pareto (concentration) analysis|
|2	|What distinct customer segments exist, and what is each worth?	|Feature engineering, K-Means clustering and segment profiling|
|3	|How does discount level affect margin in each product category?	|Exploratory data analysis and margin analysis|
|4	|Which combinations of basket and discount lead to loss-making orders?	|Classification (decision tree) and rule extraction|
|5	|How much profit would discount limits have protected in 2024?	|Scenario ("what-if") analysis|

One distinction is important: *our models identify patterns and associations in historical data. They do not by themselves prove that a particular behaviour or discount causes a customer to spend more or less.*

### Analytical Components

**Component A: Customer value segmentation**

**Problem type:** Unsupervised learning (clustering)

- **Business question:** What distinct groups of customers exist, and how much revenue and profit does each group contribute?

**Unit of analysis:** One record per customer.

**Candidate features:** recency, purchase frequency, total and average spend, profit contributed, share of spend on Electronics, average discount used, and return and cancellation history.

**Output:** A segment label for each customer, a profile of each segment (size, revenue share, profit share, typical behaviour), a plain business name for each segment, and a recommended action for each.

The segments describe customers' historical value. They are intended to guide prioritisation and service levels, not to forecast individual future purchases.

**Component B: Loss-making order detection**

**Problem type:** Binary classification

- **Business question:** Given an order's basket contents and discounts, will that order lose money?

|Target value	|Definition|
|----|----|
|1	|The order's total cost exceeds its revenue (negative profit).|
|0	|The order makes a profit or breaks even.|

**Unit of analysis:** One record per order.

**Candidate features:** information known at checkout only, such as product categories and subcategories in the basket, discount levels, number of items, basket value and store.

The exact rules for which orders to include, such as how to treat cancelled and returned orders, will be set after inspecting the data. Because margin is closely tied to price, cost and discount, we expect high accuracy. The value of this component is in turning the model into clear, auditable discount rules, not in the accuracy score alone.

### Measure of Success

We will set the success criteria before modelling, so the results are judged against fixed targets rather than chosen afterwards.

|Component	|Evaluation criteria|
|----|----|
|Data preparation	|Key integrity, documented cleaning decisions, valid data types, and order-line revenue and cost that reconcile to order totals|
|Value and margin analysis	|Reproducible metrics and interpretable findings that answer the research questions|
|Segmentation	|3–6 segments; silhouette score ≥ 0.3; each segment ≥ 5% of customers; segments remain stable when re-run with different random seeds|
|Loss-making order detection	|Recall ≥ 90% on loss-making orders in the 2024 test period; outperforms a simple "discount ≥ 25%" rule; tree depth ≤ 5 so it reads as rules|
|Validation	|Chronological split for the classifier: train on 2022–2023 orders, test on 2024 orders|
|Business impact	|Every recommendation linked to an estimated EGP impact|

- For instance, loss-making orders are likely to be a small share of all orders, so a model could reach high accuracy simply by predicting that no order loses money.

    - Accuracy alone would therefore be misleading, which is why recall on loss-making orders and comparison against a simple rule are the main criteria.

### Project Scope and Deliverables

The project scope includes:

- Data inventory and quality assessment.

- Data cleaning and integration across the nine tables, including checks on cost, discount and margin fields.

- Exploratory analysis of revenue concentration, category margins and discount impact.

- Customer-level and order-level feature engineering.

- Customer value segmentation with K-Means, including selection of the number of segments, profiling and stability checks.

- A loss-making order classifier with baseline comparison and plain-language rule extraction.

- Scenario analysis of discount limits and business recommendations.

- Reproducible code and documentation suitable for a data science portfolio.

Advanced techniques, deployment and additional targets are out of scope unless the data or our findings give a compelling reason to expand.
