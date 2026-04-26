# Azure End-to-End Data Engineering Project

## Overview
Built a production-grade ELT pipeline on Azure 
following Medallion Architecture (Bronze/Silver/Gold) 
for a retail banking domain. The pipeline handles 
incremental data ingestion, transformation, and 
aggregation using modern cloud data engineering tools.



**Flow:**
Source (Azure SQL DB) 
→ ADF Incremental Pipeline 
→ Bronze Layer (ADLS Gen2) 
→ Silver Layer (Databricks + Delta Lake) 
→ Gold Layer (Delta Live Tables)



## Tech Stack

| Tool | Purpose |
|------|---------|
| Azure Data Factory | Orchestration + Incremental Ingestion |
| Azure SQL Database | Source system (transactional data) |
| ADLS Gen2 | Data Lake storage (Bronze/Silver/Gold) |
| Azure Databricks | PySpark transformations |
| Delta Lake | ACID transactions + time travel |
| Delta Live Tables | SCD Type 2 + CDC automation |
| Unity Catalog | Data governance + table registration |


## Pipeline Details

### 1. ADF Incremental Pipeline
- **Lookup Activity** reads last watermark 
  timestamp from cdc.json in ADLS Gen2 Bronze
- **Set Variable** captures current UTC time 
  for file naming
- **ForEach Activity** loops through each 
  source table
- **Copy Activity** fetches only new records 
  using WHERE modified_date > last_watermark
- **Script Activity** gets MAX timestamp 
  from source after copy
- **If Condition** handles empty file scenarios
- **Web Activity** sends failure alerts
- **Copy Activity** updates watermark after 
  successful run

### 2. Databricks Silver Layer
- Autoloader reads Parquet files from Bronze
- Schema evolution with addNewColumns mode
- Removes duplicates using dropDuplicates
- Drops rescued data column
- Writes as Delta tables to Unity Catalog silver schema

### 3. Gold Layer (Delta Live Tables)
- Reads from Silver Delta tables
- Applies SCD Type 2 using 
  dlt.create_auto_cdc_flow
- Maintains complete change history
- Writes aggregated tables to gold schema


## Key Features
- Incremental loading — only processes 
  changed records using watermark pattern
- Fault tolerance — If Condition handles 
  empty files gracefully
- Automated alerts — Web Activity triggers 
  on pipeline failure
- Schema evolution — Autoloader handles 
  new columns automatically
- SCD Type 2 — complete change history 
  maintained in Gold layer
- Data governance — Unity Catalog manages 
  all table registrations


## Screenshots

<img width="1920" height="1200" alt="Screenshot (506)" src="https://github.com/user-attachments/assets/2931beba-3b45-4d08-8663-7f711d9656ca" />
<img width="1920" height="1200" alt="Screenshot (505)" src="https://github.com/user-attachments/assets/2811e6d9-a8bc-4c9e-9225-6430611b076b" />
<img width="1920" height="1200" alt="Screenshot (504)" src="https://github.com/user-attachments/assets/7fb187ff-4031-40ad-8a05-8b189b9debdd" />
<img width="1920" height="1200" alt="Screenshot (507)" src="https://github.com/user-attachments/assets/477b8031-0a98-4ddc-abc3-4b6c2eec9dd7" />
<img width="1920" height="1200" alt="Screenshot (509)" src="https://github.com/user-attachments/assets/a6c9d1d7-8321-47bd-b798-bb9741298c77" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/c430b71f-8d68-4a7e-9224-cdc424fde5a5" />





