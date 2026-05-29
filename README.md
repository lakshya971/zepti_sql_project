⚡ Just completed a SQL Analytics Project on Zepto's Product Catalogue!

Zepto delivers groceries in 10 minutes — but what does their product data actually look like under the hood? I built an end-to-end PostgreSQL project to find out. 🛒📊

---

🗃️ **What I built:**
A complete analytics pipeline on Zepto's product dataset — from raw table design all the way to business insights.

---

🔧 **Phase 1 — Schema Design & Data Ingestion**
Designed a normalized PostgreSQL table with 10 columns capturing SKU ID, category, MRP, discount %, discounted price, weight, stock status, and quantity.

---

🔍 **Phase 2 — Data Exploration**
→ Counted total rows & inspected sample records
→ Audited ALL columns for NULL values
→ Found products listed under multiple SKUs (different pack sizes)
→ Analysed in-stock vs out-of-stock split across the catalogue

---

🧹 **Phase 3 — Data Cleaning**
Caught two major issues that would have broken every metric:
✅ Deleted zero-price records (corrupted entries)
✅ Converted prices from **paise → ₹** (raw data stored values ×100!)

Without fixing this, every revenue figure would be inflated by 100× 😬

---

📈 **Phase 4 — Business Analysis (8 Queries)**

1️⃣ Top 10 highest-discounted products
2️⃣ Premium products (MRP > ₹300) that are out of stock
3️⃣ Estimated revenue by category
4️⃣ High-MRP items with <10% discount (pricing opportunity?)
5️⃣ Top 5 categories by average discount
6️⃣ Best price-per-gram value for products ≥ 100g
7️⃣ Weight-based segmentation → Low / Medium / Bulk
8️⃣ Total inventory weight per category (warehouse planning)

---

💡 **Key Insights:**
→ Several ₹300+ products are OOS — direct revenue loss
→ Some premium SKUs have barely any discount headroom
→ Weight segmentation is crucial for last-mile delivery logistics
→ Category-level revenue helps prioritise restocking decisions

---

🛠️ **Tech Stack:**
PostgreSQL 16 · pgAdmin 4 · SQL (DDL + DML + DQL)
Concepts: Aggregations, CASE WHEN, HAVING, Subqueries, UPDATE/DELETE

---

This was a really fun project that shows how even "simple" SQL can surface genuinely useful business intelligence for a real-world platform.

Drop a comment if you've worked on e-commerce data projects — would love to connect! 🙌

#SQL #PostgreSQL #DataAnalytics #DataEngineering #EcommerceAnalytics #QuickCommerce #Zepto #PortfolioProject #DataScience #BusinessIntelligence #SQL #100DaysOfCode
