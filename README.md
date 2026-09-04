# AtliQ Hardwares - Sales & Profitability Analytics

## Overview
An end-to-end data analytics project transforming raw transactional sales records into an interactive executive Power BI report. The analysis evaluates revenue drivers, profitability metrics, and regional performance variations for **AtliQ Hardwares**.

---

## Data Architecture
The data model follows a **Star Schema** centered around the `transactions` fact table:
* **Fact Table:** `sales transactions`
* **Dimension Tables:** `sales customers`, `sales products`, `sales markets`, `sales date`

---

## Tech Stack
* **Database Management:** MySQL Workbench
* **Data Transformation:** Power Query (ETL)
* **Visualization & Reporting:** Power BI
* **Analytics Languages:** SQL, DAX

---

## Key SQL Audit Queries
```sql
-- 1. Filter 2020 transactional records
SELECT transactions.*, date.* 
FROM transactions 
INNER JOIN date ON transactions.order_date = date.date 
WHERE date.year = 2020;

-- 2. Audit total revenue handling currency strings (INR\r & USD\r)
SELECT SUM(transactions.sales_amount) 
FROM transactions 
INNER JOIN date ON transactions.order_date = date.date 
WHERE date.year = 2020 
  AND (transactions.currency = "INR\r" OR transactions.currency = "USD\r");

-- 3. Regional market revenue audit
SELECT SUM(transactions.sales_amount) 
FROM transactions 
INNER JOIN date ON transactions.order_date = date.date 
WHERE date.year = 2020 
  AND transactions.market_code = "Mark001";
```
// Core Measures
Revenue = SUM('sales transactions'[sales_amount])

Sales Qty = SUM('sales transactions'[sales_qty])

Total Profit Margin = SUM('sales transactions'[profit_margin])

Profit Margin% = DIVIDE([Total Profit Margin], [Revenue], 0)

// Contribution Calculations
Revenue Contribution % = 
DIVIDE([Revenue], CALCULATE([Revenue], ALL('sales products'), ALL('sales customers'), ALL('sales markets')))

Profit Margin Contribution % = 
DIVIDE([Total Profit Margin], CALCULATE([Total Profit Margin], ALL('sales products'), ALL('sales customers'), ALL('sales markets')))

// Dynamic What-If Parameter & Target Variance
Profit Target = GENERATESERIES(-0.05, 0.15, 0.01)

Target Diff = [Profit Margin%] - 'Profit Target'[Profit Target Value]
