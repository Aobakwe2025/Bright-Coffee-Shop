# Bright Coffee Shop — Sales Analysis
**BrightLearn | Case Study 1 | Due: 31 March 2026**

---

## Project Overview

A data analytics case study analysing daily transactional sales data from Bright Coffee Shop (Jan–Jun 2023, 3 locations, ~149k rows). The goal is to surface actionable insights for the new CEO to drive revenue growth and improve product performance.

---

## Architecture

```
CSV File  →  Databricks (ETL)  →  Delta Table  →  Excel (Analysis)  →  PowerPoint (CEO Deck)
~149k rows    PySpark pipeline     Versioned/ACID    Pivot + Charts       Key findings + recommendations
```

---

## Repository Structure

```
bright-coffee-shop/
├── README.md
├── data/
│   ├── raw/
│   │   └── bright_coffee_shop_sales.csv        # Source data (converted from Excel)
│   └── processed/
│       └── bright_coffee_shop_cleaned.xlsx     # Cleaned & transformed dataset with pivot tables
├── databricks/
│   └── etl_pipeline.sql                        # SQL transformations (time buckets, aggregations)
├── planning/
│   └── miro_architecture_diagram.[link/png]    # Data flow & architecture diagram
├── analysis/
│   └── bright_coffee_shop_analysis.xlsx        # Pivot tables + charts
└── presentation/
    ├── methodology.docx                         # Approach & methodology document
    └── ceo_deck.pptx                            # Final CEO presentation
```

---

## Data Pipeline

### Task 1 — Planning & Architecture (Miro)

- Data flow diagram: Source → ETL → Storage → Analysis → Presentation
- Key insights to deliver:
  - Sales by product category and 30-minute time intervals
  - High-performing vs low-performing products
  - Total revenue per store location

### Task 2 — Data Processing (Databricks / PySpark)

1. Convert source Excel file to CSV
2. Load CSV into Databricks
3. Transformations:
   - Fix `unit_price`: replace commas with decimals (`3,1` → `3.1`), cast to `FLOAT`
   - Compute `total_amount = unit_price * transaction_qty`
   - Create `transaction_time_bucket`: group timestamps into 30-minute intervals
   - SQL aggregations: group by product type, time bucket, store location

**Core SQL pattern:**
```sql
SELECT
  product_type,
  product_detail,
  FLOOR(MINUTE(transaction_time) / 30) * 30 AS time_bucket,
  SUM(CAST(REPLACE(unit_price, ',', '.') AS FLOAT) * transaction_qty) AS total_revenue,
  SUM(transaction_qty) AS units_sold
FROM coffee_sales
GROUP BY product_type, product_detail, time_bucket
ORDER BY total_revenue DESC;
```

### Task 3 — Analysis (Microsoft Excel)

Pivot tables and charts covering:
- Total revenue per product type
- Peak time intervals (30-minute buckets)
- Quantity sold by product category
- Best and worst performing products
- Revenue by store location

### Task 4 — Presentation (PowerPoint)

CEO deck structure:
1. Title slide + project overview
2. Methodology summary (link to methodology.docx)
3. Key finding: top revenue-generating products
4. Key finding: peak trading hours
5. Key finding: store-by-store comparison
6. Recommendations
7. Next steps

---

## Key Metrics & Calculations

| Metric | Formula |
|---|---|
| Total amount | `unit_price × transaction_qty` |
| Time bucket | Floor transaction time to nearest 30-minute window |
| Revenue by category | SUM of total_amount grouped by product_type |
| Units sold | SUM of transaction_qty grouped by product_detail |

---

## Recommendations (to be validated with data)

- Run marketing campaigns during identified slow time slots
- Prioritise stock for consistently best-selling items
- Create promotions targeting underperforming products

## Next Steps

- Automate daily sales reporting pipeline
- Extend tracking across multiple locations
- Implement loyalty programmes aligned to peak customer time slots

---

## Submission Checklist

- [ ] Miro plan / architecture diagram
- [ ] Processed dataset (Excel / Google Sheets) with pivot tables & charts
- [ ] PowerPoint CEO presentation
- [ ] SQL file with transformation code

---

*BrightLearn — Case Study 1, 2026*