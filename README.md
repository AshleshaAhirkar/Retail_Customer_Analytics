# 🛍️ Customer Shopping Beahaviour Analysis | Python + SQL + Power BI

An end-to-end data analytics project analyzing 3,900+ retail transactions to uncover customer behavior insights, revenue trends, and product performance using Python, PostgreSQL, and Power BI.

---

## 📌 Project Objective  

To analyze customer shopping behavior and generate actionable business insights by performing data cleaning, SQL-based analysis, and interactive dashboard visualization.

---

## 🛠️ Tech Stack  

- **Python (Pandas, NumPy)** – Data Cleaning & Feature Engineering  
- **PostgreSQL** – Business Analysis using CTEs & Window Functions  
- **Power BI** – KPI Dashboard & Data Visualization  
- **SQLAlchemy** – Database Connectivity  

---

# 🐍 Data Cleaning & Transformation (Python)

The dataset was cleaned and prepared using Python:

- Handled missing values using median imputation  
- Standardized column names using snake_case formatting  
- Created customer age segmentation using quartile-based grouping  
- Converted purchase frequency into numeric format  
- Removed redundant columns  
- Loaded structured dataset into PostgreSQL database  

This ensured clean, structured, and analysis-ready data.

---

# 🗄️ SQL Analysis & Business Queries  

### 🔹 Total Revenue by Gender

```sql
SELECT gender,
       SUM(purchase_amount) AS total_revenue
FROM customer
GROUP BY gender;
```

---

### 🔹 Customers Who Used Discount & Spent Above Average

```sql
WITH cte AS (
    SELECT AVG(purchase_amount) AS average_spend
    FROM customer
)
SELECT c.customer_id,
       c.purchase_amount
FROM customer c, cte
WHERE c.discount_applied = 'Yes'
  AND c.purchase_amount >= cte.average_spend;
```

---

### 🔹 Top 5 Products with Highest Average Review Rating

```sql
SELECT item_purchased,
       ROUND(AVG(review_rating)::numeric, 2) AS average_product_rating
FROM customer
GROUP BY item_purchased
ORDER BY AVG(review_rating) DESC
LIMIT 5;
```

---

### 🔹 Average Purchase Comparison: Standard vs Express Shipping

```sql
SELECT shipping_type,
       ROUND(AVG(purchase_amount), 2) AS average_spend
FROM customer
WHERE shipping_type IN ('Standard', 'Express')
GROUP BY shipping_type;
```

---

### 🔹 Subscriber vs Non-Subscriber Revenue Comparison

```sql
SELECT subscription_status,
       COUNT(customer_id) AS total_customers,
       ROUND(AVG(purchase_amount), 2) AS avg_spend,
       SUM(purchase_amount) AS total_revenue
FROM customer
GROUP BY subscription_status
ORDER BY total_revenue DESC;
```

---

### 🔹 Top 5 Products with Highest Discount Usage Percentage

```sql
SELECT item_purchased,
       ROUND(
           SUM(CASE WHEN discount_applied = 'Yes' THEN 1 ELSE 0 END) * 100.0
           / COUNT(*), 2
       ) AS discount_percentage
FROM customer
GROUP BY item_purchased
ORDER BY discount_percentage DESC
LIMIT 5;
```

---

### 🔹 Customer Segmentation (New, Returning, Loyal)

```sql
WITH customer_type AS (
    SELECT customer_id,
           CASE
             WHEN previous_purchases = 1 THEN 'New'
             WHEN previous_purchases BETWEEN 2 AND 10 THEN 'Returning'
             ELSE 'Loyal'
           END AS customer_segment
    FROM customer
)
SELECT customer_segment,
       COUNT(*) AS total_customers
FROM customer_type
GROUP BY customer_segment;
```

---

### 🔹 Top 3 Most Purchased Products Within Each Category

```sql
WITH cte AS (
    SELECT category,
           item_purchased,
           COUNT(*) AS cnt
    FROM customer
    GROUP BY category, item_purchased
)
SELECT category,
       item_purchased,
       cnt
FROM (
    SELECT category,
           item_purchased,
           cnt,
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY cnt DESC) AS rnk
    FROM cte
) ranked
WHERE rnk <= 3;
```

---

### 🔹 Repeat Buyers & Subscription Status

```sql
SELECT subscription_status,
       COUNT(customer_id) AS repeat_buyers
FROM customer
WHERE previous_purchases > 5
GROUP BY subscription_status;
```

---

### 🔹 Revenue Contribution by Age Group

```sql
SELECT age_group,
       SUM(purchase_amount) AS total_revenue
FROM customer
GROUP BY age_group
ORDER BY total_revenue DESC;
```

---


# 📈 Power BI Dashboard  

An interactive Power BI dashboard was developed to consolidate all insights into a single reporting view.

### Dashboard Highlights:

- Total Customers: 3.9K  
- Average Purchase Amount: $59.76  
- Average Review Rating: 3.75  
- Category-wise Revenue Distribution  
- Sales by Age Group  
- Subscription Insights  
- Revenue by Demographics  

The dashboard streamlines reporting and enables faster data-driven decision-making.

---

## 📷 Dashboard Preview  

![Customer Behaviour Dashboard](dashboard.png)

---

# 🚀 Key Insights  

- Identified Top 5 high-rated products driving customer satisfaction  
- Segmented customers into New, Returning, and Loyal groups  
- Analyzed revenue contribution across age and gender  
- Evaluated discount and subscription impact on revenue  
- Built a consolidated automated reporting dashboard  

---
## 📌 Conclusion  

This project demonstrates end-to-end data analytics capabilities, from data cleaning and SQL-based analysis to interactive dashboard visualization.  
It showcases the ability to transform raw retail data into structured insights that support data-driven decision-making.


