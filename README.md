# ⚡ Zepto Quick-Commerce — SQL Analytics Project

<div align="center">

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Data%20Analytics-FF6B6B?style=for-the-badge&logo=databricks&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-00C853?style=for-the-badge)
![Domain](https://img.shields.io/badge/Domain-E--Commerce-5B2D8E?style=for-the-badge)

**An end-to-end SQL project on Zepto's product catalogue — exploring, cleaning, and analysing quick-commerce data using PostgreSQL.**

</div>

---

## 📌 Project Overview

Zepto is India's leading **10-minute grocery delivery** platform. This project uses PostgreSQL to perform a full analytics pipeline on Zepto's product SKU data — from schema design and raw data ingestion through data cleaning, exploration, and eight targeted business queries.

> **Goal:** Derive actionable business insights on pricing, inventory health, revenue potential, and product value from raw catalogue data.

---

## 🗂️ Table of Contents

- [Database Schema](#-database-schema)
- [Data Exploration](#-data-exploration)
- [Data Cleaning](#-data-cleaning)
- [Business Analysis](#-business-analysis--8-queries)
- [Key Insights](#-key-insights)
- [Tech Stack](#-tech-stack)

---

## 🏗️ Database Schema

```sql
DROP TABLE IF EXISTS zepto;

CREATE TABLE zepto (
    sku_id                 SERIAL PRIMARY KEY,
    category               VARCHAR(120),
    name                   VARCHAR(150) NOT NULL,
    mrp                    NUMERIC(8,2),
    discountPercent        NUMERIC(5,2),
    availableQuantity      INTEGER,
    discountedSellingPrice NUMERIC(8,2),
    weightInGms            INTEGER,
    outOfStock             BOOLEAN,
    quantity               INTEGER
);
```

| Column | Type | Description |
|--------|------|-------------|
| `sku_id` | SERIAL PK | Unique SKU identifier |
| `category` | VARCHAR(120) | Product category |
| `name` | VARCHAR(150) | Product name |
| `mrp` | NUMERIC(8,2) | Maximum Retail Price (₹) |
| `discountPercent` | NUMERIC(5,2) | Discount offered (%) |
| `availableQuantity` | INTEGER | Units in stock |
| `discountedSellingPrice` | NUMERIC(8,2) | Actual selling price (₹) |
| `weightInGms` | INTEGER | Product weight in grams |
| `outOfStock` | BOOLEAN | Stock availability flag |
| `quantity` | INTEGER | Pack quantity |

---

## 🔍 Data Exploration

Thorough exploration was done before any analysis to understand the dataset's shape and quality.

```sql
-- Total row count
SELECT COUNT(*) FROM zepto;

-- Sample records
SELECT * FROM zepto LIMIT 10;

-- NULL audit across all columns
SELECT * FROM zepto
WHERE name IS NULL OR category IS NULL OR mrp IS NULL
   OR discountPercent IS NULL OR discountedSellingPrice IS NULL
   OR weightInGms IS NULL OR availableQuantity IS NULL
   OR outOfStock IS NULL OR quantity IS NULL;

-- Unique product categories
SELECT DISTINCT category FROM zepto ORDER BY category;

-- Stock status split
SELECT outOfStock, COUNT(sku_id)
FROM zepto
GROUP BY outOfStock;

-- Products with multiple SKUs (different pack sizes)
SELECT name, COUNT(sku_id) AS "Number of SKUs"
FROM zepto
GROUP BY name
HAVING COUNT(sku_id) > 1
ORDER BY COUNT(sku_id) DESC;
```

---

## 🧹 Data Cleaning

Two critical issues were caught and fixed before analysis:

### Issue 1 — Zero-Price Records
```sql
-- Identify bad records
SELECT * FROM zepto WHERE mrp = 0 OR discountedSellingPrice = 0;

-- Remove corrupted entries
DELETE FROM zepto WHERE mrp = 0;
```

### Issue 2 — Prices in Paise, Not Rupees ⚠️
> The raw data stored prices in **paise** (1 ₹ = 100 paise). Without this fix, every revenue figure would be **inflated by 100×**.

```sql
UPDATE zepto
SET mrp                    = mrp / 100.0,
    discountedSellingPrice = discountedSellingPrice / 100.0;

-- Verify
SELECT mrp, discountedSellingPrice FROM zepto LIMIT 5;
```

---

## 📊 Business Analysis — 8 Queries

### Q1 · Top 10 Best-Value Products by Discount

```sql
SELECT DISTINCT name, mrp, discountPercent
FROM zepto
ORDER BY discountPercent DESC
LIMIT 10;
```
💡 *Surfaces the highest-discounted products — ideal for promotional campaigns and flash sale planning.*

---

### Q2 · High-MRP Products Currently Out of Stock

```sql
SELECT DISTINCT name, mrp
FROM zepto
WHERE outOfStock = TRUE AND mrp > 300
ORDER BY mrp DESC;
```
💡 *Premium OOS products represent immediate revenue loss and customer dissatisfaction risk.*

---

### Q3 · Estimated Revenue by Category

```sql
SELECT category,
       SUM(discountedSellingPrice * availableQuantity) AS total_revenue
FROM zepto
GROUP BY category
ORDER BY total_revenue DESC;
```
💡 *Reveals which categories drive the most potential revenue — critical for inventory prioritisation.*

---

### Q4 · Premium Products with Low Discounts

```sql
SELECT DISTINCT name, mrp, discountPercent
FROM zepto
WHERE mrp > 500 AND discountPercent < 10
ORDER BY mrp DESC, discountPercent DESC;
```
💡 *High-MRP items with minimal discounts may need competitive repricing or bundling strategies.*

---

### Q5 · Top 5 Categories by Average Discount

```sql
SELECT category,
       ROUND(AVG(discountPercent), 2) AS avg_discount
FROM zepto
GROUP BY category
ORDER BY avg_discount DESC
LIMIT 5;
```
💡 *Identifies aggressively discounted categories — signals margin pressure or clearing activity.*

---

### Q6 · Best Price-Per-Gram Value (Products ≥ 100g)

```sql
SELECT DISTINCT name, weightInGms, discountedSellingPrice,
       ROUND(discountedSellingPrice / weightInGms, 2) AS price_per_gram
FROM zepto
WHERE weightInGms >= 100
ORDER BY price_per_gram ASC;
```
💡 *Helps surface genuinely good deals for value-conscious customers and marketing highlights.*

---

### Q7 · Weight-Based Product Segmentation

```sql
SELECT DISTINCT name, weightInGms,
       CASE WHEN weightInGms < 1000 THEN 'Low'
            WHEN weightInGms < 5000 THEN 'Medium'
            ELSE 'Bulk'
       END AS weight_category
FROM zepto;
```
💡 *Bulk items require different handling, storage, and delivery slot allocation — key for logistics.*

---

### Q8 · Total Inventory Weight Per Category

```sql
SELECT category,
       SUM(weightInGms * availableQuantity) AS total_weight
FROM zepto
GROUP BY category
ORDER BY total_weight DESC;
```
💡 *Guides warehouse capacity planning, cold-chain allocation, and shelving decisions.*

---

## 📸 Project Screenshots

> SQL queries from the project in pgAdmin:

![Data Analysis Queries — Part 1](Screenshot_2026-05-29_125954.png)
*Queries Q1–Q4: Discount analysis, out-of-stock products, revenue estimation, pricing gaps*

![Data Analysis Queries — Part 2](Screenshot_2026-05-29_130009.png)
*Queries Q5–Q8: Category discounts, price-per-gram, weight segmentation, inventory weight*

---

## 💡 Key Insights

| Area | Finding |
|------|---------|
| 🧹 Data Quality | Zero-price records + paise→rupee conversion were critical fixes |
| 📦 Inventory Risk | Several ₹300+ products are OOS — direct revenue leakage |
| 💰 Revenue Focus | Category-level aggregation enables targeted restocking decisions |
| 🏷️ Pricing Strategy | Some high-MRP products carry <10% discount — room for adjustment |
| 🚚 Logistics | Weight segmentation supports efficient last-mile delivery planning |
| 🛒 Customer Value | Price-per-gram metric surfaces genuinely good deals |
| 📣 Promotions | Top-discounted products ideal for flash sales & featured placements |
| 🏭 Warehouse | Total inventory weight per category guides storage planning |

---

## 🛠️ Tech Stack

| Tool | Usage |
|------|-------|
| **PostgreSQL 16** | Primary database engine |
| **pgAdmin 4** | SQL IDE & query execution |
| **SQL** | DDL (schema), DML (cleaning), DQL (analysis) |

**SQL Concepts Used:**
`SERIAL PRIMARY KEY` · `NUMERIC` types · `BOOLEAN` flags · `GROUP BY` · `HAVING` · `CASE WHEN` · `DISTINCT` · `ORDER BY` · `UPDATE` · `DELETE` · Aggregate functions (`SUM`, `AVG`, `COUNT`, `ROUND`)

---

## 📁 Repository Structure

```
zepto-sql-analytics/
│
├── schema.sql          # Table creation script
├── exploration.sql     # Data exploration queries
├── cleaning.sql        # Data cleaning queries
├── analysis.sql        # All 8 business analysis queries
└── README.md           # This file
```

---

## 🤝 Connect

If you found this project helpful or have feedback, feel free to ⭐ the repo and connect!

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=for-the-badge&logo=linkedin)](https://linkedin.com)

---

<div align="center">
<sub>Built with ❤️ using PostgreSQL · Quick-Commerce Analytics Portfolio Project</sub>
</div>
