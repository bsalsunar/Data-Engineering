# COVID-19 Analytics on AWS – End-to-End Data Engineering Project

## Overview

This project implements a complete end-to-end data analytics pipeline for COVID-19 data using AWS cloud services. The solution follows a modern Data Lakehouse architecture with Bronze, Silver, and Gold layers, enabling scalable ingestion, transformation, warehousing, and analytics.

The pipeline processes publicly available COVID-19 datasets from the AWS Open Data COVID-19 Lake and delivers curated analytical datasets for reporting and business intelligence.

---

## Architecture

```text
AWS COVID-19 Open Data
        │
        ▼
     Amazon S3
   (Bronze Layer)
        │
        ▼
 AWS Glue / PySpark
(Data Standardization)
        │
        ▼
     Amazon S3
   (Silver Layer)
        │
        ▼
 AWS Glue / PySpark
 (Star Schema Build)
        │
        ▼
     Amazon S3
    (Gold Layer)
        │
        ▼
 Amazon Redshift
(Data Warehouse)
        │
        ▼
 Athena / BI Tools
(Analytics & Reporting)
```

---

## Dataset Sources

Data is sourced from the AWS COVID-19 Data Lake:

* New York Times COVID-19 State-Level Data
* COVID Tracking Project Testing Data
* State Abbreviation Lookup Dataset

### Source URLs

* https://registry.opendata.aws/aws-covid19-lake/
---

## Project Objectives

* Build a cloud-based COVID-19 analytics platform.
* Implement Medallion Architecture (Bronze → Silver → Gold).
* Standardize and clean raw COVID-19 datasets.
* Create dimensional models and fact tables.
* Enable SQL-based analytics using Athena and Redshift.
* Demonstrate scalable ETL using PySpark.

---

## Technology Stack

| Component        | Technology                |
| ---------------- | ------------------------- |
| Storage          | Amazon S3                 |
| Processing       | Apache Spark (PySpark)    |
| Metadata Catalog | AWS Glue                  |
| Query Engine     | Amazon Athena             |
| Data Warehouse   | Amazon Redshift           |
| Data Format      | Parquet                   |
| Language         | Python                    |
| SQL Engine       | Athena SQL / Redshift SQL |

---

## Data Lake Structure

```text
s3://<bucket>/covid/

├── bronze/
│   ├── nytimes/
│   ├── covid_tracking/
│   └── static/
│
├── silver/
│   ├── cases_standardized/
│   └── testing_standardized/
│
└── gold/
    ├── dim_date/
    ├── dim_state/
    ├── fact_cases_state_daily/
    └── fact_testing_state_daily/
```

---

# Bronze Layer

### Purpose

Store raw source files without modification.

### Datasets

#### Cases Data

```text
bronze/nytimes/us_states.csv
```

Contains:

* date
* state
* cases
* deaths

#### Testing Data

```text
bronze/covid_tracking/states_daily.csv
```

Contains:

* positive tests
* negative tests
* total tests

#### State Lookup

```text
bronze/static/states_abv.csv
```

Maps:

* State Name
* State Code

---

# Silver Layer

### Purpose

Clean, standardize, and transform raw data.

### Transformations

#### Cases Dataset

* Convert dates to DATE datatype
* Standardize state names
* Map state names to state codes
* Cast numeric columns
* Generate partition columns

Output:

```text
silver/cases_standardized/
```

#### Testing Dataset

* Parse YYYYMMDD date format
* Standardize state codes
* Cast cumulative testing metrics
* Generate partition columns

Output:

```text
silver/testing_standardized/
```

### Partition Strategy

```text
state_code/year/month/day
```

Benefits:

* Faster Athena queries
* Reduced scan costs
* Improved performance

---

# Gold Layer

### Purpose

Build analytical star-schema tables for reporting and dashboards.

---

## Dimension Tables

### dim_date

| Column     |
| ---------- |
| date_id    |
| full_date  |
| year       |
| month      |
| day        |
| dow        |
| is_weekend |

---

### dim_state

| Column     |
| ---------- |
| state_code |
| state_name |

---

## Fact Tables

### fact_cases_state_daily

| Column     |
| ---------- |
| date_id    |
| state_code |
| cases_cum  |
| deaths_cum |
| new_cases  |
| new_deaths |

Derived Metrics:

```text
new_cases =
current_cases - previous_day_cases
```

```text
new_deaths =
current_deaths - previous_day_deaths
```

---

### fact_testing_state_daily

| Column          |
| --------------- |
| date_id         |
| state_code      |
| tests_total_cum |
| tests_pos_cum   |
| tests_neg_cum   |
| new_tests       |
| positivity_rate |

Derived Metrics:

```text
new_tests =
current_tests - previous_day_tests
```

```text
positivity_rate =
tests_pos_cum / tests_total_cum
```

---

# AWS Glue & Athena

### Glue

Used to:

* Register Parquet datasets
* Maintain metadata catalog
* Support Athena querying

### Athena Validation Queries

```sql
SELECT MIN(full_date),
       MAX(full_date),
       COUNT(*)
FROM covid_silver_db.cases_standardized;
```

```sql
SELECT state_code,
       COUNT(*)
FROM covid_silver_db.testing_standardized
GROUP BY 1
ORDER BY 2 DESC;
```

---

# Redshift Data Warehouse

Gold-layer datasets are loaded into Amazon Redshift using COPY commands.

### Schema

```text
covid_gold
```

Tables:

* dim_date
* dim_state
* fact_cases_state_daily
* fact_testing_state_daily

### Optimization

#### Distribution Keys

```sql
DISTKEY(state_code)
```

#### Sort Keys

```sql
SORTKEY(date_id, state_code)
```

Benefits:

* Faster joins
* Improved query performance
* Efficient data distribution

---

# Sample Analytics

## Top 5 States by New Cases

```sql
SELECT s.state_name,
       f.new_cases
FROM covid_gold.fact_cases_state_daily f
JOIN covid_gold.dim_state s
ON s.state_code = f.state_code
WHERE f.date_id = 20200515
ORDER BY f.new_cases DESC
LIMIT 5;
```

---

## Positivity Rate Trend

```sql
SELECT d.full_date,
       t.tests_pos_cum::double precision /
       NULLIF(t.tests_total_cum,0)
       AS positivity_rate
FROM covid_gold.fact_testing_state_daily t
JOIN covid_gold.dim_date d
ON d.date_id = t.date_id
WHERE t.state_code = 'NY'
ORDER BY d.full_date;
```

---

## Daily Cases vs Testing Trend

```sql
SELECT d.full_date,
       c.new_cases,
       t.new_tests,
       t.tests_pos_cum::double precision /
       NULLIF(t.tests_total_cum,0)
       AS positivity_rate
FROM covid_gold.fact_cases_state_daily c
JOIN covid_gold.fact_testing_state_daily t
ON t.date_id = c.date_id
AND t.state_code = c.state_code
JOIN covid_gold.dim_date d
ON d.date_id = c.date_id
WHERE c.state_code = 'CA'
ORDER BY d.full_date;
```

---

# Key Learnings

* Data Lake Architecture on AWS
* Medallion Design Pattern
* PySpark ETL Development
* AWS Glue Catalog Integration
* Athena Query Optimization
* Redshift Data Warehousing
* Star Schema Modeling
* Partitioned Parquet Storage
* Analytical SQL Development

---

# Future Enhancements

* Add vaccination datasets
* Build automated Glue Workflows
* Create Airflow orchestration
* Implement incremental loading
* Build QuickSight dashboards
* Add CDC and Johns Hopkins datasets
* Create real-time streaming pipeline using Kinesis

---

# Author

**BISHAL SUNAR**

AWS | Data Engineering | Analytics Engineering

This project demonstrates a complete cloud-native data engineering workflow using AWS services, PySpark, Athena, and Redshift to deliver scalable COVID-19 analytics.
