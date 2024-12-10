# Databases

## Types

* RDBMS (= SQL / OLTP): RDS, Aurora – great for joins
* NoSQL database – no joins, no SQL : DynamoDB (\~JSON), ElastiCache (key / value pairs), Neptune (graphs), DocumentDB (for MongoDB), Keyspaces (for Apache Cassandra)
* Object Store: S3 (for big objects) / Glacier (for backups / archives)
* Data Warehouse (= SQL Analytics / BI): Redshift (OLAP), Athena, EMR
* Search: OpenSearch (JSON) – free text, unstructured searches
* Graphs: Amazon Neptune – displays relationships between data, available across 3 AZ, with up to 15 read replicas
* Ledger: Amazon Quantum Ledger Database
* Time series: Amazon Timestream

{% embed url="https://medium.com/awesome-cloud/aws-difference-between-amazon-aurora-and-amazon-rds-comparison-aws-aurora-vs-aws-rds-databases-60a69dbec41f" %}
Aurora only MySQL and PostgreSQL whereas RDS support all RDBMS
{% endembed %}

### DocumentDB

AWS implementation for MongoDB (NoSQL so DynamoDB works as well) - used to store, query, and index JSON data

> DynamoDB is a fully managed scalable service where you set the upper limit of it's potential.
>
> DocumentDB is a bit more hands on and you have to select the number of instances for the cluster and the instance sizes. This means you would need to keep an eye on their usage / performance but not to the extent of MongoDB.
>
> MongoDB would be the most flexible but also require the most maintenance.

### Neptune Streams

Real-time ordered sequence of every change to graph data\
No duplicates, strict order and accessible in HTTP REST API

### Keyspaces (for Apache Cassandra)

Serverless, Scalable, highly available, fully managed by AWS - offers PITR upto 35 days\
Replicated 3 times across multiple AZ, uses CQL (Cassandra query language)

### QLDB (Deprecated)

Quantum Ledger Database, for recording financial transactions - like blockchain replicate across 3 AZ

### Timestream

Serverless time series DB - optimizes storage based on recency along with real time series analytics (eg. IoT apps sending a lot of data on a daily basis)

<div align="left"><figure><img src="../../.gitbook/assets/image (119).png" alt="" width="415"><figcaption><p>Timestream can be used to leerage further analysis using those tools</p></figcaption></figure></div>









