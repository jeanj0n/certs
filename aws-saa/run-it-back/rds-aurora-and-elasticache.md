# RDS, Aurora and ElastiCache

### RDS (Relational Database)&#x20;

Create DB in cloud managed by AWS, all the major DB can be used except MongoDB

SQL uses port 3306

<div align="left"><figure><img src="../../.gitbook/assets/image (15) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

#### Storage auto-scaling if free storage<10%, low storage atleast 5 min or 6 hours since last change

<div align="left"><figure><img src="../../.gitbook/assets/image (16).png" alt="" width="563"><figcaption></figcaption></figure></div>

If replica same region then free otherwise PAID

<div align="left"><figure><img src="../../.gitbook/assets/image (17).png" alt="" width="563"><figcaption></figcaption></figure></div>

Multi-AZ keeps same connection string regardless of which database is set up

To go from single to multi AZ, take a snapshot and restore in new region, synchro happen auto

#### RDS Custom

Managed Oracle and Microsoft SQL Server Database with OS and database customization

* RDS: entire database and the OS to be managed by AWS
* RDS Custom: full admin access to the underlying OS and the database (SSH into EC2 instance)

### Aurora

Proprietary from AWS, mad optimized but more expensive, grow in increments of 10gb upto 128tb, upto 15 replicas

Supports MySQL and PostgreSQL

<div align="left"><figure><img src="../../.gitbook/assets/image (18).png" alt="" width="563"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (19).png" alt="" width="563"><figcaption><p>the writer and reader endpoint is how client communicate w this shi the scaling is automatic by AWS</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (20).png" alt="" width="563"><figcaption><p>set up custom endpoints to access subsets faster or for any use case</p></figcaption></figure></div>

#### Aurora Serverless

Automated database instantiation and auto- scaling based on actual usage, no planning needed and pay per second (cost-effective)

#### Global Aurora

* Cross region read replicas - useful for disaster recovery, simple to put in place
* Global database - 1 Primary Region (read / write), upto 5 secondary(read) replication < 1 sec\
  Up to 16 Read Replicas per secondary region, promoting another region has RTO < 1 min

<div align="left"><figure><img src="../../.gitbook/assets/image (21).png" alt="" width="166"><figcaption></figcaption></figure></div>

#### Aurora Machine Learning

Attach ML models to any Aurora service

* Amazon SageMaker (use with any ML model)
* Amazon Comprehend (for sentiment analysis)

#### RDS Backups

* Automated - Daily full backup of the database, transaction logs backed up every 5 min
* Manual - Retention of backup for as long as you want

In a stopped RDS database, you still pay for storage. If you plan on stopping it for a long time, you should snapshot & restore instead

<div align="left"><figure><img src="../../.gitbook/assets/image (22).png" alt="" width="563"><figcaption></figcaption></figure></div>

#### Aurora Cloning

* Faster than snapshot and restore
* Initially, the new DB cluster uses the same data volume as the original DB cluster (fast and efficient – no copying is needed)\
  When updates are made to the new DB cluster data, then additional storage is allocated and data is copied to be separated

<div align="left"><figure><img src="../../.gitbook/assets/image (23).png" alt="" width="259"><figcaption></figcaption></figure></div>

#### RDS Proxy

<div align="left"><figure><img src="../../.gitbook/assets/image (24).png" alt="" width="236"><figcaption></figcaption></figure></div>

* Allows apps to pool and share DB connections established with the database
* Enforce IAM Authentication for DB, and securely store credentials in AWS Secrets Manager
* RDS Proxy is never publicly accessible (must be accessed from VPC)

### ElastiCache

ElastiCache is to get managed Redis or Memcached, involves heavy application code changes\
Provides cache and session management

<div align="left"><figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

#### Cache Security

IAM Authentication for Redis is supported, only used for AWS API-level security

Also use Redis AUTH, set a “password/token” when you create a Redis cluster\
Memcached supports SASL-based authentication (advanced)

**Use Patterns**

* Lazy Loading: all the read data is cached, data can become stale in cache
* Write Through: Adds or update data in the cache when written to a DB (no stale data)
* Session Store: store temporary session data in a cache (using TTL features)

**Redis Sorted sets (Game leaderboards)** guarantee both uniqueness and element ordering\
Each time a new element added, it’s ranked in real time, then added in correct order

### Important Ports

<div align="left"><figure><img src="../../.gitbook/assets/image (25).png" alt="" width="348"><figcaption></figcaption></figure></div>
