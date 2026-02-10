# Truck Logistics Data Pipeline

Author: **Sumanth**

> **IMPORTANT:** _To skip the project overview and go straight to setup instructions, click [here](setup.md)_

---

## Project Overview

This is my **batch-oriented ETL data pipeline project** for a truck logistics company. The goal of this project is to consolidate operational trip data collected, transform it into analytics-ready models, and surface business insights through dashboards for executives and senior management.

Unlike a real-time or streaming system, I intentionally designed this as a **scheduled batch pipeline**, reflecting how many logistics organizations actually operate—processing weekly or monthly data drops rather than continuous event streams.

The pipeline provisions cloud infrastructure on Google Cloud Platform, orchestrates data ingestion and transformation, applies analytics modeling, and visualizes key performance and revenue metrics.

---
<img src="static/assets/trucks.jpeg" alt="Trucks" height="300" width="600">
---
## Table of Contents

- [Problem Statement](#problem-statement)
- [Tools & Technology](#tools--technology)
- [Architecture Overview](#architecture-overview)
- [Data Sources & Schema](#data-sources--schema)
- [ETL Orchestration with Mage](#etl-orchestration-with-mage)
- [Analytics Layer with dbt](#analytics-layer-with-dbt)
- [Dashboard & Visualization](#dashboard--visualization)
- [Future Improvements](#future-improvements)
- [Acknowledgements](#acknowledgements)

---

## Problem Statement

Truck logistics companies generate large volumes of operational data related to trips, drivers, customers, and distances traveled. When this data is stored in isolated files and processed manually, it becomes difficult for leadership to:

- Understand revenue performance by customer and trip type
- Track operational efficiency over time
- Make informed, data-driven business decisions

I address these challenges by building a **reliable batch ETL pipeline** that:

- Ingests weekly trip data files
- Applies consistent transformations and revenue calculations
- Centralizes data in a cloud data warehouse
- Enables analytics and visualization for business stakeholders

The result is a repeatable and scalable analytics workflow covering logistics activity from **July through January**.

---

## Tools & Technology

- **Containerization:** Docker
- **Workflow Orchestration:** Mage
- **Infrastructure as Code:** Terraform
- **Data Lake:** Google Cloud Storage
- **Data Warehouse:** Google BigQuery
- **Transformations & Analytics:** dbt (Data Build Tool)
- **Visualization:** Looker Studio

---

## Architecture Overview

The pipeline follows a classic **batch analytics architecture**:

1. **Local CSV ingestion** – Weekly trip data is delivered as CSV files
2. **Transformation & format optimization** – Data is cleaned and converted to Parquet
3. **Cloud storage** – Parquet files are stored in Google Cloud Storage
4. **External tables** – BigQuery external tables reference the Parquet data
5. **Analytics modeling** – dbt builds staging and core models in BigQuery
6. **Visualization** – Looker Studio dashboards query analytics tables

<img src="static/assets/data_architecture.drawio.svg" alt="Data Architecture" height="300" width="600">

This architecture prioritizes simplicity, reproducibility, and cost efficiency over low-latency processing.

---

## Data Sources & Schema

All data used in this project is **synthetically generated** to avoid exposure of private or sensitive information. The data generation logic is included in the repository.

### Source Files

There are two primary datasets:

### 1. `trip_data` (weekly batch files)

Contains trip-level logistics data.

**Columns:**
- `date` – Date of the trip
- `driver` – Driver name
- `customer` – Customer name
- `hours` – Hours worked on the trip
- `km` – Distance traveled in kilometers

These files represent accumulated trips from **July to January**, updated on a regular batch schedule.

### 2. `customer_rates` (reference data)

Contains pricing and fuel surcharge rules per customer.

**Columns:**
- `customer`
- `hour_city`
- `hour_regular`
- `hour_hy`
- `fsc_city`
- `fsc_regular`
- `fsc_hy`
- `hy_mileage`

This dataset is treated as relatively static reference data and is seeded into dbt.

---

## ETL Orchestration with Mage

I use Mage to orchestrate the **batch ETL workflow** using Python, SQL, and dbt blocks.

### Pipeline Steps

1. Load weekly CSV trip data from the local data directory
2. Apply cleaning and enrichment logic
3. Convert the dataset to Parquet format
4. Upload Parquet files to Google Cloud Storage
5. Create or refresh external tables in BigQuery
6. Install dbt dependencies
7. Seed reference data (`customer_rates`)
8. Build staging and core analytics models

   <img src="static/assets/pipeline.png" alt="Pipeline" height="600" width="300">


The entire workflow runs inside Docker containers, ensuring consistent execution across environments.

---

## Analytics Layer with dbt

I use dbt to transform raw logistics data into analytics-ready models.

### Model Structure

- **Staging models**
  - Clean column names
  - Add surrogate keys (e.g. `trip_id`)
  - Derive trip types (city, regular, highway)

- **Core model**
  - Join trip data with customer rate rules
  - Calculate revenue metrics based on hours, mileage, and fuel surcharges
  - Produce a single fact table optimized for reporting

This layered approach improves data quality, testability, and maintainability.

---

## Dashboard & Visualization

I built a Looker Studio dashboard directly on top of BigQuery analytics tables.

The dashboard enables stakeholders to explore:

- Revenue by customer
- Revenue by trip type
- Driver activity and utilization
- Time-based performance trends (July–January)

By using Looker Studio with BigQuery as the source, the solution achieves fast query performance without the complexity of a real-time system.

Dashboard Link: [HERE](https://lookerstudio.google.com/s/pBt7UHZBg1k)

<img src="static/assets/dashboard.png" alt="Chart1" height="300" width="600">

---

## Future Improvements

- Increase historical data volume for longer-term trend analysis
- Partition and cluster BigQuery tables for improved performance
- Version customer rate tables by year
- Introduce data quality tests and freshness checks
- Explore incremental dbt models for larger batch workloads

---

## Project Ownership

This project was **independently designed, developed, and delivered** as an **ongoing project**.

All aspects of the work—including architecture design, infrastructure provisioning, data modeling, orchestration, analytics, and visualization—were completed as a solo effort, without external contributors or third‑party development assistance.

This project reflects my end‑to‑end understanding of batch data engineering systems and my ability to independently design, build, and continuously improve a production‑style analytics pipeline.

---

