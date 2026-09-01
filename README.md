# Sales Analytics SQL Project

An end-to-end SQL data cleaning and analysis project built on a synthetic e-commerce dataset with three related tables: **customers**, **products**, and **sales**. The dataset was intentionally seeded with missing values and duplicate rows to practice real-world data cleaning, then analyzed using joins, subqueries, CTEs, and window functions.

## Overview

- **Tool used:** MySQL Workbench / MySQL via Jupyter Notebook (`ipython-sql`)
- **Tables:** `customers`, `products`, `sales` (3,210+ combined rows)
- **Goal:** Clean a messy relational dataset and answer business questions about revenue, customers, and products using SQL

## Dataset

| Table | Description |
|---|---|
| `customers` | customer_id, customer_name, gender, age, email, phone, city, country, segment, signup_date |
| `products` | product_id, product_name, category, supplier, unit_price, stock_qty |
| `sales` | order_id, customer_id, product_id, order_date, ship_date, region, ship_mode, quantity, unit_price, discount, sales_amount |

The dataset is synthetic (generated for practice) and was deliberately seeded with:
- Missing values across multiple columns (`signup_date`, `country`, `stock_qty`, `ship_mode`, etc.)
- Duplicate rows in `customers`, `products`, and `sales`
- Orphan foreign key references between `sales` → `customers` and `sales` → `products`
- Dates stored as text in `dd-mm-yyyy` format

## Data Cleaning Process

1. **Explored the schema** — checked table structure and row counts before touching anything (3,210 total rows across all tables at the start).
2. **Standardized blank strings to NULL** — some missing values were stored as empty strings (`''`) rather than true `NULL`, which would have caused them to be missed by `IS NULL` checks. Converted these first (e.g. `signup_date`, `stock_qty`, `ship_mode`).
3. **Audited nulls column-by-column** using conditional `SUM()` across every column in each table, e.g.:
   ```sql
   SELECT
   SUM(customer_id IS NULL) AS null_id,
   SUM(gender IS NULL) AS null_gender,
   SUM(country IS NULL) AS null_country,
   ...
   FROM customers;
   ```
4. **Filled nulls with meaningful defaults** — e.g. missing `country` and `supplier` values were set to `"Unknown"` rather than dropped, since deleting rows would lose otherwise-valid sales/customer data.
5. **Detected duplicate rows** in all three tables using `GROUP BY ... HAVING COUNT(*) > 1` on the natural key of each table.
6. **Checked referential integrity** between `sales` and `customers`/`products` using a `LEFT JOIN ... WHERE <parent_key> IS NULL` pattern to catch orphan sales records.
7. **Removed duplicates safely** using `ROW_NUMBER()` partitioned over each table's identifying columns, keeping only the first occurrence (`rn = 1`) and deleting the rest.
8. **Converted text dates to real dates** using `STR_TO_DATE(order_date, '%d-%m-%Y')` wherever date arithmetic or formatting was needed (daily/monthly trend queries).

## SQL Techniques Used

| Technique | Where it's used |
|---|---|
| **Joins** | Revenue by country, category, and customer segment (`sales` joined to `customers`/`products`) |
| **Subqueries** | Nested `GROUP BY` aggregates used inside outer `SELECT`s for ranking and totals |
| **CTEs (`WITH`)** | Product revenue ranking, high-value customer ranking, top product per region, daily/monthly revenue trends |
| **Window functions** | `ROW_NUMBER()` for de-duplication, `DENSE_RANK()` for top-N rankings (products, customers, per-region bestsellers), `LAG()` for month-over-month revenue change, running totals with `SUM() OVER (ORDER BY ...)` |

## Key Insights

- **Revenue by segment:** Consumer segment generated the highest revenue (~$2.30M), followed by Corporate (~$1.12M) and Home Office (~$685K).
- **Revenue by category:** Groceries led all categories (~$935K), narrowly ahead of Electronics (~$884K).
- **Top country by revenue:** USA led with ~$850K in revenue across 590 orders, followed by India (~$768K, 529 orders).
- **Top customer:** Brian Miller was the highest-value customer at ~$22.2K in lifetime spend.
- **Regional bestsellers:** Top-selling products varied by region — e.g. Olive Oil (Variant 3) led Asia Pacific, while Printer Paper led North America — showing category preference isn't uniform globally.
- **Monthly trend:** Revenue fluctuated month to month (e.g. a ~$15.9K drop from January to February 2022), tracked using `LAG()` to compute period-over-period change.

## How to Run This Project

1. Import `customers.csv`, `products.csv`, and `sales.csv` into a MySQL database (via MySQL Workbench's Table Data Import Wizard, or `LOAD DATA INFILE`).
2. Open `sqlsalesanalytics.ipynb` in Jupyter and run:
   ```
   %load_ext sql
   %sql mysql+mysqlconnector://<user>:<password>@localhost/<database>
   ```
3. Run the cells in order — cleaning steps first, followed by the analysis queries.

## Project Structure

```
├── data/
│   ├── customers.csv
│   ├── products.csv
│   └── sales.csv
├── sqlsalesanalytics.ipynb   # full cleaning + analysis notebook
└── README.md
```

## Tools

- MySQL / MySQL Workbench
- Jupyter Notebook with `ipython-sql`
- Python (via `%%sql` magic for query execution)

## Notes

This project uses synthetic data generated for practice purposes, with data quality issues (nulls, duplicates, format inconsistencies) introduced intentionally to demonstrate a full data-cleaning workflow from raw import to analysis-ready tables.
