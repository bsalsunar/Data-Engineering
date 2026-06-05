PROJECT TITLE
Patient Health Monitoring Data Pipeline

BUSINESS SCENARIO
A hospital receives patient health monitoring files every hour from:
- ICU monitoring systems
- Wearable health devices
- Hospital management systems
- Emergency care systems

The hospital wants to:
- Store incoming patient data securely
- Validate health records automatically
- Detect corrupted files
- Clean and transform patient data
- Catalog metadata automatically
- Store processed healthcare data
- Separate bad patient records
- Maintain production-ready ETL workflows

Objective
Build a healthcare ETL pipeline that processes patient monitoring data securely.

PROJECT GOALS
build a system that:
- Ingests patient data
- Validates incoming files
- Cleans medical records
- Detects invalid patient readings
- Stores transformed healthcare data
- Handles schema changes

HEALTH METRICS TO TRACK
| Metric | Example |
| ---------------- | ------- |
| Heart Rate | 72 BPM |
| Blood Pressure | 120/80 |

| Oxygen Level | 98% |
| Temperature | 98.6 F |
| Respiration Rate | 16/min |

PHASE 2 — DATA COLLECTION & STORAGE

TASK 1 — Create S3 Bucket

Bucket Name
patient-health-monitoring

Create Folder Structure

raw/vitals/
raw/patients/
raw/devices/

processed/vitals/
processed/patients/
processed/reports/

rejected/

PHASE 3 — IAM IMPLEMENTATION

TASK 1 — Glue IAM Role

Permissions

* Read raw bucket
* Write processed bucket
* Access Glue Catalog
* Access logs

TASK 2 — Lambda IAM Role

Permissions

* Read uploaded files
* Move rejected files
* Trigger crawler
* Trigger ETL job

PHASE 4 — SAMPLE HEALTHCARE DATA

FILE 1 — patient_vitals.csv

patient_id,timestamp,heart_rate,blood_pressure,oxygen_level,temperature
P1001,2025-01-10 08:00:00,72,120/80,98,98.6
P1002,2025-01-10 08:05:00,95,140/90,96,99.1
P1003,2025-01-10 08:10:00,45,90/60,88,101.2
P1004,2025-01-10 08:15:00,,130/85,97,98.4

P1005,2025-01-10 08:20:00,110,150/95,,100.1
P1005,2025-01-10 08:20:00,110,150/95,,100.1

FILE 2 — patient_details.csv

patient_id,name,age,gender,city,disease
P1001,Rahul Sharma,45,Male,Delhi,Diabetes
P1002,Priya Verma,52,Female,Mumbai,Hypertension
P1003,Amit Singh,60,Male,Pune,Heart Disease
P1004,Neha Kapoor,29,Female,Delhi,Asthma
P1005,Rohit Jain,48,Male,Jaipur,Diabetes

FILE 3 — device_logs.csv

device_id,patient_id,device_type,status,last_sync
D101,P1001,SmartWatch,Active,2025-01-10 08:00:00
D102,P1002,Oximeter,Inactive,2025-01-10 07:50:00
D103,P1003,HeartMonitor,Active,2025-01-10 08:05:00
