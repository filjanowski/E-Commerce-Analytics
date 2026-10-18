# E-Commetce-Analytics
Brazilian E-Commerce Analytics
Power BI · Power Query · DAX
I built a two-page Power BI report to explore sales performance, delivery quality and repeat purchasing using the Olist Brazilian e-commerce dataset. The project covers data preparation, relational modelling, KPI development and report design, with a focus on making the business meaning of each metric clear.
Report: [Brazylian-ecommerce.pbix](Brazylian-ecommerce.pbix)
Business questions
- How do product sales change over time, across categories and across Brazilian states?
- Which locations combine substantial sales with longer delivery times?
- How do customer ratings differ between on-time and late deliveries?
- How often do customers make another purchase, and how long does it take?
- What proportion of customers who leave a low rating return within 90 days?
Report pages
1. Sales Overview
Four KPI cards show Product Sales, Orders with Items, Items Sold and Sales per Order. The page includes monthly sales, the top 10 product categories, and a comparison of sales and average delivery time by customer state. Date, state and category slicers support further exploration.
2. Customer Behaviour
Five KPI cards show Unique Customers, Repeat Customer Rate, Low-score Return (90d), Average Review Score and Late Delivery Rate.
Visual	What it measures
Payment Value by Method	Recorded payment value and share by payment method
Purchase Frequency	Customers with 1, 2 or 3+ delivered orders
Review Score Distribution	Number of reviewed orders at each score from 1 to 5
Purchase Activity by Day & Time	Delivered orders by weekday and four-hour purchase-time band
Time to Second Purchase	Repeat customers grouped by elapsed time between their first two delivered purchases
Review Score by Delivery Status	Average order review score for on-time and late deliveries


Results
The figures below use the full delivered-order history, without date, state or category selections. Reviews, delivery performance and the 90-day return metric use the eligible populations described below. Monetary values are in Brazilian reais (BRL / R$).
Metric	Result	Definition
Product Sales	R$13,221,498.11	Sum of item prices, excluding freight
Orders with Items	96,478	Distinct delivered orders represented in the order-items table
Items Sold	110,197	Number of order-item rows
Sales per Order	R$137.04	Product Sales divided by Orders with Items
Unique Customers	93,358	Distinct customer_unique_id values with a delivered order
Repeat Customer Rate	3.00%	Customers with at least two delivered orders divided by all customers in scope
Average Review Score	4.16 / 5	Mean of the latest available review score per delivered order
Late Delivery Rate	6.77%	Late orders divided by delivered orders with a usable delivery classification
Low-score Return (90d)	0.81%	Eligible low-score customers who purchase again within 90 days of their review


Selected findings:
- On-time deliveries average 4.29/5, compared with 2.27/5 for late deliveries. Delivery reliability is an area to investigate when prioritising improvements to customer experience.
- 2,801 customers purchase more than once. Among these repeat customers, the median time to a second purchase is approximately 29 days.
- Credit cards account for 78.5% of recorded payment value, while customers in São Paulo state account for 38.3% of product sales.
- 79 of 9,755 eligible low-score customers return within 90 days. This provides a baseline for investigating recovery after a poor customer experience.
Data preparation and modelling
The source contains 99,441 orders across all statuses. The report is filtered to delivered, covering purchase dates from 15 September 2016 to 29 August 2018.
Power Query handles:
- Explicit data types and locale-aware conversion of dates and numeric fields.
- Delivery-quality checks, elapsed delivery time and late-delivery classification. An order is late when its actual delivery date is after its estimated delivery date; unusable records retain a blank classification.
- Review aggregation to one row per order_id, retaining the review with the latest review_answer_timestamp, before merging scores into Orders.
- English product-category labels, missing-category handling and translation exceptions.
- A Customer Summary table grouped by customer_unique_id, containing purchase counts, second-purchase intervals, frequency bands and return-eligibility flags.
customer_unique_id identifies returning customers across orders. customer_id connects each order to its customer record and is used for that relationship.
Core reporting relationships:
```mermaid

![ ](https://github.com/filjanowski/E-Commerce-Analytics/blob/main/Ralationships.png)

```
Arrows represent single-direction filter propagation. Item and payment records remain in separate tables, avoiding duplicated totals when an order has multiple items and multiple payment entries. Reviews and category translations are incorporated through Power Query merges.
DAX measures calculate sales, order counts, payment shares, review averages and customer-return rates. Supporting measures are reused in KPI formulas and formatted payment labels.
How the 90-day return metric works
For each customer:
1. Identify the first observed delivered order, sorting by purchase timestamp and then order_id to resolve ties.
2. Use that order's latest review and its review_answer_timestamp as the starting point.
3. Require a review score of 1 or 2 and a review timestamp on or after the first purchase.
4. Require a full 90-day follow-up window before the latest purchase timestamp among delivered orders in the dataset.
5. Count a return if another delivered order was purchased after the review and no later than 90 days afterwards. This can be a later order beyond the customer's second purchase.
The result is 79 / 9,755 = 0.81%. Using a complete follow-up window prevents customers reviewed near the end of the dataset from being classified as non-returners before they have had 90 days to return.
Scope and interpretation
- The analysis describes observed associations; it does not establish that late delivery causes lower ratings or reduced repeat purchasing.
- Customer frequency and second-purchase intervals use the full observed delivered-order history. Customer Summary is calculated during refresh; date selections do not recalculate these historical attributes.
- A customer with one observed order may have purchased outside the dataset's coverage. The 3% repeat-purchase rate and the 0.81% low-score return rate use different populations and observation windows.
- Average ratings use 95,832 reviewed delivered orders. The late-delivery calculation uses 96,470 orders with usable delivery information. Visuals filtered to on-time or late delivery also exclude unknown classifications.
- The data includes partial years. Monthly trends should be interpreted with those coverage boundaries in mind.
Open and refresh the report
1. Download Brazylian-ecommerce.pbix and open it in Power BI Desktop. The saved report contains imported data for inspection.
2. To refresh it, download the original dataset linked below and extract the CSV files into one local folder, keeping their filenames unchanged.
3. Select Transform data, open the Source_Files query and update the path used by Folder.Files in Advanced Editor.
4. Update any hardcoded Folder Path values in the CSV queries' Navigation steps to the same folder. These navigation keys also contain the original local path and need to match the new location.
5. Select Close & Apply, then Refresh.
With the report filtered to delivered orders and visual selections cleared, the customer KPIs should show 93,358 customers, 3.0% repeat customers and 0.81% low-score returns.
Data source and attribution
Data provided by Olist: Brazilian E-Commerce Public Dataset by Olist.
The original dataset contains anonymised commercial records from 2016–2018 and is published under CC BY-NC-SA 4.0. This portfolio project applies data transformations, calculated metrics and visualisations to that source.
Author: Filip Janowski
