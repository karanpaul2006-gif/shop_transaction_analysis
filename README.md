
# 🛍️ Retail Shop Point-of-Sale (POS) & Transactional Analytics

An end-to-end data analytics project designed to evaluate operational performance, customer payment channel preferences, and high-value customer segments using a real-world retail store transaction dataset. This project leverages **Python (Pandas, SQLite)** and **Intermediate SQL (CTEs, Window Functions, and Aggregate Groupings)** to extract actionable business insights for retail management.

---

## 📂 Project Structure

```text
shop_transaction_analysis/
│
├── retail_pos_analysis.ipynb    # Main Jupyter Notebook containing SQLite setup & intermediate SQL queries
├── shop_dataset.csv             # Operational Point-of-Sale (POS) transaction dataset (2026)
└── README.md                    # Project documentation & execution guide
🛠️ Tech Stack & Tools
Database & Querying: SQLite, SQL (CTEs, Window Functions, GROUP BY, Subqueries)

Programming & Data Manipulation: Python, Pandas, NumPy

Environment: GitHub Codespaces / Jupyter Notebook

📊 Analytical Pipeline & SQL Implementation
1. In-Memory Database Architecture (SQLite & Python)
Loaded raw operational Point-of-Sale records into an isolated, in-memory SQLite database connection (sqlite3) via Pandas to simulate a relational data warehouse environment.

2. Payment Method Performance & Channel Insights
Business Objective: Analyze sales volume and average transaction value (ATV) across different payment types (Cash, Card, UPI, Net Banking) to optimize checkout workflows and processor fee allocations.

SQL Techniques Used: Aggregate functions (COUNT, SUM, AVG), GROUP BY, and descending sorting (ORDER BY).

3. Monthly Revenue Trends & Seasonality
Business Objective: Track revenue fluctuations month-over-month across 2026 to evaluate performance trends and plan store inventory.

SQL Techniques Used: String extraction (SUBSTR), conditional grouping, and chronological sorting.

4. High-Value Customer Segmentation (CTEs & Window Functions)
Business Objective: Identify and rank top-tier customers based on cumulative lifetime spending to support targeted loyalty programs.

SQL Techniques Used:

Common Table Expressions (CTEs): Encapsulated customer-level aggregated metrics before ranking.

Window Functions (DENSE_RANK()): Partitioned and ranked customers cleanly without collapsing the underlying dataset rows.
