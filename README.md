# Retail Shop POS & Transactional Analytics

**A SQL-driven analysis of retail transactions using Python, Pandas and SQLite to examine payment channel performance, monthly sales trends and high-value transaction records.**

![Python](https://img.shields.io/badge/Python-3.x-blue)
![SQL](https://img.shields.io/badge/SQL-SQLite-lightgrey)
![Pandas](https://img.shields.io/badge/Library-Pandas-purple)
![Jupyter](https://img.shields.io/badge/Environment-Jupyter-orange)

---

## 1. Project Overview

Retail businesses generate transactional data that can help management understand sales performance, customer purchasing patterns and payment preferences.

This project analyses a 50-record Point-of-Sale (POS) transaction sample using Python, Pandas, SQLite and SQL.

The objective is to demonstrate how transactional data can be converted into structured business information through SQL aggregations, Common Table Expressions (CTEs) and window functions.

The analysis focuses on three areas:

- **Payment Channel Analysis:** Comparing transaction volume, recorded revenue and average transaction value across payment methods.
- **Monthly Sales Analysis:** Examining monthly transaction volume and revenue patterns.
- **Customer Transaction Ranking:** Using CTEs and DENSE_RANK() to identify records associated with the highest individual spending amounts.

The project is designed as a practical demonstration of SQL-based retail analytics and business reporting.

## 2. Business Problem

Retail managers need visibility into sales performance to understand how customers transact and how revenue is distributed across different payment channels and time periods.

This analysis explores the following business questions:

1. Which payment methods account for the highest transaction volume?
2. Which payment channels generate the highest recorded sales value?
3. Which payment methods have the highest average transaction value?
4. How does monthly transaction volume change over the observation period?
5. Which months record the highest and lowest sales values?
6. Which customer records have the highest associated transaction amounts?

These questions provide a starting point for evaluating payment-channel performance and identifying sales patterns that may require further investigation.

## 3. Dataset Description

**Dataset:** `shop_dataset.csv`

**Records:** 50

**Observation Period:** January–June 2026

The repository contains a small POS-style transaction dataset with the following fields:

| Column | Description |
|---|---|
| CustomerID | Unique customer identifier |
| Name | Customer name recorded in the dataset |
| Age | Customer age |
| Sex | Customer demographic attribute |
| Transaction_Type | Payment method used |
| Date | Transaction date in DD-MM-YYYY format |
| Product_ID | Associated product identifier |
| Price | Recorded transaction amount |

### Data Considerations

The dataset contains 50 records, with each CustomerID appearing once.

The repository does not currently document an external dataset source or explicitly specify the currency associated with Price. Therefore, financial values in this analysis are reported in the dataset's original price units.

The dataset is used as a small analytical sample rather than a representative estimate of an entire retail business.

## 4. Technology Stack

| Technology | Application |
|---|---|
| Python | Data loading and execution |
| Pandas | CSV ingestion and result handling |
| SQLite | In-memory relational database |
| SQL | Transactional analysis and aggregations |
| Common Table Expressions | Structured customer-level aggregation |
| DENSE_RANK() | Ranking transaction records |
| Jupyter Notebook | Analysis environment |

## 5. Analytical Workflow

### Step 1: Data Loading and Database Creation

The CSV file is imported into Python using Pandas.

An SQLite in-memory database is created using Python's sqlite3 library.

The transaction dataset is loaded into a relational table named `pos_transactions`.

```python
import pandas as pd
import sqlite3

# Load dataset
df = pd.read_csv('shop_dataset.csv')

# Create in-memory SQLite database
conn = sqlite3.connect(':memory:')

# Load transactions into SQLite
df.to_sql(
    'pos_transactions',
    conn,
    index=False
)
```

This approach allows SQL queries to be executed directly against the imported transaction data without requiring a separately configured database server.

### Step 2: Payment Method Performance Analysis

**Business Objective:**

Evaluate transaction volume, total recorded sales and average transaction value across different payment methods.

The analysis compares:

- Cash
- Card
- UPI
- Net Banking

**SQL Concepts Used:**

- COUNT()
- SUM()
- AVG()
- GROUP BY
- ORDER BY

**SQL Implementation:**

```sql
SELECT
    Transaction_Type,
    COUNT(CustomerID) AS Total_Transactions,
    SUM(Price) AS Total_Revenue,
    ROUND(AVG(Price), 2) AS Average_Transaction_Value

FROM pos_transactions

GROUP BY Transaction_Type

ORDER BY Total_Revenue DESC;
```

The query aggregates transactions by payment method and ranks payment channels according to total recorded revenue.

### Step 3: Monthly Revenue and Transaction Analysis

**Business Objective:**

Understand monthly sales performance and identify differences in transaction activity across the observation period.

The analysis extracts the month from the original DD-MM-YYYY date format and calculates:

- Monthly transaction volume.
- Monthly recorded revenue.

**SQL Concepts Used:**

- SUBSTR()
- COUNT()
- SUM()
- GROUP BY
- ORDER BY

**SQL Implementation:**

```sql
SELECT
    SUBSTR(Date, 4, 2) AS Month,
    COUNT(CustomerID) AS Monthly_Orders,
    SUM(Price) AS Monthly_Revenue

FROM pos_transactions

GROUP BY Month

ORDER BY Month ASC;
```

This provides a monthly summary of sales activity and helps identify periods with relatively higher or lower transaction volumes.

### Step 4: High-Value Transaction Ranking Using CTEs and Window Functions

**Business Objective:**

Identify the five highest recorded spending amounts associated with customer records.

The analysis uses a Common Table Expression to aggregate transaction amounts by customer identifier and then applies DENSE_RANK() to rank the results.

**SQL Concepts Used:**

- WITH clause
- Common Table Expressions
- SUM()
- COUNT()
- GROUP BY
- DENSE_RANK()
- Window Functions

**SQL Implementation:**

```sql
WITH CustomerSpending AS (

    SELECT
        CustomerID,
        Name,
        Sex,
        Age,
        SUM(Price) AS Total_Spent,
        COUNT(Product_ID) AS Items_Bought

    FROM pos_transactions

    GROUP BY
        CustomerID,
        Name,
        Sex,
        Age
),

RankedCustomers AS (

    SELECT
        Name,
        Sex,
        Age,
        Total_Spent,

        DENSE_RANK() OVER (
            ORDER BY Total_Spent DESC
        ) AS Spending_Rank

    FROM CustomerSpending
)

SELECT *
FROM RankedCustomers
WHERE Spending_Rank <= 5;
```

**Important Interpretation Note:**

Since every CustomerID appears only once in the current dataset, the ranking represents individual recorded transaction amounts rather than cumulative lifetime customer spending.

A dataset containing repeat transactions for the same CustomerID would be required to analyse genuine customer-level spending patterns and repeat purchasing behaviour.

## 6. Key Analytical Results

### A. Payment Method Performance

The analysis produced the following results:

| Payment Method | Transactions | Total Revenue | Average Transaction Value |
|---|---:|---:|---:|
| Card | 14 | 8,095 | 578.21 |
| Cash | 16 | 7,509 | 469.31 |
| Net Banking | 11 | 5,249 | 477.18 |
| UPI | 9 | 4,717 | 524.11 |
| **Total** | **50** | **25,570** | **511.40** |

**Key Observations:**

- Cash recorded the highest transaction volume, accounting for 16 of the 50 transactions.
- Card payments generated the highest total recorded revenue of 8,095.
- Card payments also recorded the highest average transaction value of 578.21.
- UPI recorded an average transaction value of 524.11, higher than Cash and Net Banking.

These results indicate differences in payment-channel usage and transaction values within the sample.

### B. Monthly Sales Performance

| Month | Transactions | Monthly Revenue |
|---|---:|---:|
| January | 10 | 3,507 |
| February | 7 | 3,753 |
| March | 8 | 3,860 |
| April | 8 | 4,421 |
| May | 5 | 4,165 |
| June | 12 | 5,864 |
| **Total** | **50** | **25,570** |

**Key Observations:**

- June recorded the highest monthly revenue at 5,864.
- January recorded the lowest monthly revenue at 3,507.
- June also recorded the highest transaction volume, with 12 transactions.
- May recorded the lowest transaction volume, with 5 transactions.

The results show variation in monthly transaction activity. A longer observation period would be required to establish reliable seasonal patterns.

### C. Highest-Value Customer Records

The customer-ranking query identified the following five highest recorded amounts:

| Rank | Recorded Amount |
|---|---:|
| 1 | 1,247 |
| 2 | 1,230 |
| 3 | 1,200 |
| 4 | 1,194 |
| 5 | 1,188 |

These represent the highest transaction amounts associated with the customer records in the available sample.

They should not be interpreted as customer lifetime values because repeat transactions are not available for the same customer identifiers.

## 7. Business Interpretation and Management Considerations

The SQL results provide several observations that could be investigated further by retail management.

**1. Payment Channel Performance**

Card payments generated the highest total recorded revenue and average transaction value, whereas Cash accounted for the highest number of transactions.

Management could investigate payment-processing costs, customer preferences and transaction values before considering any changes to payment-channel strategy.

**2. Monthly Sales Performance**

June recorded the highest sales value and transaction volume in the sample.

Further investigation into promotions, product availability and customer demand could help explain this observation.

**3. Transaction Value Analysis**

The ranking identifies the five highest individual recorded transaction amounts.

With a larger dataset containing repeat customer identifiers, this analysis could be extended into customer segmentation and loyalty analysis.

These are exploratory observations rather than verified operational recommendations. The available sample does not establish the causes behind the observed differences.

## 8. Limitations

The current analysis has several limitations:

- The dataset contains only 50 transactions.
- External dataset provenance is not documented.
- Each CustomerID appears only once, preventing repeat-purchase and lifetime-value analysis.
- Product categories and product descriptions are unavailable.
- Quantity, unit cost, discounts, returns and profit margins are not included.
- Payment-processing fees are unavailable.
- The currency associated with Price is not explicitly documented.
- The analysis does not establish the causes of monthly sales fluctuations.

These limitations should be considered when interpreting the findings.

## 9. Repository Structure

```text
shop_transaction_analysis/
│
├── retail_pos_analysis.ipynb
│   └── Main Jupyter Notebook containing
│       Python, SQLite setup and SQL analysis
│
├── shop_dataset.csv
│   └── POS transaction dataset
│
└── README.md
    └── Project documentation
```

## 10. How to Run the Project

### Requirements

- Python 3.x
- Pandas
- Jupyter Notebook

SQLite is available through Python's built-in `sqlite3` library.

### Installation

Clone the repository:

```bash
git clone https://github.com/karanpaul2006-gif/shop_transaction_analysis.git
```

Navigate to the repository:

```bash
cd shop_transaction_analysis
```

Install the required packages:

```bash
pip install pandas jupyter
```

Launch Jupyter Notebook:

```bash
jupyter notebook
```

Open:

```text
retail_pos_analysis.ipynb
```

Execute the notebook cells sequentially to reproduce the SQL analysis and results.

**Note:** Run the notebook from the repository directory so that `shop_dataset.csv` can be located using its relative path.

## 11. Future Improvements

Potential extensions to this project include:

- Expanding the dataset with multiple transactions per customer.
- Implementing data-quality validation and duplicate checks.
- Introducing product category and inventory analysis.
- Analysing discounts, returns and profitability.
- Adding visualizations for payment-channel and monthly sales performance.
- Developing a concise management report with actionable findings.
- Extending the analysis to a larger transactional dataset.

## 12. Skills Demonstrated

**SQL:** CTEs, DENSE_RANK(), GROUP BY, aggregate functions, filtering and sorting.

**Python:** Pandas, SQLite integration and Jupyter Notebook.

**Analytics:** Payment-channel analysis, transaction-value analysis, monthly revenue analysis and ranking.

**Business Understanding:** Retail sales monitoring, payment-channel comparison and exploratory performance analysis.

---

**Author:** Karan Paul Arora

**GitHub:** https://github.com/karanpaul2006-gif
