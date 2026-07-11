# 🛒 Instacart Market Basket Analysis

![Instacart](https://img.shields.io/badge/Instacart-Market%20Basket%20Analysis-003D29?style=for-the-badge&logo=data:image/png;base64,iVBORw0KGgo=)
![Databricks](https://img.shields.io/badge/Databricks-SQL-FF6B35?style=for-the-badge&logo=databricks&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F59E0B?style=for-the-badge&logo=powerbi&logoColor=white)
![Apache Spark](https://img.shields.io/badge/Apache%20Spark-SQL-E25A1C?style=for-the-badge&logo=apachespark&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta%20Lake-Tables-003D29?style=for-the-badge)
![Kaggle](https://img.shields.io/badge/Dataset-Kaggle-20BEFF?style=for-the-badge&logo=kaggle&logoColor=white)

> **Real-time insights into grocery ordering behavior, product reorder patterns, customer segmentation & peak demand analysis across 33M+ order records.**

---

## 📊 Dashboard Preview

![https://drive.google.com/file/d/1WcX2ssT20O8UU99tWSSL7AqibFLyl32c/view?usp=drive_link]
---

## 🎯 Project Overview

Instacart's grocery ordering dataset had no analytical layer — raw transactional data across 6 tables with no visibility into customer behavior, peak demand windows, product reorder patterns, or department-level performance.

This project builds a **complete SQL analysis pipeline** on Databricks and an **interactive Power BI dashboard** to surface actionable business insights from 33M+ rows of real grocery ordering data.

---

## 📁 Dataset

| Detail | Info |
|---|---|
| Source | [Kaggle — Instacart Market Basket Analysis](https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis) |
| Total Rows | 33,819,106 order-product records |
| Tables | 6 CSV files |
| Orders | 3,421,083 |
| Customers | 206,209 |
| Products | 49,687 |
| Departments | 21 |
| Aisles | 134 |

---

## 🗂️ Schema Design

```
                aisles ◄──── products ────► departments
                                  │
                                  ▼
orders ──────► order_products (FACT TABLE — 33.8M rows)
  │
  ▼
users (derived from orders)
```

### Tables

| Table | Rows | Type | Key Columns |
|---|---|---|---|
| `orders` | 3,421,083 | Fact | order_id, user_id, order_dow, order_hour_of_day, days_since_prior_order |
| `order_products` | 33,819,106 | Fact | order_id, product_id, add_to_cart_order, reordered |
| `products` | 49,687 | Dimension | product_id, product_name, aisle_id, department_id |
| `aisles` | 134 | Dimension | aisle_id, aisle |
| `departments` | 21 | Dimension | department_id, department |

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **Databricks Community Edition** | Cloud SQL environment |
| **Apache Spark SQL** | Query engine for 33M+ rows |
| **Unity Catalog Volumes** | CSV ingestion & storage |
| **Delta Lake** | Permanent table storage |
| **Power BI Desktop** | Interactive dashboard |
| **Kaggle** | Dataset source |

---

## ⚙️ Setup & How to Run

### Step 1 — Download Dataset
```
kaggle datasets download -d psparks/instacart-market-basket-analysis
```
Or download manually from [Kaggle](https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis)

### Step 2 — Upload to Databricks
```
Catalog → default → instacart_data Volume
Upload all 6 CSV files
```

### Step 3 — Create Delta Tables
```sql
CREATE TABLE workspace.instacart.orders
AS SELECT * FROM read_files(
  '/Volumes/workspace/default/instacart_data/orders.csv',
  format => 'csv', header => true, inferSchema => true
);

-- Repeat for products, aisles, departments,
-- order_products_prior, order_products_train

-- Merge fact table
CREATE TABLE workspace.instacart.order_products AS
SELECT * FROM workspace.instacart.order_products_prior
UNION ALL
SELECT * FROM workspace.instacart.order_products_train;
```

### Step 4 — Run Queries
Open `instacart.ipynb` in Databricks and run all cells sequentially.

### Step 5 — Power BI Dashboard
- Export query results as CSVs
- Open `instacart_dashboard.pbix` in Power BI Desktop
- Refresh data sources

---

## 📝 SQL Queries (33 Total)

### 🟢 Beginner — Aggregations & Filters (A1–A10)

| # | Question | Concepts |
|---|---|---|
| A1 | How many total orders exist? | COUNT |
| A2 | How many unique customers? | COUNT DISTINCT |
| A3 | How many unique products? | COUNT |
| A4 | Which day of week has most orders? | GROUP BY, ORDER BY |
| A5 | What are the peak ordering hours? | GROUP BY, LIMIT |
| A6 | How many products per department? | JOIN, GROUP BY |
| A7 | Top 10 most ordered products? | JOIN, ORDER BY |
| A8 | How many single-item orders? | Subquery, HAVING |
| A9 | Average items per order? | AVG, Subquery |
| A10 | Which aisle has most products? | JOIN, GROUP BY |

### 🟡 Intermediate — JOINs, CTEs, CASE WHEN (B1–B10)

| # | Question | Concepts |
|---|---|---|
| B1 | Total orders per department | 3-table JOIN |
| B2 | Reorder rate by department | SUM, AVG, JOIN |
| B3 | Overall reorder percentage | CASE WHEN |
| B4 | Customers with 10+ orders | HAVING, Subquery |
| B5 | Customer order segmentation | CTE, CASE WHEN |
| B6 | Avg days between orders by segment | CTE, AVG |
| B7 | Top 10 aisles by orders | 3-table JOIN |
| B8 | Cart position vs reorder rate | AVG, WHERE |
| B9 | Top 10 reordered products | HAVING, ORDER BY |
| B10 | Avg basket size per department | Nested Subquery |

### 🔴 Advanced — Window Functions, CTEs (C1–C8)

| # | Question | Concepts |
|---|---|---|
| C1 | Top 5 products per department | RANK() OVER PARTITION BY |
| C2 | Most popular product per day | ROW_NUMBER() |
| C3 | Customer RFM table | Multi-JOIN, AVG |
| C4 | High volume low reorder products | CTE, WHERE filter |
| C5 | Top 3 first-cart products per dept | RANK(), WHERE = 1 |
| C6 | Rolling 3-order basket size | ROWS BETWEEN, AVG OVER |
| C7 | Market basket co-purchased pairs | Self-JOIN |
| C8 | Loyal vs one-time preferences | CTE, CASE WHEN, pivot |

### 🟣 Set Operations (D1–D2)

| # | Question | Concepts |
|---|---|---|
| D1 | Products in prior but not train | EXCEPT |
| D2 | Common products in both sets | INTERSECT |

---

## 📈 Key Insights

| # | Insight |
|---|---|
| 🥕 | **Produce leads** with 29% of all 33M order lines — fresh fruits & vegetables are Instacart's backbone |
| 🔁 | **59% reorder rate** platform-wide — strong habitual purchase behavior across all categories |
| ⏰ | **10 AM – 3 PM on Saturdays** is the peak ordering window — critical for delivery capacity planning |
| 👥 | **57% of customers are Loyal** (16+ orders), reordering every ~10 days vs Occasional at ~16 days |
| 🍌 | **Banana is #1 every day** of the week — 491,291 total orders across the entire dataset |
| 🛒 | **Personal Care** has highest reorder rate at 65.2% — subscription-like buying behavior |
| 📍 | **Cart position matters** — items added first reordered at 65.4% vs 51.2% at position 10 |
| 🏪 | **163,593 single-item orders** (~4.8%) — customers doing quick top-up trips |

---

## 📊 Dashboard Features

| Feature | Detail |
|---|---|
| KPI Cards | Total Orders · Unique Customers · Total Products · Reorder Rate · Avg Basket Size · Avg Days Between Orders |
| Charts | 10 interactive visuals |
| Filters | Department · Day of Week · Customer Segment |
| Pages | Single-page layout |
| Theme | Instacart brand colors (#003D29, #FF6B35, #FAF3E8) |

### Visuals Included

- 📈 Orders by Hour of Day (Line chart)
- 📊 Orders by Day of Week (Bar chart)
- 🍩 Orders by Department (Donut chart)
- 🏆 Top 10 Most Ordered Products (Horizontal bar)
- 📉 Reorder Likelihood by Cart Position (Line chart)
- 💡 Business Insights (Text visual)
- 🧩 Customer Segments (Donut chart)
- 🌳 Top Reordered Products (Treemap)
- 📊 Days Between Purchases by Segment (Bar chart)

---

## 📂 Project Structure

```
instacart-market-basket-analysis/
│
├── 📓 instacart.ipynb          # All 33 SQL queries in Databricks notebook
├── 📊 instacart_dashboard.pbix # Power BI dashboard file
├── 🖼️ instacart_dashboard.png  # Dashboard screenshot
├── 📄 README.md                # This file
│
└── 📁 sql dataset instacart/                    # (not included — download from Kaggle)
    ├── orders.csv
    ├── products.csv
    ├── aisles.csv
    ├── departments.csv
    ├── order_products__prior.csv
    └── order_products__train.csv
```

---

## 🔢 Results Summary

```
Total Orders          →  3,421,083
Order-Product Rows    → 33,819,106
Unique Customers      →    206,209
Unique Products       →     49,687
Departments           →         21
Aisles                →        134
Overall Reorder Rate  →        59%
Avg Basket Size       →      10.11 items
Avg Days Between      →       11.1 days
Peak Hour             →     10 AM
Busiest Day           →   Saturday
#1 Product            →     Banana (491K orders)
#1 Reorder Dept       →   Personal Care (65.2%)
SQL Queries Written   →         33
Dashboard Visuals     →         10
```

---

## 👩‍💻 About

**Zainab Ansari**
B.E. Computer Science Engineering — Data Science Specialization
Lords Institute of Engineering and Technology, Hyderabad
CGPA: 9.19 | Graduated: May 2026

[![GitHub](https://img.shields.io/badge/GitHub-Xynab-181717?style=flat&logo=github)](https://github.com/Xynab)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Zainab%20Ansari-0077B5?style=flat&logo=linkedin)](https://linkedin.com/in/zainab-ansari-a52196252)
[![Email](https://img.shields.io/badge/Email-zainabansari8686%40gmail.com-D14836?style=flat&logo=gmail)](mailto:zainabansari8686@gmail.com)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

*⭐ If you found this project helpful, please give it a star!*
