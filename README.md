
# Website Traffic Analytics Pipeline

This project implements an end-to-end data pipeline for analyzing website traffic, from raw log ingestion to a structured dimensional model for advanced analytics. The pipeline leverages Apache Spark for distributed data processing, Apache Airflow for orchestration, and AWS S3 for scalable data storage. Key metrics are also extracted and loaded into a SQL database for quick reporting.

## Project Overview

The pipeline automates the following stages:

1.  **Raw Data Ingestion & Initial Cleaning:**
    * **Source:** Unstructured website log files (simulated).
    * **Process:** Apache Spark is used to parse raw log lines using regular expressions, extracting structured fields (timestamp, IP, URL, status code, user agent, etc.).
    * **Storage:** Lightly cleaned, raw-structured data is stored in AWS S3 as Parquet files, serving as a data lake.

2.  **Key Metrics Calculation & SQL Loading:**
    * **Purpose:** Provide daily, high-level insights for business stakeholders.
    * **Process:** An Apache Airflow DAG orchestrates a daily Spark job that reads the cleaned log data for the day. It calculates:
        * **Daily Unique Visitors**
        * **Daily Page Views**
        * **Daily Error Rates (4xx/5xx)**
    * **Storage:** These calculated metrics are loaded into a SQL database (e.g., PostgreSQL) for quick access and dashboarding.

3.  **Dimensional Model Transformation (Star Schema):**
    * **Purpose:** Enable flexible, high-performance analytical queries and deeper insights into user behavior.
    * **Process:** An Apache Airflow DAG orchestrates another daily Spark job that transforms the cleaned log data into a dimensional model (star schema). This involves:
        * Extracting and populating various **Dimension Tables** (e.g., `dim_date`, `dim_time`, `dim_user`, `dim_page`, `dim_device_user_agent`, `dim_referrer`). Surrogate keys are generated using hashing.
        * Building a central `fact_page_views` table, containing foreign keys to the dimension tables and quantitative measures (e.g., `request_count`, `response_size_bytes`).
    * **Storage:** The entire dimensional model (fact and dimension tables) is stored in AWS S3 as highly optimized Parquet files, partitioned for efficient querying.

## Technologies Used

* **Apache Spark:** Distributed data processing for cleaning, transformation, and aggregation.
* **Apache Airflow:** Workflow orchestration, scheduling, and monitoring.
* **AWS S3:** Scalable, durable, and cost-effective data lake storage for raw-cleaned data and the dimensional model.
* **SQL Database (e.g., PostgreSQL):** For storing aggregated daily metrics.
* **Python:** Primary language for Spark jobs and Airflow DAGs.
* **Pandas:** Used within Airflow's Python tasks for reading small CSVs and preparing data for SQL loading.

## Project Structure
website_analytics_pipeline/
├── dags/
│   ├── calculate_website_metrics.py        
│   ├── daily_metrics_pipeline.py          
│   ├── dimensional_model_transformation.py 
│   └── dwh_transformation_dag.py          
├── requirements.txt                        
└── README.md                                
