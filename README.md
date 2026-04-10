# Ecommerce Sales Performance Analysis | BigQuery + Tableau

Analyzing ecommerce performance to identify revenue drivers, product trends, and customer experience insights.

---

## Project Overview

This project analyzes ecommerce marketplace data to evaluate business performance across products, categories, and time.

The analysis focuses on identifying revenue trends, top-performing products, and areas for growth or improvement.

The analysis was performed using **BigQuery (SQL)** for data processing and **Tableau Public** for visualization.

---

## Tableau Dashboard
[View the interactive Tableau dashboard](https://public.tableau.com/views/Portfolio-asj/Dashboard1?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

---

## Dashboard Preview

<img width="3834" height="2074" alt="image" src="https://github.com/user-attachments/assets/9b338d24-a267-4873-9c2d-acafcee0e61d" />

---

## Project Goals

The main goals of this project were to:

- Analyze ecommerce performance using SQL-based KPI development  
- Identify product and category-level revenue drivers  
- Evaluate monthly revenue trends and seasonality  
- Measure product growth using month-over-month analysis  
- Identify high-revenue, low-rating opportunities  
- Present findings in an interactive dashboard for stakeholders  

---

## Tools Used

- **BigQuery** — data querying and transformation  
- **SQL** — aggregations, window functions, ranking  
- **Tableau Public** — dashboard design and visualization  

---

## Key Metrics

The project focuses on the following metrics:

- Total revenue  
- Total orders  
- Items sold  
- Average order value (AOV)  
- Average review score  
- Average delivery time  
- Month-over-month revenue growth  

---

## Key Analysis Areas

This project covers:

- **Overall ecommerce performance summary**  
- **Monthly revenue trend analysis**  
- **Category-level performance breakdown**  
- **Product-level ranking and performance**  
- **Month-over-month growth analysis**  
- **Customer experience insights (reviews & delivery)**  

---

## Main Insights

Some of the key findings from the analysis include:

- **Revenue shows strong upward growth**, with noticeable seasonal spikes  
- **A small number of categories drive a large share of revenue**, indicating concentration  
- **Top products contribute disproportionately to total sales**, highlighting key revenue drivers  
- **High-revenue products with lower review scores** present optimization opportunities  
- **Product performance varies significantly month-over-month**, indicating fluctuating demand  

---

## SQL Queries

### 1) Product Month-over-Month Growth

This query calculates monthly revenue per product and measures growth compared to the prior month.

```sql
WITH A AS (
    SELECT
        product_id AS Products,
        purchase_year,
        purchase_month,
        ROUND(SUM(revenue), 2) AS Monthly_Revenue
    FROM `project-4e22fa79-fabf-4341-936.ecommerce.fact_ecommerce_order_items`
    GROUP BY product_id, purchase_year, purchase_month
)

SELECT
    Products,
    purchase_year,
    purchase_month,
    ROUND(Monthly_Revenue, 2) AS Monthly_Revenue,
    ROUND(NULLIF(LAG(Monthly_Revenue) OVER (PARTITION BY Products ORDER BY purchase_year, purchase_month), 0), 2) AS Prior_Month_Revenue,
    ROUND(Monthly_Revenue - NULLIF(LAG(Monthly_Revenue) OVER (PARTITION BY Products ORDER BY purchase_year, purchase_month), 0), 2) AS MoM_Change,
    CONCAT(
        ROUND(
            (Monthly_Revenue - NULLIF(LAG(Monthly_Revenue) OVER (PARTITION BY Products ORDER BY purchase_year, purchase_month), 0))
            / NULLIF(LAG(Monthly_Revenue) OVER (PARTITION BY Products ORDER BY purchase_year, purchase_month), 0) * 100,
            2
        ),
        ' %'
    ) AS MoM_Change_Pct
FROM A
ORDER BY Products, purchase_year, purchase_month;\
```
### 2) Product Ranking by Revenue

This query ranks products based on total revenue and includes supporting sales and review metrics.

```sql
WITH A AS (
    SELECT
        product_id,
        product_category_english AS Category,
        ROUND(SUM(revenue), 2) AS Total_Revenue,
        COUNT(*) AS Total_Items_Sold,
        COUNT(DISTINCT order_id) AS Total_Orders,
        ROUND(AVG(review_score), 1) AS Average_Review_Rating
    FROM `project-4e22fa79-fabf-4341-936.ecommerce.fact_ecommerce_order_items`
    GROUP BY product_id, Category
)

SELECT
    product_id,
    Category,
    Total_Revenue,
    Total_Items_Sold,
    Total_Orders,
    Average_Review_Rating,
    RANK() OVER (ORDER BY Total_Revenue DESC) AS Rank
FROM A;
```
## Key Takeaways

This analysis highlights that ecommerce performance is driven by a combination of **category concentration and consistent product-level performance**, with clear opportunities for optimization and growth.

- **Revenue is concentrated in a small number of categories**, with Health & Beauty leading (~$1.4M+), followed by Watches/Gifts and Bed/Bath/Table (~$1.2M each)  
- **Total revenue reached ~$15.4M across ~96K orders**, with an average order value of ~$160, indicating steady transaction size  
- **Revenue trends show consistent growth through 2017 with a peak in late Q4**, suggesting strong seasonal demand patterns  
- **Top-performing products generate ~$47K–$67K each**, indicating revenue is distributed across multiple strong contributors rather than a single dominant product  
- **Customer satisfaction remains stable across categories**, with review scores tightly clustered (~4.0–4.2)  

### Strategic Implications

- Focus on **scaling top-performing categories** to maximize revenue impact  
- Improve **high-revenue, lower-rated products** to increase conversion and customer satisfaction  
- Leverage **seasonal peaks (Q4)** for targeted promotions and inventory planning  
- Expand **mid-tier categories with strong ratings** as potential growth opportunities  
