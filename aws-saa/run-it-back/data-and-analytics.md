# Data & Analytics

## Athena

Serverless query service to analyze data stored in Amazon S3, **built on Presto**

Use Glue to convert your data to Apache Parquet or ORC\
Supports CSV, JSON, ORC, Avro, and Parquet

Columnar data for cost-savings and compress data for smaller retrievals\
Partition datasets to query on virtual columns, prefer large files (> 128MB)

#### Federated Query

Run SQL queries across multiple data sources using Lambda (Data source connector) storing the results back in S3

## Redshift

**OLAP (Online analytical processing)** - 10x better performance than other data warehouses

Columnar in nature instead of row based and parallel query engine\
Can run in provisioned or serverless cluster, faster than Athena due to indexes

### Redshift Cluster

<div align="left"><figure><img src="../../.gitbook/assets/image (215).png" alt=""><figcaption><p>Leader query plan and sort results, Compute perform query and send to leader</p></figcaption></figure></div>

### Snapshots & DR

“Multi-AZ” mode for some clusters

Snapshots - incremental PITR backup stored in S3, restore this into a new cluster \
Automated: every 8 hours, every 5 GB, or on a schedule. Set retention between 1 to 35 days

Configure Amazon Redshift to automatically copy snapshots (automated or manual) of a cluster to another AWS Region

### Loading data&#x20;

<div align="left"><figure><img src="../../.gitbook/assets/image (216).png" alt="" width="462"><figcaption></figcaption></figure></div>

**Redshift Spectrum** - Query data already in S3 without loading it, needs a Redshift cluster available and the Spectrum acts as the middle man

## OpenSearch

Successor to Amazon ElasticSearch, **search any field even partially matches**

* Two modes: managed cluster or serverless cluster
* Security through Cognito & IAM, KMS encryption, TLS
* No native support for SQL, ingestion from Kinesis Data Firehose, AWS IoT, and CloudWatch Logs

## EMR (Elastic MapReduce)

Creates Hadoop clusters (Big Data) made of hundreds of EC2 to analyze and process vast amount of data

* Bundled with Apache Spark, HBase, Presto, Flink
* All provisioning looked after, auto-scaling and integrated with spot instances
* Master node, core node like Leader and compute nodes - Task node (optional) to run spot instance tasks

## QuickSight

Serverless machine learning-powered business intelligence service to create interactive dashboards

* In-memory computation using SPICE if data imported directly
* Enterprise edition can setup Column-Level security (CLS)
* Define Users (standard versions) and Groups (enterprise version), doesn't exist in IAM
* Dashboard is read-only snapshot and preserves config of analysis

## Glue

Extract, transform, and load (ETL) service, fully serverless - converts data to a more desirable form to perform further analysis or store in data warehouse

Cool functions

* Glue Job Bookmarks: prevent re-processing old data
* Glue Elastic Views:
* Glue DataBrew: clean and normalize data using pre-built transformation
* Glue Studio: new GUI to create, run and monitor ETL jobs in Glue
* Glue Streaming ETL (built on Apache Spark Structured Streaming): compatible with Kinesis Data Streaming, Kafka, MSK (managed Kafka)

## Lake Formation

Data lake = central place to have all your data for analytics purposes

Fully managed service, aggregate and transform data from multiple sources\
**On top of AWS Glue**, Fine-grained Access Control for your applications (row and column-level)

Implement centralized security permissions instead of at individual data sources

## Kinesis Data Analytics (SQL)

Real-time analytics on Kinesis Data Streams & Firehose using SQL - fully managed, serverless

### Kinesis Data Analysis for Apache Flink

Flink - Java, Scala or SQL app to process the streaming data

Does not read from Firehose (use Kinesis Analytics for SQL instead)

<div align="left"><figure><img src="../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure></div>

### Amazon MSK (Managed Streaming for Apache Kafka)

Alternative to Amazon Kinesis, fully managed Kafka on AWS - can run serverless\
Automatic recovery from common Kafka failures, data stored on EBS volume as long as we want

Consumers include Kinesis Flink, Glue, Lambda and apps running on EC2, ECS

<div align="left"><figure><img src="../../.gitbook/assets/image (219).png" alt=""><figcaption><p>MSK creates &#x26; manages Kafka brokers nodes &#x26; Zookeeper nodes, multi-AZ (up to 3 for HA)</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (220).png" alt="" width="422"><figcaption><p>what was data streams vs firehose again?</p></figcaption></figure></div>
