**HI-Labs Training Program – Detailed Project Document with Diagrams and Code**

---

# **Phase 1 Project – Data Extraction & Analysis CLI Tool**

### **Overview:**
Participants will create a CLI-based tool that extracts data from a Linux system, processes it with Python, and stores it in a PostgreSQL or MySQL database.

### **Objectives:**
- Practice basic Linux scripting for automation.
- Work with CSV data and SQL commands for data management.
- Implement basic Python data parsing and cleaning logic.

### **Architecture Diagram:**
```
[CSV Data Source] --> [Linux Shell Script] --> [Python Cleaner] --> [PostgreSQL/MySQL Database]
```

### **Workflow Diagram:**
```
1. Download sample data
2. Organize into folders via shell script
3. Read and validate data in Python
4. Create SQL tables
5. Insert cleaned data
6. Run SQL queries and generate report
```

### **Complete Code Implementation:**

#### **1. Shell Script (download_and_prepare.sh)**
```bash
#!/bin/bash

# Create folders
mkdir -p ~/project_hilabs/data
mkdir -p ~/project_hilabs/reports

# Download sample CSV
curl -o ~/project_hilabs/data/hospital_data.csv "https://example.com/sample_hospital_data.csv"

# Set permissions
chmod 755 ~/project_hilabs/data/hospital_data.csv
```

#### **2. SQL Script (create_hospital_schema.sql)**
```sql
CREATE TABLE IF NOT EXISTS patients (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INTEGER,
    gender VARCHAR(10),
    diagnosis TEXT,
    visit_date DATE
);
```

#### **3. Python Script (process_and_load.py)**
```python
import csv
import psycopg2  # or use mysql.connector for MySQL
from datetime import datetime

# PostgreSQL connection setup
db = psycopg2.connect(
    host="localhost",
    database="hilabs",
    user="your_user",
    password="your_password"
)
cursor = db.cursor()

# Create table
with open("create_hospital_schema.sql", "r") as f:
    cursor.execute(f.read())

def validate_row(row):
    try:
        datetime.strptime(row['visit_date'], '%Y-%m-%d')
        int(row['age'])
        return True
    except:
        return False

# Load and clean CSV data
with open("project_hilabs/data/hospital_data.csv", newline='') as csvfile:
    reader = csv.DictReader(csvfile)
    valid_rows = [
        (row['name'], int(row['age']), row['gender'], row['diagnosis'], row['visit_date'])
        for row in reader if validate_row(row)
    ]

cursor.executemany("""
    INSERT INTO patients (name, age, gender, diagnosis, visit_date)
    VALUES (%s, %s, %s, %s, %s)
""", valid_rows)

db.commit()
db.close()
```

#### **4. SQL Report Script (generate_report.sql)**
```sql
SELECT gender, COUNT(*) as count FROM patients GROUP BY gender;
```

#### **5. Python Report Generator (generate_report.py)**
```python
import psycopg2
import csv

db = psycopg2.connect(
    host="localhost",
    database="hilabs",
    user="your_user",
    password="your_password"
)
cursor = db.cursor()
cursor.execute("SELECT gender, COUNT(*) FROM patients GROUP BY gender")
rows = cursor.fetchall()

db.close()

with open("project_hilabs/reports/gender_report.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["Gender", "Count"])
    writer.writerows(rows)
```

### **Execution Instructions:**
1. Run shell script to prepare data:
```bash
bash download_and_prepare.sh
```

2. Run Python script to load and clean data:
```bash
python3 process_and_load.py
```

3. Generate summary report:
```bash
python3 generate_report.py
```

### **Requirements:**
- Linux environment (or WSL/macOS/VM)
- PostgreSQL or MySQL Server
- Python 3.7+
- `psycopg2` for PostgreSQL (`pip install psycopg2`)
- `mysql-connector-python` for MySQL if chosen

### **Modules Integrated:**
- Linux Essentials
- SQL Basics
- Python Basics

### **Milestones:**
1. Create directory structure and download files
2. Clean and load data using Python
3. Create and query DB
4. Export reports from analysis

### **Deliverables:**
- Shell script
- Python scripts
- SQL scripts
- CSV report

**HI-Labs Training Program – Detailed Project Document with Diagrams and Code**

---

# **Phase 1 Project – Data Extraction & Analysis CLI Tool**

### *(Phase 1 section retained as is – PostgreSQL and MySQL version already implemented)*

---

# **Phase 2 Project – Automated Data Ingestion Pipeline**

### **Overview:**
Participants will build an automated ingestion pipeline using Python, SQL, AWS (S3, EC2), and Apache Airflow. This pipeline will fetch files, transform them, upload to cloud storage, and orchestrate the process.

### **Objectives:**
- Automate the end-to-end data ingestion process.
- Trigger pipeline via Apache Airflow DAGs.
- Integrate EC2, S3, Python, SQL, and Airflow.

### **Architecture Diagram:**
```
[SFTP/External Data Source] -> [Python Script] -> [AWS S3 Bucket]
                                           ↘           ↓
                                        [EC2] -> [Process with Pandas] -> [Upload Cleaned Data to S3]
                                                 ↓
                                           [Trigger Airflow DAG] -> [Store Status Logs]
```

### **Workflow Diagram:**
```
1. Python script connects to SFTP server
2. Downloads and saves raw files
3. Uploads files to S3 bucket
4. Launch EC2 instance to process data
5. EC2 script transforms data using Pandas
6. Uploads clean data back to S3
7. Airflow DAG orchestrates and monitors this entire process
```

### **Code and Configuration:**

#### **1. Python SFTP + S3 Upload Script (sftp_to_s3.py)**
```python
import paramiko
import boto3

s3 = boto3.client('s3')
host, port = 'sftp.example.com', 22
username, password = 'user', 'pass'

transport = paramiko.Transport((host, port))
transport.connect(username=username, password=password)
sftp = paramiko.SFTPClient.from_transport(transport)

filename = 'hospital_data.csv'
sftp.get(f'/remote/path/{filename}', filename)
sftp.close()

s3.upload_file(filename, 'your-bucket-name', f'raw/{filename}')
```

#### **2. EC2 Python Script for Processing (process_on_ec2.py)**
```python
import pandas as pd
import boto3

s3 = boto3.client('s3')
filename = 'hospital_data.csv'

# Download from S3
s3.download_file('your-bucket-name', f'raw/{filename}', filename)

# Process with Pandas
df = pd.read_csv(filename)
df = df.dropna()
df['visit_date'] = pd.to_datetime(df['visit_date'])

# Save and upload
clean_file = 'cleaned_data.csv'
df.to_csv(clean_file, index=False)
s3.upload_file(clean_file, 'your-bucket-name', f'cleaned/{clean_file}')
```

#### **3. Airflow DAG (airflow_dag.py)**
```python
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator
from datetime import datetime

def trigger_sftp():
    import subprocess
    subprocess.run(["python3", "sftp_to_s3.py"])

def trigger_ec2():
    import boto3
    ec2 = boto3.client('ec2')
    ec2.start_instances(InstanceIds=['i-xxxxxxxxxxxxxxxxx'])

def finalize():
    print("Pipeline Complete")

dag = DAG('hospital_data_pipeline', start_date=datetime(2024, 1, 1), schedule_interval='@daily', catchup=False)

step1 = PythonOperator(task_id='sftp_to_s3', python_callable=trigger_sftp, dag=dag)
step2 = PythonOperator(task_id='trigger_ec2', python_callable=trigger_ec2, dag=dag)
step3 = PythonOperator(task_id='finalize', python_callable=finalize, dag=dag)

step1 >> step2 >> step3
```

### **Requirements:**
- AWS CLI and IAM setup
- S3 Bucket, EC2 instance (with role permissions)
- Airflow environment
- Python 3.7+, boto3, pandas, paramiko

### **Modules Integrated:**
- Python Advanced
- AWS and Pipeline Basics
- Apache Airflow

### **Milestones:**
1. Automate file fetching via Python + SFTP
2. Upload and store raw data in S3
3. Process data on EC2 with Pandas
4. Orchestrate using Airflow DAG

### **Deliverables:**
- Python scripts for SFTP, EC2 processing
- Cleaned data in S3
- Working Airflow DAG
- Logs and pipeline status dashboard

(Phase 3 section follows below as before)

**HI-Labs Training Program – Detailed Project Document with Diagrams and Code**

---

# **Phase 1 Project – Data Extraction & Analysis CLI Tool**

### *(Phase 1 section retained as is – PostgreSQL and MySQL version already implemented)*

---

# **Phase 2 Project – Automated Data Ingestion Pipeline**

### *(Phase 2 section retained as updated)*

---

# **Phase 3 Project – Big Data Analytics Pipeline**

### **Overview:**
Participants will build a large-scale data analytics pipeline using Snowflake (or Hadoop), Apache Airflow, and Python. The system ingests data, performs transformations, stores in a data warehouse, and runs analytics queries.

### **Objectives:**
- Ingest and store large datasets into Snowflake or Hadoop.
- Transform and validate data using Pandas/Dask.
- Use Airflow to automate and orchestrate the data pipeline.
- Perform analytics with SQL and visualize results.

### **Architecture Diagram:**
```
[SFTP Source] → [Python ETL Script] → [Transform with Pandas/Dask]
                                           ↓
                                    [Store in Snowflake or Hadoop]
                                           ↓
                                   [Query & Aggregate with SQL]
                                           ↓
                                 [Airflow DAG Automation + Logs in S3]
```

### **Workflow Diagram:**
```
1. Download JSON/CSV/XML files from SFTP
2. Preprocess and clean using Pandas or Dask
3. Convert and load into Snowflake using Python connector
4. Query and aggregate data via SQL scripts
5. Schedule entire process with Airflow DAG
6. Store pipeline status logs and errors to AWS S3
```

### **Code and Configuration:**

#### **1. Python ETL Script (etl_to_snowflake.py)**
```python
import pandas as pd
import boto3
import snowflake.connector

# Step 1: Load large file
file_path = 'hospital_large_data.csv'
df = pd.read_csv(file_path)

# Step 2: Transform
filtered = df.dropna()
filtered['visit_date'] = pd.to_datetime(filtered['visit_date'])

# Step 3: Connect to Snowflake
conn = snowflake.connector.connect(
    user='USER',
    password='PASSWORD',
    account='ACCOUNT',
    warehouse='WH',
    database='DB',
    schema='PUBLIC'
)
cursor = conn.cursor()
cursor.execute("CREATE OR REPLACE TABLE IF NOT EXISTS cleaned_patients (id INT, name STRING, age INT, gender STRING, diagnosis STRING, visit_date DATE)")

# Step 4: Upload data
for row in filtered.itertuples(index=False):
    cursor.execute("INSERT INTO cleaned_patients VALUES (%s, %s, %s, %s, %s, %s)", row)
conn.commit()
```

#### **2. Sample Analytics SQL (analytics.sql)**
```sql
SELECT gender, COUNT(*) AS visits FROM cleaned_patients GROUP BY gender;
```

#### **3. Airflow DAG (bigdata_dag.py)**
```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime
import subprocess

def run_etl():
    subprocess.run(["python3", "etl_to_snowflake.py"])

def archive_logs():
    import boto3
    s3 = boto3.client('s3')
    s3.upload_file('etl.log', 'your-bucket-name', 'logs/etl.log')

dag = DAG('bigdata_pipeline', start_date=datetime(2024, 1, 1), schedule_interval='@daily', catchup=False)

step1 = PythonOperator(task_id='run_etl_script', python_callable=run_etl, dag=dag)
step2 = PythonOperator(task_id='archive_logs', python_callable=archive_logs, dag=dag)

step1 >> step2
```

### **Requirements:**
- AWS S3 bucket
- Snowflake account
- Airflow scheduler
- Python packages: pandas, boto3, snowflake-connector-python

### **Modules Integrated:**
- Big Data Technologies
- Airflow DAGs
- Advanced Python Workflows

### **Milestones:**
1. Design Snowflake schema and transform logic
2. ETL script using Pandas/Dask
3. Airflow DAG for orchestration and logging
4. Analytics SQL and dashboarding (optional)

### **Deliverables:**
- Python ETL script
- Snowflake table with transformed data
- Airflow DAG
- SQL query results (CSV or screenshots)
- Pipeline logs stored in S3



