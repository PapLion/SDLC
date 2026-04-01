# Roadmap.sh

## Data Engineer

### Introduction
- What is Data Engineering?
- Data Engineering vs Data Science
- Skills and Responsibilities
- Data Engineering Lifecycle
- Choosing the Right Technologies

### Learn the Basics
- **Programming Skills** (Python is recommended: Python, Java, Scala, Go)
- Data Structures and Algorithms
- Git and GitHub
- Linux Basics
- Networking Fundamentals
- Distributed Systems Basics

### Data Engineering Lifecycle
- **Understand Different Steps:**
  1. Data Generation
  2. Data Storage
  3. Data Ingestion
  4. Data Serving

### Data Generation
- **Sources of Data:** Database, APIs, Logs, Mobile Apps, IoT
- Data Collection Considerations

### Data Storage
- **Database Fundamentals:** Data Normalization, Data Modelling Techniques, CAP Theorem, OLTP vs OLAP, Slowly Changing Dimension - SCD, Horizontal vs Vertical Scaling, Star vs Snowflake Schema
- **Relational Databases:** - Learn SQL, Indexing, Transactions
  - Relational Databases: MySQL, PostgreSQL, MariaDB, Aurora DB, Oracle, MS SQL
- **NoSQL Databases:**
  - Document: MongoDB, ElasticSearch, CosmosDB, CouchDB
  - Column: Cassandra, BigTable, HBase
  - Graph: Neo4j, Neptune
  - Key-Value: Redis, Memcached, DynamoDB

### Data Warehousing
- What is Data Warehouse?
- **Data Warehousing Architectures:**
  - Data Warehouse: Google BigQuery, Snowflake, Amazon Redshift
  - Data Lake: Databricks Delta Lake, Snowflake, Onehouse
  - Data Mart, Data Mesh
  - Other Data Architectures: Data Fabric, Data Hub, Metadata-first Architecture, Serverless Options

### Cloud Computing
- Cloud Architectures
- **Cloud Providers:**
  - AWS: Amazon EC2 (Compute), S3 (Storage), Amazon RDS (Database), Glue (ETL)
  - Azure: Azure Virtual Machines, Azure Blob Storage, Azure SQL Database, Data Factory (ETL)
  - Google Cloud: Compute Engine (Compute), Google Cloud Storage, Cloud SQL (Database), Dataflow

### Data Ingestion
- Types of Data Ingestion: Batch, Hybrid, Realtime
- **Data Pipelines:**
  - ETL Process: Extract Data, Transform Data, Load Data
  - Data Pipeline Tools: Apache Airflow, dbt, Luigi, Prefect

### Cluster Computing & Big Data
- **Cluster Computing Basics:** What is Cluster Computing, Distributed File Systems, HDFS, Job Scheduling, Cluster Management Tools (Kubernetes, Apache Hadoop YARN)
- **Big Data Tools:** Apache Spark
  - Hadoop Ecosystem: HDFS, YARN, MapReduce

### Infrastructure, CI/CD & Monitoring
- **Containers & Orchestration:** Docker, Kubernetes, Google Cloud GKE, AWS EKS
- **CI/CD:** GitHub Actions, Circle CI, GitLab CI, ArgoCD
- **Monitoring:** Prometheus, Datadog, Sentry, New Relic

### Testing & Messaging Systems
- **Testing:** Unit Testing, Integration Testing, End-to-End Testing, Functional Testing, A/B Testing, Load Testing, Smoke Testing
- **Messaging Systems:** What and why use them?, Async vs Sync Communication, Messages vs Streams, Best Practices
  - Common Tools: Apache Kafka, RabbitMQ, AWS SQS, AWS SNS

### Infrastructure as Code - IaC
- Declarative vs Imperative, Idempotency, Reusability, Environmental Management
- Common Tools: Terraform, OpenTofu, AWS CDK, Google Deployment Mgr.

### Data Serving
- Data Analytics (Visit the Data Analyst Roadmap)
- **Business Intelligence:**
  - BI Tools: Microsoft Power BI, Streamlit, Tableu, Looker
- **Reverse ETL:** ETL vs Reverse ETL, Reverse ETL Usecases
  - Tools: Hightouch, Census, Segment

### Security, Governance & Privacy
- **Security:** Authentication vs Authorization, Encryption, Tokenization, Data Masking, Data Obfuscation
- **Data Governance:** Data Quality, Data Lineage, Metadata Management, Data Interoperability, Data Quality
- **Privacy:** Data and AI Regulations (GDPR, ECPA, EU AI Act)

### Machine Learning
- Machine Learning
- MLOps