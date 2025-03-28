**HI-Labs Training Program – Detailed Project Document with Diagrams and Code**



# **Capstone Project – End-to-End Hospital Analytics Platform**

### **Overview:**
This project integrates all phases into one cohesive end-to-end pipeline that mimics a real-world hospital data engineering scenario. From raw data ingestion to big data transformation and analysis, the system will be fully automated using modern cloud and orchestration tools.

### **Objectives:**
- Combine CLI automation, Python ETL, SQL processing, and pipeline orchestration.
- Integrate multiple data sources and formats.
- Deliver clean, transformed data for analytics in a warehouse like Snowflake.
- Automate everything using Airflow and store logs in AWS S3.

### **Architecture Diagram:**
```
[Raw Data Sources (CSV/JSON/XML)]
         ↓
[Linux Script for Downloading] → [Python Preprocessing Script]
         ↓                                     ↓
[S3 - Raw Layer] ←──────────────┘
         ↓
[EC2 for Batch Transformations (Pandas/Dask)]
         ↓
[S3 - Clean Layer] → [Snowflake Load Script]
         ↓
[Airflow DAG Scheduler]
         ↓
[Reports + Logs in S3] → [Email/Slack Notification]
```

### **Workflow Diagram:**
```
1. Shell script automates CSV download
2. Python cleans and validates raw data
3. Uploads to S3 (raw)
4. EC2 performs batch processing
5. Resulting data loaded into Snowflake
6. Airflow DAG orchestrates steps, monitors status
7. Reports exported, logs archived, notifications sent
```

### **Key Modules Involved:**
- Linux Essentials
- Python Basics & Advanced
- SQL Basics & Advanced
- AWS (S3, EC2, IAM)
- Apache Airflow
- Snowflake/Hadoop
- Big Data Tools (Pandas, Dask)

### **Deliverables:**
- Shell + Python Scripts
- MySQL/PostgreSQL table creation scripts
- Airflow DAGs
- Cleaned datasets in S3 (versioned)
- Final data in Snowflake
- Analytics query output (SQL)
- Logs and email reports

### **Sample Capstone Files:**

#### **Shell Script – run_data_pipeline.sh**
```bash
#!/bin/bash
python3 download_data.py
python3 preprocess.py
aws s3 cp cleaned.csv s3://hospital-bucket/cleaned/
```

#### **Airflow DAG – full_pipeline.py**
```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator
from datetime import datetime

def send_notification():
    print("Data pipeline completed successfully!")

dag = DAG('full_hospital_pipeline', start_date=datetime(2024, 1, 1), schedule_interval='@daily', catchup=False)

run_script = BashOperator(task_id='run_pipeline', bash_command='bash run_data_pipeline.sh', dag=dag)
notify = PythonOperator(task_id='notify', python_callable=send_notification, dag=dag)

run_script >> notify
```

#### **Sample SQL – Report Queries**
```sql
SELECT diagnosis, COUNT(*) AS case_count FROM cleaned_patients GROUP BY diagnosis ORDER BY case_count DESC;
```

### **Milestones:**
1. Architecture finalization
2. Implementation of shell automation + Python ETL
3. Cloud storage integration (S3)
4. Data warehouse loading (Snowflake)
5. Airflow DAG orchestration
6. Reporting + notification setup

### **Evaluation Criteria:**
- Proper use of tools and pipelines
- Clean code and modular structure
- Successfully loading and querying warehouse
- Timely execution via Airflow
- Valid reporting and data integrity

---

