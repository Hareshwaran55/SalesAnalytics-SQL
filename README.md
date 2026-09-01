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
