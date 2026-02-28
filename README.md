# 04-analytics-engineering_zoomcamp
It is the 4th module of data engineering zoomcamp '26

Analytics Engineering is the bridge between Data Engineering and Data Analysis. A Data Engineer builds pipelines and moves data into the warehouse. A Data Analyst creates dashboards and reports for business users. An Analytics Engineer works in the middle and makes sure the data inside the warehouse is clean, organized, tested, and ready to use.
This role became important because cloud data warehouses made storage and computing cheap. Earlier, teams had to carefully choose what data to store. Analytics Engineers bring software engineering practices into analytics work. They use version control like Git, write modular SQL models, add tests, and create documentation. This makes data reliable and easier to maintain.

Modern data workflows mostly follow the ELT approach. ELT means Extract, Load, and then Transform. First, raw data is loaded into the warehouse. After that, transformations are done inside the warehouse using SQL. This approach is faster and more flexible because storage is cheap. dbt is the main tool used for transformation. It runs SQL models inside the warehouse and helps organize, test, and document the data. dbt fits into the “T” of ELT.

In this project, raw data is first loaded into the cloud data warehouse. After loading, we use dbt to transform the data. The goal is to create clean and structured tables that business users can understand.

We use dimensional modeling based on Kimball’s framework. The purpose of dimensional modeling is to make data easy to understand and fast to query. Unlike normalized databases, dimensional modeling allows some data duplication to improve performance and usability.

# What is learnt in this module?

The goal is to build a structured, tested, and documented data warehouse model using NYC Taxi trip data.

# What is dbt?

<img width="819" height="374" alt="image" src="https://github.com/user-attachments/assets/4c6b8cc4-251a-422f-99b1-60cffaa99f5d" />


dbt (data build tool) is a transformation workflow tool.

It sits on top of your data warehouse and helps you turn raw data into something useful for downstream users like analysts, BI tools, dashboards, or ML pipelines.

You write SQL (or sometimes Python) to define transformations.
dbt handles the rest — it compiles the code, runs it in the warehouse, manages dependencies, and saves the results as tables or views.

## How dbt works?

In a real company setup, data comes from many places:

* Backend systems
* Frontend applications
* Third-party APIs (for example weather data)

All this raw data is loaded into a data warehouse such as:

* BigQuery
* Snowflake
* Databricks
* Redshift

dbt is the transformation layer that converts this raw data into clean, structured, business-ready datasets.

You only write a SELECT statement.
dbt takes care of the rest.

## What Happens When You Run dbt run

When you execute:
```
dbt run
```
dbt performs the following steps:
1. Compiles your SQL
   - Resolves ref()
   - Resolves source()
   - Processes Jinja templates and macros

2. Sends the compiled SQL to your data warehouse

3. Materializes the result as:
   - Table
   - View
   - Incremental table
   - Ephemeral model (CTE)
     
You focus on writing SQL.
dbt handles execution and dependency management.

## Why dbt is Important

dbt brings software engineering best practices into analytics.

Key improvements dbt provides:

1. Version control (Git-based workflows)
2. Modularity (models built on top of each other)
3. Testing (data quality checks)
4. Documentation (auto-generated docs)
5. Environment management (dev / staging / prod)
6. CI/CD support

This makes analytics code reliable, maintainable, and production-ready.

## dbt Core vs dbt Cloud

| Feature | dbt Core | dbt Cloud |
|----------|-----------|------------|
| Cost | Free and open source | SaaS product (Free Developer plan available, paid plans for teams) |
| Installation | Installed locally | Web-based (runs in browser) |
| Development | Terminal + local IDE | Built-in web IDE |
| Environment Setup | You manage everything manually | Built-in environment management (dev/staging/prod) |
| Orchestration | External tools required (Airflow, cron, etc.) | Built-in scheduling and job management |
| Documentation Hosting | You host it yourself | Hosted automatically |
| Logging & Monitoring | Manual setup required | Built-in logging and observability |
| Infrastructure Management | Fully managed by you | Managed by dbt Cloud |
| Metadata Access | Manual setup | Built-in APIs |
| Semantic Layer | Not included by default | Included (for metrics management) |
| Control Level | Full control | Less control but easier management |
| Best For | Engineers comfortable managing infra | Teams wanting managed solution |

## Project structure
When you run:
```
dbt init
```
dbt creates a standard folder structure.

```
my_project/
│
├── dbt_project.yml            # Main project config
├── README.md                  # Project documentation
├── profiles.yml               # Local user config (usually in ~/.dbt/)
├── models/                    # SQL models
│   ├── staging/               # Staging layer models
│   ├── intermediate/          # Intermediate transformation models
│   └── marts/                 # Business-ready models
│
├── analysis/                  # Temporary exploratory SQL
├── macros/                    # Reusable SQL macros
├── seeds/                     # CSV files to load as tables
├── snapshots/                 # Historical snapshots of tables
├── tests/                     # Custom tests (optional)
├── logs/                      # dbt runtime logs (created after running dbt)
├── target/                    # Compiled SQL + run artifacts (auto-generated)
├── dbt_packages/              # Installed dbt packages (if any)
├── .dbt/                      # Hidden folder for metadata, manifests, etc.
└── venv/                      # Python virtual environment 
```
Now let’s understand each part.

1. dbt_project.yml
   - dbt_project.yml is the main configuration file of a dbt project. It tells dbt how your project is structured and how it should behave.
   - It controls project information like project name, dbt version, profile name (connection to warehouse)
   - It controls the model configuration — where the models are stored, how they are materialized (as views, tables, or incremental models), and the default settings applied to different folders.
   - It specifies the locations where dbt should search for models, tests, seeds, snapshots, and macros.
   - You can configure tags, schema naming, variables, on-run hooks, and default materializations, as well as define schemas for seeds, snapshot strategies, and target schemas.

 In short, it defines: the project name, the models, how they should be built, and how everything in the project should behave.

2. models/
   - This is the heart of dbt.
   - All .sql files inside this folder become models that dbt builds in your warehouse.
   - Each file represents a model, which is essentially a select query that dbt will run to create a table or view in your data warehouse.
   - In dbt, the models/ folder can have subfolders to organize your project more clearly.
     
      ```      
      models/
     ├── staging/ # Clean and standardize raw data from your sources
     ├── intermediate/ # Combine and transform staging tables into useful calculations
     └── marts/ # Ready-to-use tables for reporting, dashboards, or analytics
      ```
3. analysis/
   - A scratchpad for temporary SQL queries.
   - For exploring, testing ideas, or checking data.
   - Not meant for production.A scratchpad for temporary SQL queries.
   - For exploring, testing ideas, or checking data.
   - Not meant for production.
4. macros/
   - Reusable helper code in SQL.
   - Lets you avoid repeating the same logic in multiple models.
   - Example: a macro to get payment type descriptions.
5. seeds/
   - Stores CSV files.
   - dbt can load them into your warehouse as tables.
   - Useful for lookup tables or small reference data.
6. snapshots/
   - Keeps a history of changing data.
   - If a source table overwrites old data, snapshots record the old values too.
7. tests/
   - Write SQL to check your data quality.
   - Example: check that vendorid is never null.
8. logs/
   - Stores dbt runtime logs.
   - Created automatically when you run dbt.
   - Shows what dbt did step by step.
9. target/
   - Auto-generated folder for compiled SQL and materialized tables/views.
   - dbt creates this when you run dbt run.
10. dbt_packages/
   - Contains installed dbt packages from packages.yml.
   - Only exists if you use external packages.
11. .dbt/
   - Hidden folder for metadata and cache used by dbt.
   - dbt stores manifests and run info here.
   - You usually don’t touch it.
12. venv/
   - Python virtual environment (if created one)
   - Keeps dbt and its dependencies separate from other Python projects.

# Project Overview

This project demonstrates an end-to-end data transformation and analytics workflow for NYC Taxi Trip data (Green & Yellow taxis, 2019–2020).  

## Technologies Used

- **Google BigQuery** – Data warehouse for storing raw and transformed data  
- **Google Cloud Storage (GCS)** – Parquet files for raw NYC taxi data  
- **dbt (Data Build Tool)** – Data transformation, modeling, and testing  
- **SQL** – Transformations and queries  
- **GitHub** – Version control for project files

# Project Setup & Steps

## 1. Install dbt CLI

```
pip install dbt-bigquery
```
## 2. Verify
```
dbt --version
```
## Set up BigQuery
   - Create a GCP project and BigQuery dataset: taxi_rides_ny
   - Enable BigQuery API.
   - Upload Parquet files to GCS buckets.

 Created a dbt project and set up profiles.yml to connect to BigQuery.

 ## Service account setup
   - Created a GCP Service Account for dbt to authenticate with BigQuery.
   - Downloaded the JSON key and stored it securely.
   - Configured dbt in profiles.yml
     
 ## Tested connection using
 ```
   dbt debug
 ```
     
1. Data Ingestion
   - Loaded Green and Yellow Taxi Parquet files from GCS into BigQuery.
   - Addressed schema issues:
     - Column types mismatches (e.g., ehail_fee DOUBLE → NUMERIC)
     - Column name mismatches (e.g., VendorID → vendor_id, lpep_pickup_datetime → pickup_datetime)
   - Used external tables initially and then converted to native tables after correcting types. 

 3. Staging layer
    - Standardized column names to match BigQuery.
    - Casted data types properly:
      - INTEGER (vendor_id, rate_code, trip_type)
      - NUMERIC (trip_distance, fare_amount, ehail_fee)
      - TIMESTAMP (pickup_datetime, dropoff_datetime)
    - Filtered out records with null vendor_id for data quality.
  ```
dbt run --select stg_green_tripdata stg_yellow_tripdata
```
      
 4. Intermediate Layer
    - Combined green and yellow taxi datasets into a single unified model.
  ```
     dbt run --select int_trips_unioned int_trips
  ```
   - Resolved data type mismatches and added a service_type column to identify taxi type.
     
 5. Mart / Analytics Layer
    - Build fact and dimension tables ready for reporting or dashboards.
    - Models:
       - fct_trips → Main fact table with trip metrics
       - dim_zones → Taxi zone reference data
       - dim_vendors → Vendor metadata
       - fct_monthly_zone_revenue → Aggregated monthly revenue per zone
    - Aggregate metrics by month, zone, or vendor
    - Join with dimension tables for descriptive analytics
  ```
    dbt run --select fct_trips dim_zones dim_vendors fct_monthly_zone_revenue
  ```
 6. Run all tests
  ```    
    dbt test
  ```

Resources

- [DataTalks Club](https://datatalks.club/)
- [Bruin Documentation](https://getbruin.com/docs/bruin/commands/overview.html)
- [Data Engineering Zoomcamp](https://github.com/DataTalksClub/data-engineering-zoomcamp)



   




