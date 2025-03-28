**HI-Labs Training Program – Detailed Project Document**

---

# **Phase 1 Project – Data Extraction & Analysis CLI Tool**

### **Overview:**
Participants will create a CLI-based tool that extracts data from a Linux system, processes it with Python, and stores it in an SQL database.

### **Objectives:**
- Practice basic Linux scripting for automation.
- Work with CSV data and SQL commands for data management.
- Implement basic Python data parsing and cleaning logic.

### **Requirements:**
- Linux environment access (local/VM).
- Sample hospital data in CSV format.
- SQLite or MySQL for database.

### **Modules Integrated:**
- Linux Essentials
- SQL Basics
- Python Basics

### **Milestones:**
1. Create a directory structure in Linux to store data files.
2. Develop a shell script that downloads CSV and sets appropriate permissions.
3. Write a Python script to read, clean, and validate the data.
4. Use SQL to create tables and insert cleaned data.
5. Generate a report of hospital visits using SQL queries.

### **Deliverables:**
- Shell script file
- Python cleaning script
- SQL scripts
- Sample report (CSV or printed output)

---

# **Phase 2 Project – Automated Data Ingestion Pipeline**

### **Overview:**
Build an automated ingestion pipeline using Python, SQL, and AWS, orchestrated via Apache Airflow.

### **Objectives:**
- Understand end-to-end ingestion using AWS and pipelines.
- Automate data transformation and storage using Python.
- Orchestrate with Airflow.

### **Requirements:**
- AWS account (S3, EC2 access)
- Airflow environment
- External CSV data source (via SFTP or web)

### **Modules Integrated:**
- Advanced SQL
- Python Advanced
- AWS & Pipelines

### **Milestones:**
1. Define schema and create tables using advanced SQL features.
2. Build a class-based Python solution to fetch and preprocess data.
3. Upload the cleaned data to an S3 bucket.
4. Launch EC2 instance for further processing.
5. Create an Airflow DAG to manage and automate the entire process.

### **Deliverables:**
- DAG file (.py)
- Python automation script
- SQL performance reports
- Logs and S3 confirmation screenshots

---

# **Phase 3 Project – Big Data Analytics Pipeline**

### **Overview:**
Design a pipeline to ingest, process, and analyze large-scale data using Hadoop/Snowflake, Python, and Airflow.

### **Objectives:**
- Load data into Snowflake or Hadoop.
- Use Dask and Pandas for efficient data transformation.
- Automate with Airflow and manage pipeline monitoring.

### **Requirements:**
- Snowflake environment
- Hadoop/Databricks access
- Large CSV/XML/JSON datasets
- AWS account for storage/logging

### **Modules Integrated:**
- Big Data Technologies
- Advanced Pipeline Dev
- Advanced Python Workflows

### **Milestones:**
1. Integrate multi-format data from external source (SFTP/REST).
2. Transform data using Pandas/Dask workflows.
3. Load structured data into Snowflake.
4. Create Airflow DAGs for orchestration.
5. Use boto3 to log results to S3.
6. Send summary email reports post execution.

### **Deliverables:**
- Python ETL pipeline script
- Airflow DAG files
- Snowflake query reports
- Logs stored in S3
- Email report sample

---

# **Capstone Project – Full Stack Data Engineering Implementation**

### **Overview:**
Build a fully automated pipeline from scratch using all tools and concepts from the training. The project will simulate a real-time hospital analytics system.

### **Tools Used:**
- Linux, SQL, Python
- AWS (S3, EC2, Lambda)
- Apache Airflow
- Snowflake / Hadoop
- Dask, Pandas, boto3

### **Milestones:**
1. Requirement gathering and planning.
2. Build secure SFTP extraction and organize raw files in Linux.
3. Clean and standardize files using Python.
4. Load into database and log using Airflow.
5. Store results and monitoring logs in AWS S3.
6. Send out reports via automated emails.

### **Deliverables:**
- Project Report
- All Scripts (Python/Shell/SQL)
- DAG Files
- Execution Logs
- Presentation Summary (optional for review/demo)

---

# **Evaluation Criteria:**
- Code Quality & Structure
- Successful Execution of Pipeline
- Accuracy of Final Outputs
- Usage of CI/CD/Automation Tools
- Documentation and Reporting

