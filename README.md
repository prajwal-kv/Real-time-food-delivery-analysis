# Real-time-food-delivery-analysis

A production-ready, real-time data analytics pipeline for processing and analyzing food industry data using AWS services, Apache Airflow, and PySpark.
📋 Table of Contents

Overview
Architecture
Features
Tech Stack
Prerequisites
Project Structure
Setup Instructions
Pipeline Flow
Configuration
Usage
Monitoring
Contributing
License

🎯 Overview
This project implements an end-to-end real-time data pipeline for food industry analytics. It ingests streaming data, processes it using distributed computing, stores it in a scalable data warehouse, and provides interactive dashboards for business intelligence.
Key Capabilities

Real-time data ingestion from multiple sources
Automated data deduplication and transformation
Scalable processing using Apache Spark on EMR
Centralized data warehouse with dimensional modeling
Interactive dashboards for data visualization
CI/CD integration for automated deployments

🏗️ Architecture
Local Development → CodeBuild → S3
                                 ↓
Python Mock Generator → Kinesis Data Stream
                                 ↓
                         Apache Airflow (Orchestration)
                                 ↓
                         Amazon EMR (PySpark)
                          (Deduplication)
                                 ↓
                         Amazon Redshift
                      (Dim Tables + Fact Tables)
                                 ↓
                         Amazon QuickSight
                           (Dashboards)
Architecture Components

CI/CD Layer: Automated deployment of DAGs, PySpark scripts, and dimension tables
Ingestion Layer: Real-time data streaming via Kinesis
Orchestration Layer: Workflow management using Apache Airflow
Processing Layer: Distributed data processing with PySpark on EMR
Storage Layer: Data warehouse built on Amazon Redshift
Visualization Layer: Business intelligence dashboards with QuickSight
Security Layer: IAM roles and policies across all services

✨ Features

⚡ Real-time Processing: Stream processing with sub-second latency
🔄 Automated Workflows: Self-managing pipelines with Airflow
🧹 Data Quality: Built-in deduplication and validation
📊 Dimensional Modeling: Star schema for optimized analytics
🎨 Interactive Dashboards: Real-time visualization with QuickSight
🚀 CI/CD Integration: Automated deployments with AWS CodeBuild
🔒 Secure by Design: IAM-based security across all components
📈 Scalable: Auto-scaling EMR clusters for varying workloads

🛠️ Tech Stack
AWS Services

Amazon Kinesis Data Streams: Real-time data ingestion
Amazon EMR: Managed Hadoop/Spark cluster
Amazon Redshift: Cloud data warehouse
Amazon S3: Object storage for scripts and data
AWS CodeBuild: CI/CD automation
Amazon QuickSight: Business intelligence and visualization
AWS IAM: Security and access management

Frameworks & Tools

Apache Airflow: Workflow orchestration
Apache Spark (PySpark): Distributed data processing
Python: Data generation and scripting
SQL: Data querying and transformation

📦 Prerequisites

AWS Account with appropriate permissions
Python 3.8+
Apache Airflow 2.x
Basic knowledge of:

AWS services
Apache Spark
SQL
Python



Required AWS Permissions

EMR: Full access for cluster management
Kinesis: Read/Write access to data streams
Redshift: Admin access for database operations
S3: Read/Write access to buckets
IAM: Role creation and policy attachment
CodeBuild: Project management
QuickSight: Dashboard creation and management

📁 Project Structure
food-data-pipeline/
├── dags/
│   ├── food_data_pipeline_dag.py
│   └── config/
│       └── redshift_config.py
├── scripts/
│   ├── data_generator/
│   │   └── mock_food_data_generator.py
│   ├── spark_jobs/
│   │   └── food_data_processor.py
│   └── redshift/
│       ├── create_dim_tables.sql
│       └── create_fact_tables.sql
├── config/
│   ├── emr_config.json
│   └── kinesis_config.json
├── buildspec.yml
├── requirements.txt
└── README.md
🚀 Setup Instructions
1. Clone the Repository
bashgit clone https://github.com/yourusername/food-data-pipeline.git
cd food-data-pipeline
2. Set Up AWS Resources
Create S3 Buckets
bashaws s3 mb s3://food-pipeline-scripts
aws s3 mb s3://food-pipeline-data
Create Kinesis Data Stream
bashaws kinesis create-stream \
    --stream-name food-data-stream \
    --shard-count 2
Create Redshift Cluster
bashaws redshift create-cluster \
    --cluster-identifier food-data-warehouse \
    --node-type dc2.large \
    --master-username admin \
    --master-user-password YourPassword123 \
    --number-of-nodes 2
Create EMR Cluster
bashaws emr create-cluster \
    --name "Food Data Processing Cluster" \
    --release-label emr-6.10.0 \
    --applications Name=Spark Name=Hadoop \
    --ec2-attributes KeyName=your-key-pair \
    --instance-type m5.xlarge \
    --instance-count 3 \
    --use-default-roles
3. Configure IAM Roles
Create IAM roles for:

EMR service role
EMR EC2 instance profile
Airflow execution role
Redshift access role

4. Set Up Apache Airflow
bash# Install Airflow
pip install apache-airflow[amazon,postgres]

# Initialize Airflow database
airflow db init

# Create admin user
airflow users create \
    --username admin \
    --firstname Admin \
    --lastname User \
    --role Admin \
    --email admin@example.com
5. Configure Environment Variables
Create a .env file:
bashAWS_REGION=us-east-1
KINESIS_STREAM_NAME=food-data-stream
EMR_CLUSTER_ID=j-XXXXXXXXXXXXX
REDSHIFT_HOST=your-cluster.region.redshift.amazonaws.com
REDSHIFT_DATABASE=fooddata
REDSHIFT_USER=admin
REDSHIFT_PASSWORD=YourPassword123
S3_SCRIPTS_BUCKET=food-pipeline-scripts
S3_DATA_BUCKET=food-pipeline-data
6. Deploy with CodeBuild
bash# Create CodeBuild project
aws codebuild create-project \
    --name food-pipeline-build \
    --source type=GITHUB,location=https://github.com/yourusername/food-data-pipeline.git \
    --artifacts type=S3,location=food-pipeline-scripts \
    --environment type=LINUX_CONTAINER,image=aws/codebuild/standard:5.0,computeType=BUILD_GENERAL1_SMALL \
    --service-role your-codebuild-role-arn
🔄 Pipeline Flow
End-to-End Data Flow

Data Generation

Python mock generator creates realistic food industry data
Data includes orders, inventory, sales, and customer information


Data Ingestion

Generator pushes data to Kinesis Data Stream
Stream buffers data for real-time processing


Orchestration

Airflow DAG triggers based on schedule or event
Airflow submits EMR step for Spark job execution


Data Processing

EMR reads data from Kinesis stream
PySpark performs:

Data deduplication
Schema validation
Data transformation
Aggregations




Data Loading

Dimension tables loaded from S3 (via CI/CD)
Fact tables loaded from EMR processing results
Data inserted into Redshift tables


Visualization

QuickSight connects to Redshift
Dashboards display real-time analytics
Automated report generation



⚙️ Configuration
Airflow DAG Configuration
pythondefault_args = {
    'owner': 'data-team',
    'depends_on_past': False,
    'start_date': datetime(2024, 1, 1),
    'email_on_failure': True,
    'email_on_retry': False,
    'retries': 2,
    'retry_delay': timedelta(minutes=5),
}

dag = DAG(
    'food_data_pipeline',
    default_args=default_args,
    description='Real-time food data processing pipeline',
    schedule_interval='@hourly',
    catchup=False
)
EMR Spark Job Configuration
pythonspark_config = {
    'spark.streaming.stopGracefullyOnShutdown': 'true',
    'spark.streaming.backpressure.enabled': 'true',
    'spark.sql.adaptive.enabled': 'true',
    'spark.dynamicAllocation.enabled': 'true'
}
📊 Usage
Start Data Generation
bashpython scripts/data_generator/mock_food_data_generator.py \
    --stream-name food-data-stream \
    --records-per-batch 100 \
    --interval 5
Trigger Airflow DAG
bash# Via CLI
airflow dags trigger food_data_pipeline

# Via UI
# Navigate to http://localhost:8080 and enable the DAG
Query Redshift
sql-- Example: Get daily sales summary
SELECT 
    date_key,
    product_name,
    SUM(quantity) as total_quantity,
    SUM(revenue) as total_revenue
FROM fact_sales fs
JOIN dim_product dp ON fs.product_key = dp.product_key
JOIN dim_date dd ON fs.date_key = dd.date_key
GROUP BY date_key, product_name
ORDER BY date_key DESC;
View QuickSight Dashboard

Log into AWS QuickSight console
Navigate to Dashboards
Select "Food Data Analytics Dashboard"
View real-time metrics and trends

📈 Monitoring
CloudWatch Metrics

Kinesis stream metrics (incoming records, iterator age)
EMR cluster metrics (CPU, memory, tasks)
Redshift query performance
Airflow task success/failure rates

Airflow Monitoring
bash# Check DAG status
airflow dags list

# View task logs
airflow tasks logs food_data_pipeline task_name 2024-01-01
EMR Monitoring

Access EMR cluster UI at port 8088
Monitor Spark jobs and stages
Check YARN resource manager

🤝 Contributing
Contributions are welcome! Please follow these steps:

Fork the repository
Create a feature branch (git checkout -b feature/AmazingFeature)
Commit your changes (git commit -m 'Add some AmazingFeature')
Push to the branch (git push origin feature/AmazingFeature)
Open a Pull Request

Coding Standards

Follow PEP 8 for Python code
Add docstrings to functions and classes
Write unit tests for new features
Update documentation as needed



🙏 Acknowledgments

AWS Documentation
Apache Airflow Community
Apache Spark Community

📞 Support
For questions or issues:

Open an issue in GitHub
Email: prajwalkv620@gmail.com
