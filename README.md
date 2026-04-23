# 📊 RFM Customer Segmentation Analysis

## 🧠 Project Overview

This project applies RFM (Recency, Frequency, Monetary) analysis to segment customers based on purchasing behavior. The goal is to identify valuable customer groups and support data-driven marketing and retention strategies.

---

## 📸 Dashboard Preview

![RFM Dashboard](RFM_ANALYSIS_DASHBOARD.pdf)

---

## 🔗 Live Dashboard

[View Live Dashboard](https://datastudio.google.com/reporting/d08eb808-3ba3-4776-92b6-a51bf3f57b12)


---

## 📌 RFM Concept

RFM analysis evaluates customers based on:

- **Recency (R):** How recently a customer purchased  
- **Frequency (F):** How often they purchase  
- **Monetary (M):** How much they spend  

These metrics are used to classify customers into business-driven segments such as:

- Top Customers  
- Loyal Customers  
- Growing Customers  
- At Risk Customers  
- Low Value Customers  

---

## 🛠️ SQL Implementation

## 📌 Step 1: RFM View Creation

```sql
CREATE OR REPLACE VIEW rfm_segmented AS
SELECT 
  customer_id,   -- explicitly included

  recency,
  frequency,
  monetary,

  NTILE(5) OVER (ORDER BY recency ASC) AS r_score,
  NTILE(5) OVER (ORDER BY frequency DESC) AS f_score,
  NTILE(5) OVER (ORDER BY monetary DESC) AS m_score,

  CONCAT(
    NTILE(5) OVER (ORDER BY recency ASC),
    NTILE(5) OVER (ORDER BY frequency DESC),
    NTILE(5) OVER (ORDER BY monetary DESC)
  ) AS rfm_score,

  CASE
    WHEN NTILE(5) OVER (ORDER BY recency ASC) >= 4 
     AND NTILE(5) OVER (ORDER BY frequency DESC) >= 4 
     AND NTILE(5) OVER (ORDER BY monetary DESC) >= 4 THEN 'Top Customers'

    WHEN NTILE(5) OVER (ORDER BY recency ASC) >= 3 
     AND NTILE(5) OVER (ORDER BY frequency DESC) >= 3 THEN 'Loyal Customers'

    WHEN NTILE(5) OVER (ORDER BY recency ASC) >= 3 
     AND NTILE(5) OVER (ORDER BY frequency DESC) <= 2 THEN 'Growing Customers'

    WHEN NTILE(5) OVER (ORDER BY recency ASC) <= 2 
     AND NTILE(5) OVER (ORDER BY frequency DESC) >= 3 THEN 'At Risk Customers'

    ELSE 'Low Value Customers'
  END AS segment

FROM rfm_table;
```

---

## 🧠 Explanation

This view:
- Calculates RFM scores using NTILE(5)
- Combines scores into a 3-digit RFM score
- Groups customers into business segments
- Prepares dataset for dashboard analysis

---

## 📊 Customer Count by Segment

```sql
SELECT segment, COUNT(*) AS total_customers
FROM rfm_segmented
GROUP BY segment
ORDER BY total_customers DESC;
```

### Insight
- Loyal Customers and Low Value Customers dominate in volume  
- Top Customers are fewer but high value  
- At Risk Customers need retention focus  

---

## 💰 Revenue by Segment

```sql
SELECT segment, SUM(monetary) AS total_revenue
FROM rfm_segmented
GROUP BY segment
ORDER BY total_revenue DESC;
```

### Insight
- Revenue is mainly driven by Low Value and Growing Customers  
- Loyal Customers provide stable revenue  
- Top Customers are not always highest revenue contributors  

---

## 🔍 Key Insights

- Customer base is mostly Loyal and Low Value segments  
- Growing Customers present upsell opportunities  
- At Risk Customers represent churn risk  
- Top Customers require loyalty programs  

---

## 🚀 Business Impact

- Customer segmentation strategy  
- Marketing targeting  
- Retention optimization  
- Revenue analysis  
- Customer lifetime value improvement  

---

## 📌 Tools Used

- MySQL  
- Window Functions (NTILE)  
- CASE WHEN logic  
- GROUP BY aggregations
