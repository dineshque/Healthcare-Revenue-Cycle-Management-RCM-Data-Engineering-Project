# Healthcare Revenue Cycle Management (RCM) Data Engineering Project

## Project Overview

This project implements an end-to-end **Healthcare Revenue Cycle Management (RCM)** data pipeline using **Azure Data Engineering services, PySpark, and SQL**. The pipeline processes healthcare claims, patient billing, and payment data to generate actionable insights and improve revenue efficiency.

The solution follows **Medallion Architecture** and uses a **metadata-driven ingestion framework** to handle multiple healthcare data sources efficiently.

---

##  Key Features

* Designed and implemented scalable **ETL pipelines** for healthcare data processing.
* Built **metadata-driven ingestion workflows** using Azure Data Factory.
* Processed large datasets using **PySpark and Delta Lake**.
* Implemented **Slowly Changing Dimension (SCD Type 2)** logic.
* Created a **Common Data Model (CDM)** for healthcare analytics.

* Implemented audit logging using Delta tables.

---

##  Architecture

### Medallion Architecture

![Screenshot 2026-02-12 120051_1771356216831.png](./Screenshot 2026-02-12 120051_1771356216831.png "Screenshot 2026-02-12 120051_1771356216831.png")

```
Landing Layer → Bronze Layer → Silver Layer → Gold Layer
```

| Layer   | Description                               |
| ------- | ----------------------------------------- |
| Landing | Raw flat file ingestion                   |
| Bronze  | Raw data stored in Parquet format         |
| Silver  | Cleaned, enriched, CDM & SCD2 implemented |
| Gold    | Aggregated Fact & Dimension tables        |

---

## Data Sources

### 1️ EMR Database (Azure SQL DB)

* Patients
* Providers (Doctors)
* Departments
* Transactions (Billing Data)
* Encounters (Patient Visits)

### 2️ Claims Data

* Monthly CSV files dropped into Data Lake

### 3️ External APIs

* NPI (National Provider Identifier)
* ICD (International Classification of Diseases)
* CPT (Procedure Codes)

### 4️ Audit Table

* Delta table for tracking load history and pipeline execution.

---

## ☁️ Azure Services Used

| Service                      | Purpose                          |
| ---------------------------- | -------------------------------- |
| Azure Data Factory           | Data ingestion & orchestration   |
| Azure SQL Database           | EMR source system                |
| Azure Data Lake Storage Gen2 | Data storage                     |
| Azure Databricks             | Data transformation & processing |

---

##  Data Storage Structure

```
ADLS Gen2
│
├── landing
├── bronze
├── silver
├── gold
└── config
    └── emr
        └── load_config.csv
```

---

##  File Formats

* Bronze → Parquet
* Silver → Delta Tables
* Gold → Delta Tables

---

##  Data Flow

### Source to Bronze Layer

| Source               | Destination      |
| -------------------- | ---------------- |
| EMR SQL DB           | Bronze (Parquet) |
| Claims Flat Files    | Landing → Bronze |
| APIs (NPI, ICD, CPT) | Bronze (Parquet) |

---

##  Metadata Driven Pipeline

### Config File Sample

```csv
database,datasource,tablename,loadtype,watermark,is_active,targetpath
hospital-a,hos-a,dbo.encounters,Incremental,ModifiedDate,1,hosa
hospital-a,hos-a,dbo.patients,Incremental,ModifiedDate,1,hosa
```

---

##  Azure Data Factory Pipeline Design

### Pipeline Activities

#### Step 1: Lookup Config File

* Reads metadata configuration.

#### Step 2: ForEach Activity

* Iterates over each table configuration.

#### Step 3: Check Bronze File Availability

* Archives existing files before loading.

---

### Full Load Process

1. Extract all data from SQL source.
2. Add datasource column.
3. Load into Bronze layer.
4. Insert audit log record.

---

### Incremental Load Process

1. Fetch last load timestamp from audit table.
2. Extract delta data using watermark column.
3. Load into Bronze layer.
4. Update audit log table.

---

## 🥉 Bronze Layer

Stores raw data in **Parquet Format**:

* EMR Data
* Claims Data
* NPI, ICD, CPT Data

---

## 🥈 Silver Layer

### Processing Includes:

* Data cleansing
* Data enrichment
* Common Data Model creation
* SCD Type 2 implementation
* Delta Table storage

---

## 🥇 Gold Layer

Final curated analytics layer.

### Filters Applied:

* `is_current = true`
* `is_quarantined = false`

### Outputs:

* Fact tables
* Dimension tables

---

##  Data Processing with Databricks

Implemented using:

* PySpark transformations
* Delta Lake merge operations

---


##  Tech Stack

* Azure Data Factory
* Azure Databricks
* PySpark
* SQL
* Delta Lake
* ADLS Gen2

---

##  Learning Outcomes

* Implemented Medallion Architecture
* Designed metadata-driven ingestion framework
* Applied SCD Type 2 in real healthcare data
* Optimized distributed data processing
