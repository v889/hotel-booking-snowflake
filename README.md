# 🏨 Hotel Booking Analytics — Snowflake Data Engineering Project

## 📌 Overview
End-to-end data engineering pipeline built on **Snowflake** implementing **Bronze–Silver–Gold Medallion Architecture** for hotel booking analytics and business intelligence. The project covers raw data ingestion via Snowflake Stages, production-grade data quality transformations, Gold-layer aggregations, and a **Snowflake Native Dashboard** for business insights.

---

## 🏗️ Architecture
---

## 📊 Dashboard Metrics

| Metric | Value |
|--------|-------|
| Total Guests | 5,072 |
| Average Booking Value | $334.11 |
| Data Range | Nov 2024 – May 2026 |
| Top Analytics | City-level Revenue, Daily Booking Trends |

![Dashboard](dashboard/dashboard_screenshot.png)

---

## 🛠️ Tech Stack

| Tool | Usage |
|------|-------|
| **Snowflake** | Database, Stages, File Format, Native Dashboard |
| **SQL** | DDL, DML, Data Quality Transformations |
| **Medallion Architecture** | Bronze / Silver / Gold layers |
| **Snowflake Stages** | CSV file ingestion |
| **COPY INTO** | Bulk data loading |
| **Snowflake Dashboard** | Business Intelligence & Visualization |

---

# 🏨 Hotel Booking Analytics — Snowflake Data Engineering Project

## 📌 Overview
End-to-end data engineering pipeline built on **Snowflake** implementing **Bronze–Silver–Gold Medallion Architecture** for hotel booking analytics and business intelligence. The project covers raw data ingestion via Snowflake Stages, production-grade data quality transformations, Gold-layer aggregations, and a **Snowflake Native Dashboard** for business insights.

---

## 🏗️ Architecture

```
CSV Data → Snowflake Stage → Bronze (Raw Ingestion) → Silver (Cleaned & Validated) → Gold (Aggregated) → Snowflake Dashboard
```

---

## 📊 Dashboard Metrics

| Metric | Value |
|--------|-------|
| Total Guests | 5,072 |
| Average Booking Value | $334.11 |
| Data Range | Nov 2024 – May 2026 |
| Top Analytics | City-level Revenue, Daily Booking Trends |

![Dashboard](dashboard/dashboard_screenshot.png)

---

## 🛠️ Tech Stack

| Tool | Usage |
|------|-------|
| **Snowflake** | Database, Stages, File Format, Native Dashboard |
| **SQL** | DDL, DML, Data Quality Transformations |
| **Medallion Architecture** | Bronze / Silver / Gold layers |
| **Snowflake Stages** | CSV file ingestion |
| **COPY INTO** | Bulk data loading |
| **Snowflake Dashboard** | Business Intelligence & Visualization |

---

## 📂 Project Structure

```
hotel-booking-snowflake/
├── sql/
│   └── Processing.sql            # All DDL + DML + transformations
├── dashboard/
│   └── dashboard_screenshot.png  # Snowflake native dashboard
├── data/
│   └── sample_data.csv           # Sample hotel booking dataset
└── README.md
```

---

## 🔄 Pipeline Layers

### 🥉 Bronze Layer — Raw Ingestion
- Created Snowflake **FILE FORMAT** for CSV parsing with null handling
- Created **External STAGE** (`STG_HOTEL_BOOKING`) for file upload
- Loaded raw data into `BRONZE_HOTEL_BOOKING` using **COPY INTO**
- All columns stored as `STRING` for maximum ingestion flexibility
- Error handling via `ON_ERROR = CONTINUE`

### 🥈 Silver Layer — Data Cleaning & Validation

Applied **6 production-grade data quality transformations:**

| Check | Transformation Applied |
|-------|----------------------|
| ✅ Email validation | LIKE pattern match — invalid emails set to NULL |
| ✅ Date casting | TRY_TO_DATE() with NULLIF for safe conversion |
| ✅ Negative amounts | ABS() to correct negative total_amount values |
| ✅ Status typo fix | 'confirmeeed', 'confirmd' → 'Confirmed' |
| ✅ Name/City clean | INITCAP + TRIM for standardisation |
| ✅ Invalid row filter | Rows where check-out < check-in removed |

### 🥇 Gold Layer — Analytics-Ready Tables

| Table | Purpose |
|-------|---------|
| `GOLD_AGG_DAILY_BOOKING` | Daily total bookings & revenue |
| `GOLD_AGG_HOTEL_CITY_SALES` | Revenue breakdown by city (DESC) |
| `GOLD_BOOKING_CLEAN` | Fully cleaned production dataset for BI |

---

## 📈 Snowflake Native Dashboard

Built a business-facing dashboard directly in Snowflake showing:
- 📌 **Average Booking Value** — $334.11
- 👥 **Total Guest Count** — 5,072
- 📅 **Monthly Revenue Trends** — Nov 2024 to May 2026
- 🏙️ **Top Cities by Revenue** — ranked bar chart

---

## 🚀 How to Run

1. **Create a Snowflake account** — [signup free](https://signup.snowflake.com)
2. **Create database** — run `CREATE DATABASE HOTEL_DB;`
3. **Run** `sql/Processing.sql` sequentially in Snowflake worksheet
4. **Upload** your CSV to the Snowflake stage using:
```sql
PUT file://path/to/your/data.csv @STG_HOTEL_BOOKING;
```
5. **Query Gold tables** for analytics:
```sql
SELECT * FROM GOLD_AGG_DAILY_BOOKING;
SELECT * FROM GOLD_AGG_HOTEL_CITY_SALES;
```

---

## 🔑 Key SQL Highlights

```sql
-- Silver layer: Safe date casting + email validation + status fix
INSERT INTO SILVER_HOTEL_BOOKING
SELECT
    booking_id,
    INITCAP(TRIM(hotel_city))        AS hotel_city,
    CASE
        WHEN customer_email LIKE '%@%.%'
        THEN LOWER(TRIM(customer_email))
        ELSE NULL
    END                              AS customer_email,
    TRY_TO_DATE(NULLIF(check_in_date, ''))  AS check_in_date,
    ABS(TRY_TO_NUMBER(total_amount)) AS total_amount,
    CASE
        WHEN LOWER(booking_status) IN ('confirmeeed','confirmd')
        THEN 'Confirmed'
        ELSE booking_status
    END                              AS booking_status
FROM BRONZE_HOTEL_BOOKING
WHERE TRY_TO_DATE(check_out_date) >= TRY_TO_DATE(check_in_date);
```

---

## 👤 Author

**Vishal Gupta**
Data Engineer | Snowflake | Azure Databricks | PySpark | Python

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/vishal-gupta-sde)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black)](https://github.com/v889)
