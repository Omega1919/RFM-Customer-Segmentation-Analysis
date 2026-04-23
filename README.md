# 📊 RFM Customer Segmentation Analysis

## 🧠 Project Overview

This project applies RFM (Recency, Frequency, Monetary) analysis to segment customers based on purchasing behavior. The goal is to identify valuable customer groups and support data-driven marketing and retention strategies.

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
- Assigns customers into business segments
- Creates a clean dataset for dashboarding

---

## 📊 Customer Count by Segment

```sql
SELECT segment, COUNT(*) AS total_customers
FROM rfm_segmented
GROUP BY segment
ORDER BY total_customers DESC;
```

### Insight
- Loyal Customers are typically the largest segment  
- Low Value Customers also appear in high volume  
- Top Customers are fewer but more valuable  

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
- Top Customers are valuable but not the largest revenue source  

---

## 🔍 Key Insights

- Revenue is spread across multiple customer groups  
- Loyal Customers form the core stable base  
- Growing Customers represent upsell opportunities  
- At Risk Customers need retention focus  
- Top Customers are high priority for loyalty programs  

---

## 🚀 Business Impact

- Customer segmentation strategy  
- Marketing targeting  
- Retention campaigns  
- Revenue optimization  
- Customer lifetime value improvement  

---

## 📌 Tools Used

- MySQL  
- Window Functions (NTILE)  
- CASE WHEN logic  
- GROUP BY aggregations
