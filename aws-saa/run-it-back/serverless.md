# Serverless

No need to manage the servers, they still be running in the background

<div align="left"><figure><img src="../../.gitbook/assets/image (207).png" alt="" width="398"><figcaption></figcaption></figure></div>

## Lambda

Surprisingly, C is not supported but C#, Python, Rust, Java all are\
• Free tier of 1,000,000 AWS Lambda requests and 400,000 GBs of compute time

Lambda Container Image to run serverless but just use ECS/Fargate

### Limits

Max 10 GB RAM

First 1,000,000 requests are free OR 400,000 GB seconds of compute time per month

#### Execution

* Memory allocation: 128 MB – 10GB (1 MB increments)
* Maximum execution time: 900 seconds (15 minutes)
* Environment variables (4 KB)
* Disk capacity in the “function container” (in /tmp): 512 MB to 10GB
* Concurrency executions: 1000 (can be increased)

#### Deployment

* Lambda function deployment size (compressed .zip): 50 MB
* Size of uncompressed deployment (code + dependencies): 250 MB
* Size of environment variables: 4 KB

#### SnapStart

Invoke function from pre-initialized state instead of full init process, producing 10x gains

### Edge Functions

Serverless, attached to Cloudfront centers

<div align="left"><figure><img src="../../.gitbook/assets/image (210).png" alt="" width="82"><figcaption><p>see who can control what</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (208).png" alt="" width="411"><figcaption><p>both are provided by Cloudfront only, NO MIX UP W API</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (209).png" alt="" width="415"><figcaption><p>CF for more web stuff and Lambda for more processing type tasks</p></figcaption></figure></div>

### VPC with Lambda

By default, Lambda is launched outside VPC, no resources inside VPC can be accessed.\
Define VPC ID, subnets, security groups and it will create an ENI (Elastic Network Interface)

However, direct access to resources is also a problem (load management)\
Use RDS Proxy to interact with Lambda (IAM, scalability, availability), Lambda should be in VPC using above method (ENI)

### Processing Data Events

Invoke lambda from within DB instance, allow outbound traffic from DB and have perms to run lambda (Lambda Resource-based & IAM policy)\
Supported by RDS Event Notifications and Aurora MySQL, **you have all the info**

RDS Event notifications give notis about DB instance itself, no info about the data tho\
Near real-time (5 minutes)\
Subscribed to the following: DB instance, DB snapshot, DB Parameter Group, DB Security Group, RDS Proxy, Custom Engine Version

## DynamoDB

Fully managed, highly available NoSQL DB with replication across multiple AZs\
Standard and Infrequent Access (IA) Table class depending on frequent usage of data

Made of tables, Primary key (Partition and Sort key, like primary and sub-primary)\
Each table infinite items (rows), attributes (columns) can be added at any time.\
Max size of an item - 400 kb

* Provisioned (default) - Read Capacity Units (RCU) & Write Capacity Units (WCU), auto-scaling possible
* On-demand - No capacity planning, perfect for unpredictable workloads, sudden spikes

#### DAX (DynamoDB Accelerator)

Cache for DynamoDB, 5 minutes TTL - use for individual objects, queries (aggregate outputs use ElastiCache)

### Stream Processing

Ordered stream of item-level modifications (create/update/delete) in a table

* DynamoDB Streams - 24 hours retention, limited consumers, process using Lambda or DDB Stream Kinesis adapter
* Kinesis Data Streams (Newer) - 1 year retention, high consumers, processed using a lot more (Refer consumers in Kinesis, they do the 'processing')

<div align="left"><figure><img src="../../.gitbook/assets/image (211).png" alt="" width="453"><figcaption><p>DynamoDB streams</p></figcaption></figure></div>

#### Global Tables

Low latency in multiple regions, Active-Active replication, can read & write to table in any region\
DDB Streams is a pre-requisite

### Backup

#### PITR (Point in time recovery)

Last 35 days, recovery process creates a new table

#### On-demand

Full backups until explicitly deleted, can't configure in AWS backup (enables cross-region copy) - recovery here also creates new table

### Integration with S3

* Export (need PITR) - doesn't affect read capacity, perform analysis on S3 data before importing back, export in JSON or ION format
* Import - CSV, JSON, ION format, doesn't consume write capacity, create new table - import errors logged in CloudWatch

## API Gateway

AWS Lambda + API Gateway - Serverless

#### **Integrations**

* Lambda - Easy way expose REST API backed by Lambda
* HTTP - internal on premise, ALB, caching, user auth etc.
* AWS Service - Client comm w AWS service via API with no direct access

#### Endpoint Types

* Edge-optimized (default) - for global clients, route through CloudFront, **API Gateway still in one region**&#x20;
* Regional - for clients in same region
* Private - Access from VPC or ENI, use resource policy to define access

#### Security

* User Auth - via IAM (Internal), Cognito (External users, web & mobile app) or custom
* Custom Domain Name HTTPS - through ACM (AWS Certificate Manager), set up record in Route 53, edge-optimized certificate in **us-east-1** & regional certificate in that of API Gateway

<div align="left"><figure><img src="../../.gitbook/assets/image (213).png" alt=""><figcaption><p>Lambda has NO CACHING FEATURE</p></figcaption></figure></div>

### Step Function

Visual workflow to orchestrate Lambda functions - sequence, parallel, conditions, timeouts, error handling

## Cognito

User Identity management for web/mobile application

* Cognito User Pools - Sign in for app users, integrate w API and ALB
* Cognito Identity Pools (Federated Identity) - Give AWS credentials so users can access AWS resources directly, integrate w user pools as Identity provider (IP) : provide default IAM roles or custom for fine grained control

<div align="left"><figure><img src="../../.gitbook/assets/image (212).png" alt="" width="414"><figcaption><p>Identity Pools</p></figcaption></figure></div>
