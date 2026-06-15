# 🏥 Patient Health Monitoring Data Pipeline

## 📌 Project Overview

The **Patient Health Monitoring Data Pipeline** is a healthcare-focused ETL solution built using **AWS S3, AWS Glue, AWS Lambda, IAM, and PySpark**. The pipeline processes patient monitoring data collected from ICU systems, wearable devices, hospital management systems, and emergency care systems.

The solution automates data ingestion, validation, transformation, cataloging, and storage while identifying corrupted, duplicate, and invalid healthcare records.

---

## 🎯 Business Problem

Hospitals receive large volumes of patient monitoring data every hour from multiple sources. Manual processing can lead to:

* Data quality issues
* Delayed medical insights
* Inconsistent patient records
* Difficulty handling schema changes
* Challenges in maintaining scalable ETL workflows

This project provides a production-ready healthcare ETL pipeline to address these challenges.

---

## 🏗️ Architecture

```text
Healthcare Sources
      │
      ▼
 AWS S3 (Raw Layer)
      │
      ▼
 AWS Lambda
(File Validation)
      │
      ▼
 AWS Glue Crawler
      │
      ▼
 AWS Glue Data Catalog
      │
      ▼
 AWS Glue ETL (PySpark)
      │
      ▼
 AWS S3 (Processed Layer)
      │
      ▼
 Analytics & Reporting
```

---

## 🚀 Project Objectives

The pipeline is designed to:

* Ingest patient healthcare data
* Validate incoming files automatically
* Detect corrupted and invalid records
* Clean and transform healthcare data
* Handle schema evolution
* Catalog metadata automatically
* Separate rejected records
* Store processed data in optimized format

---

## 📂 S3 Data Lake Structure

```text
patient-health-monitoring/

├── raw/
│   ├── vitals/
│   ├── patients/
│   └── devices/
│
├── processed/
│   ├── vitals/
│   ├── patients/
│   └── reports/
│
└── rejected/
```

---

## 🔐 IAM Configuration

### AWS Glue IAM Role

Permissions:

* Read raw healthcare data
* Write processed data
* Access Glue Catalog
* Access CloudWatch Logs

### AWS Lambda IAM Role

Permissions:

* Read uploaded files
* Move invalid files to rejected folder
* Trigger Glue Crawlers
* Trigger Glue ETL Jobs

---

## 📊 Dataset Used

### Patient Vitals

| Column         |
| -------------- |
| patient_id     |
| timestamp      |
| heart_rate     |
| blood_pressure |
| oxygen_level   |
| temperature    |

### Patient Details

| Column     |
| ---------- |
| patient_id |
| name       |
| age        |
| gender     |
| city       |
| disease    |

### Device Logs

| Column      |
| ----------- |
| device_id   |
| patient_id  |
| device_type |
| status      |
| last_sync   |

---

## ⚠️ Data Quality Issues Handled

The pipeline detects and processes:

| Issue                      | Description                                  |
| -------------------------- | -------------------------------------------- |
| Null Values                | Missing heart rate, oxygen level, etc.       |
| Duplicate Records          | Duplicate patient records                    |
| Invalid Data               | Incorrect health readings                    |
| Critical Health Conditions | Low oxygen level, fever, abnormal heart rate |
| Schema Changes             | New columns added in source files            |

---

## 🔎 AWS Glue Crawler

### Glue Database

```text
healthcare_db
```

### Generated Tables

| Table Name      | Source       |
| --------------- | ------------ |
| patient_vitals  | raw/vitals   |
| patient_details | raw/patients |
| device_logs     | raw/devices  |

The crawler automatically discovers:

* Schema
* Data types
* Table locations
* Metadata

---

## ⚙️ AWS Glue ETL Pipeline

### Step 1: Data Ingestion

Read source files:

* patient_vitals.csv
* patient_details.csv
* device_logs.csv

---

### Step 2: Data Cleaning

Performed operations:

* Remove duplicate records
* Handle null values
* Validate patient readings
* Standardize data formats

---

### Step 3: Data Transformation

Created derived columns:

| Column          | Description                   |
| --------------- | ----------------------------- |
| health_status   | Patient health condition      |
| alert_flag      | Indicates critical condition  |
| monitoring_date | Date extracted from timestamp |
| monitoring_hour | Hour extracted from timestamp |

### Health Status Rules

| Condition         | Status    |
| ----------------- | --------- |
| oxygen_level < 90 | Critical  |
| temperature > 100 | Fever     |
| heart_rate > 100  | High Risk |
| Otherwise         | Normal    |

---

### Step 4: Data Enrichment

Joined:

* Patient Vitals
* Patient Details
* Device Logs

Final Dataset:

| Column          |
| --------------- |
| patient_id      |
| patient_name    |
| disease         |
| heart_rate      |
| oxygen_level    |
| temperature     |
| health_status   |
| device_type     |
| monitoring_date |

---

### Step 5: Data Storage

Output Location:

```text
processed/reports/
```

Storage Format:

```text
Parquet
```

Benefits:

* Faster queries
* Compression
* Cost optimization
* Analytics-ready datasets

---

## 📈 Sample Output

| patient_id | patient_name | disease       | heart_rate | oxygen_level | temperature | health_status |
| ---------- | ------------ | ------------- | ---------- | ------------ | ----------- | ------------- |
| P1001      | Rahul Sharma | Diabetes      | 72         | 98           | 98.6        | Normal        |
| P1002      | Priya Verma  | Hypertension  | 95         | 96           | 99.1        | Warning       |
| P1003      | Amit Singh   | Heart Disease | 45         | 88           | 101.2       | Critical      |

---

## 🛠️ Technologies Used

* AWS S3
* AWS Glue
* AWS Glue Crawler
* AWS Glue Data Catalog
* AWS Lambda
* AWS IAM
* PySpark
* Apache Spark
* Parquet

---

## 📊 Key Features

✅ Automated Data Ingestion

✅ Schema Discovery with Glue Crawler

✅ Metadata Management using Glue Catalog

✅ Data Quality Validation

✅ Duplicate Detection

✅ Null Value Handling

✅ Critical Health Alert Detection

✅ Data Transformation using PySpark

✅ Secure Storage using Amazon S3

✅ Production-Ready ETL Workflow

---

## 📚 Learning Outcomes

Through this project, I gained hands-on experience in:

* Building Data Lakes on AWS
* Designing ETL Pipelines
* AWS Glue Development
* PySpark Data Processing
* Data Quality Engineering
* Healthcare Data Analytics
* Metadata Management
* Cloud Security with IAM

---

**Bishal Sunar**

Healthcare Data Engineering Project using AWS Glue, S3, Lambda, IAM, and PySpark.
