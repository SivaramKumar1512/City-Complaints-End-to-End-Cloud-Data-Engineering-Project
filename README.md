📘 City Complaints – End-to-End Cloud Data Engineering Project

AWS S3 → Snowflake → dbt → (Power BI Ready)

This project demonstrates a complete, production-style data engineering workflow using modern cloud tools.
It ingests raw complaint data, loads it into a warehouse, transforms it into analytics-ready tables using dbt, and prepares it for BI dashboards.

🚀 Architecture Overview
          CSV → JSON
             │
             ▼
     ┌────────────────┐
     │     AWS S3     │
     │ Raw Data Lake  │
     └────────────────┘
             │ Stage (S3)
             ▼
     ┌────────────────┐
     │   Snowflake    │
     │   RAW Layer    │
     └────────────────┘
             │ dbt run
             ▼
     ┌────────────────────────┐
     │         dbt            │
     │  STG → MARTS Models    │
     └────────────────────────┘
             │
             ▼
       ✔ Power BI Ready

📂 Repository Structure
city_complaints_repo/
│
├── README.md
│
├── s3/
│   └── data.json                # Sample data (GitHub-safe)
│
├── snowflake/
│   ├── create.sql               # Database, file format, stage, raw table
│   ├── load.sql                 # COPY INTO RAW table
│   └── analytics_examples.sql   # Example analytical queries
│
└── dbt/
    ├── dbt_project.yml
    └── models/
        ├── sources.yml
        ├── schema.yml           # dbt tests
        ├── staging/
        │   └── stg_complaints.sql
        └── marts/
            ├── fct_complaints.sql
            ├── dim_agency.sql
            └── dim_location.sql

🗂 Data Source

The original dataset is a complaint dataset (CSV).
It was converted into JSON and uploaded to an S3 bucket:

s3://<your-bucket-name>/data.json


Only a small sample is included in this repo for GitHub usage.

❄ Snowflake Setup
1. Create database, stage, and file format

Located in /snowflake/create.sql:

CREATE DATABASE CITY_COMPLAINTS_DB;
CREATE OR REPLACE FILE FORMAT json_ff TYPE = JSON STRIP_OUTER_ARRAY = TRUE;

CREATE OR REPLACE STAGE city_complaints_stage
  URL = 's3://<your-bucket>'
  CREDENTIALS = (
    AWS_KEY_ID = '<aws-key>'
    AWS_SECRET_KEY = '<aws-secret>'
  )
  FILE_FORMAT = json_ff;

2. Load JSON into RAW table

From /snowflake/load.sql:

COPY INTO RAW_COMPLAINTS
FROM @city_complaints_stage/data.json
FILE_FORMAT = (FORMAT_NAME = json_ff);

3. Validate
SELECT COUNT(*) FROM RAW_COMPLAINTS;

🧱 dbt Project

The project includes:

✔ Staging model

Cleans JSON data

Parses timestamps

Normalizes zip codes

Flattens nested fields

✔ Marts layer

fct_complaints – Fact table

dim_agency – Dimension table

dim_location – Location dimension

✔ Tests (schema.yml)

not_null

unique

source tests

🧪 Running dbt
