# Online Retail Analytics — e-commerce sales analysis in Power BI



## 1\. Project overview



**Objective:** to build an analytical report that helps evaluate sales, the product structure of revenue, customer behavior, and the monetary volume of cancelled transactions. Based on the metrics, the report highlights areas for further review and provides recommendations for improving business performance.



**Tools:** Power BI Desktop, Power Query (M), DAX.



**Source:** [Online Retail — UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/352/online+retail) ([DOI: 10.24432/C5BW33](https://doi.org/10.24432/C5BW33)). Data from a UK-based online retailer covering the period from December 1, 2010 to December 9, 2011. The dataset license is **CC BY 4.0**.



**Key questions:**

* How do sales, orders, and average order value change over time?
* Which countries and items generate the main share of revenue?
* How are product items distributed across ABC groups?
* What is the structure of the customer base by order frequency?
* How concentrated are cancelled orders, and which transactions require review?



## 2\. Report structure



### Sales Overview — sales overview

Six KPIs: gross sales, cancellation amount, net sales, total orders, average order value, and cancellation rate. In addition, the page includes monthly sales dynamics, order dynamics and average order value, as well as the distribution of revenue by country. Slicers by year, month, and country are provided.



### Product Analytics — product analysis

Top 10 by revenue and units sold, an ABC analysis table, and summary indicators for groups A/B/C. For product analysis, a `Product Type = Product` filter is applied to exclude the technical items `POSTAGE`, `DOTCOM POSTAGE`, and `Manual`.



### Customer Analytics — customer analysis

Number of customers, number of repeat customers, gross revenue per customer; top 10 customers by revenue and by number of orders; and a comparison of customers with one order versus multiple orders.



### Cancellations Analysis — cancellations analysis

Gross and net sales, cancellation amount and cancellation rate; monthly cancellation dynamics; and the largest products and countries by cancelled orders.



## 3\. Data preparation and methodology



Source fields: `InvoiceNo`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `UnitPrice`, `CustomerID`, `Country`.



**Power Query preparation:**

1. Data types were checked and assigned, including transaction date/time, quantity, price, and text identifiers.
2. `TransactionType` was added: `Cancellation` if `InvoiceNo` starts with `C`, otherwise `Sale`.
3. `LineAmount = Quantity × UnitPrice` was added.



**Calculated DAX columns:**



* `Has Customer ID` — a flag indicating whether a customer identifier is present, used to exclude blank `CustomerID` values from customer visuals.
* `Product Type` — `Technical` for `POSTAGE`, `DOTCOM POSTAGE`, and `Manual`; all other items are classified as `Product`.



**Core DAX calculations:**

|Metric|Logic|
|-|-|
|`Gross Sales`|Sum of `LineAmount` for `Sale` transactions with `Quantity > 0` and `UnitPrice > 0`|
|`Cancellation Amount`|Positive amount for `Cancellation` transactions with `Quantity < 0` and `UnitPrice > 0`|
|`Net Sales`|`Gross Sales − Cancellation Amount`|
|`Total Orders`|Number of unique `InvoiceNo` values for valid positive sales|
|`Average Order Value`|`Gross Sales / Total Orders`|
|`Cancellation Rate`|`Cancellation Amount / Gross Sales` — monetary share|
|`Total Customers`|Unique non-blank `CustomerID` values for valid positive sales|
|`Repeat Customers`|Customers with two or more unique orders during the selected period|
|`Revenue per Customer`|`Gross Sales` for rows with a populated `CustomerID`, divided by `Total Customers`|

**ABC analysis:** items are ranked by gross revenue, and both share and cumulative share are calculated. The adopted thresholds are A — up to 80% of cumulative revenue, B — up to 95%, and C — all remaining items. Classes are determined within the active filter context. On the Product Analytics page, the three technical items mentioned above are excluded from the calculation. The helper tables `ABC Groups` and `Customer Groups` are used to display groups in visuals.

**Customer terminology:** on the chart, the `New` category means **one order during the selected period**, while `Repeat` means **two or more**.



## 4\. Key results and business insights



### 4.1. Sales and geography



**Observations**

* Gross sales are **about £10.67M**; cancelled orders amount to **about £896.81K**; and net sales under the adopted methodology are **about £9.77M**.
* The monetary cancellation rate is **8.41%**.
* In 2011, revenue increases noticeably in autumn; the chart shows the highest monthly figure in **November** (approximately **£1.5M** in net sales).
* The **United Kingdom** significantly outperforms other countries in total revenue: it is the main sales market in the observed dataset.



**Interpretation**

* The autumn growth may be related to seasonal demand and pre-holiday purchases, but confirming seasonality would require comparable full periods across several years.
* The sharp decline in December on the chart is **not informative**, because the dataset for December 2011 only contains data through December 9.



**Practical actions**

* Check sales dynamics for the presence of pronounced seasonality. **Economic purpose:** to plan purchasing and resources in advance for high-demand periods, reducing lost sales due to stockouts and excess inventory during slow periods.
* Compare autumn growth with the number of orders and average order value to determine which component is driving revenue growth. **Economic purpose:** to choose between stimulating order volume and increasing average order value, so that marketing budget can be directed toward the most relevant source of revenue growth.
* Review the dynamics of key countries separately, because aggregate results may hide patterns in smaller markets. **Economic purpose:** to allocate promotion and sales development spending across countries based on their revenue contribution and avoid inefficient spending.



**Practical value of the Sales Overview page**

It shows **where revenue is generated** — across time periods and countries — and where its volume is declining. Combined analysis of orders and average order value helps determine what is driving sales changes and supports decisions on assortment, purchase planning, and budget allocation aimed at increasing revenue and preventing unnecessary costs.



### 4.2. Product structure and ABC



**Observations**

* The leaders by monetary revenue and by units sold **are different**. `REGENCY CAKESTAND 3 TIER` is the leader in the revenue ranking, while `PAPER CRAFT, LITTLE BIRDIE` and `MEDIUM CERAMIC TOP STORAGE JAR` lead in quantity sold.



**Interpretation**

* High sales volume does not necessarily mean leadership in revenue: unit price also has a strong impact.
* ABC segmentation helps prioritize work with the assortment.



**Practical actions**

* Use ABC groups to prioritize further review; purchases of groups A and B can be increased if demand is present, while purchases of group C can be reduced or excluded if there are no supplier buyout obligations. **Economic purpose:** to improve the availability of high-demand items and reduce capital tied up in low-turnover assortment, storage costs, and the risk of illiquid inventory.



**Practical value of the Product Analytics page**

It shows **which products generate revenue**, which items sell in high quantities but bring in less money, and where turnover is concentrated. This is the basis for deciding which items should be kept in stock and where purchasing budget should be directed in order to avoid losing sales due to stockouts and to avoid tying up capital in weak-performing items. At the same time, the dashboard does not include cost of goods sold or inventory balances, so it cannot directly assess profit or overstock based on this page alone.



### 4.3. Customer behavior



**Observations**

* The dataset contains **about 4K identified customers**; of these, **about 3K** placed two or more orders during the analyzed period.
* The chart shows that customers with multiple orders are **more numerous** than customers with only one order.
* Average **gross** revenue per customer is about **£2.05K**.
* The top-10 customer lists by purchase amount and by number of orders do not fully overlap.



**Interpretation**

* The observed customer base includes a meaningful segment of repeat customers.
* A customer with a high number of orders does not necessarily generate the highest revenue; average order size also matters.



**Practical actions**

* Segment customers by order frequency, revenue, and recency of last purchase (RFM) in the next stage of analysis. **Economic purpose:** to target retention offers more precisely at valuable customer segments, increasing repeat revenue without applying the same spending to all customers.
* Analyze separately customers with high revenue but relatively few orders and customers with high frequency but lower average order value. **Economic purpose:** for the first group, the focus is on stimulating repeat purchases; for the second, on increasing basket size. This creates opportunities to grow revenue from the existing customer base.



**Practical value of the Customer Analytics page**

It shows **which customers generate revenue**, how significant the role of repeat orders is, and how purchase frequency differs from monetary contribution. This helps choose targeted actions for retaining valuable customers and increasing average order value instead of applying uniform promotion costs across the whole customer base. Changes in revenue per customer can also help detect a possible decline in customer activity.



### 4.4. Cancelled transactions



**Observations**

* The monetary volume of cancellations is about **£896.81K**, or **8.41%** of gross sales.
* The monthly dynamics show a **December spike** in cancellations.
* Among the largest items by cancellation amount are **`AMAZON FEE`**, **`PAPER CRAFT, LITTLE BIRDIE`**, and **`Manual`**.
* As with sales, the United Kingdom dominates cancellation amounts, which is consistent with the structure of sales.



**Interpretation**

* Based on the available data, it is not possible to determine the reasons for cancellations, the timing of the original sale, or the actual financial damage without linking cancellations back to the original orders.



**Practical actions**

* Separate product-related cancellations from technical adjustments, and then recalculate metrics for each category. **Economic purpose:** to avoid treating technical postings as merchandise-related losses and to focus loss-reduction actions on transactions that create real returns and associated costs.
* Check the causes of the December spike and propose measures to reduce returns. **Economic purpose:** to reduce revenue loss and the costs of reverse logistics and return processing if the spike is associated with product-related cancellations.
* Calculate not only the amount of cancellations by country and product, but also the **monetary cancellation rate** relative to sales. **Economic purpose:** to identify products and markets with a disproportionately high cancellation rate and prioritize actions with the greatest potential to reduce losses, rather than simply those with the highest sales volume.



**Practical value of the Cancellations Analysis page**

It shows **where cancellation amounts arise** — by month, country, and item — and how large they are relative to gross sales. This helps identify transactions with potentially high return-related costs and supports decisions aimed at reducing related losses and operating expenses. Since the cancellation amounts include technical charges, the entire cancellation volume cannot automatically be treated as direct financial damage.



## 5\. How to open the project

1. Download the report file `Online\\\_Retail\\\_Analytics.pbix` from the repository **after it has been added**.
2. Open the file in **Power BI Desktop**.
3. Switch between the four pages and use the available date and country filters.

