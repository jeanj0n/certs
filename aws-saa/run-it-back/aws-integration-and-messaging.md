# AWS Integration and Messaging

## SQS (Queue)

<div align="left"><figure><img src="../../.gitbook/assets/image (15).png" alt="" width="556"><figcaption><p>synchronous apps may not perform well w variable workloads, decouple to scale independently</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt="" width="499"><figcaption><p>unlimited throughput, 256kb message limit, duplicates and out of order messages (SendMessageAPI)</p></figcaption></figure></div>

Can use CloudWatch metric to trigger autoscaling of EC2 'Consumers'

### SQS Security

Encryption: In-flight HTTPS API, at-rest KMS, client-side as per clients choice

Access Controls: IAM policies regulate access to SQS API

SQS Access Policies (Similar to S3): Cross-account access to queue, allow other services to write to queue

### Visibility Timeout

30 seconds default, once consumer polls a message not visible to others till timeout over after which it becomes available again in SQS if not deleted\
Consumer can call **ChangeMessageVisibility** API to get more time.

Long Polling - Consumer wait in between 'polls' if there are no messages in queue (20 sec preferred) via **WaitTimeSeconds**

### FIFO Queue (Ordering Messages)

300 messages without batching, 3000 with. No duplicates

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption><p>decoupling bw application tiers</p></figcaption></figure></div>

## SNS (Notification) Pub/Sub

Receivers (Subscriptions) listen to Topics. One topic max 12.5 mil subs, One sub max 100k topics\
Integrate many AWS services w SNS for notifications

Publish to SNS via Topic Publish (SDK) or DIrect Publish (Mobile SDK)

Same security policies as SQS

<div align="left"><figure><img src="../../.gitbook/assets/image (104).png" alt="" width="500"><figcaption><p>These services can fetch notifications from SNS (No Kinesis Data Stream)</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (106).png" alt="" width="545"><figcaption><p>These services can send data to SNS to publish notificaitons</p></figcaption></figure></div>

### SNS+SQS: Fan Out&#x20;

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt="" width="374"><figcaption><p>Easily manage SQS instead of manual config (setting up new queues), fully decoupled, cross-region delivery</p></figcaption></figure></div>

#### SNS FIFO - Ordering (Message Group ID aka batching?) and Deduplication (Deduplication ID within the group)

Can have Standard and FIFO SQS as subscribers

Use FIFO too in above figure for ordering + deduplication

<div align="left"><figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt="" width="527"><figcaption><p>Use JSON policy to filter messages sent to subscriptions</p></figcaption></figure></div>

## Kinesis

* Data Streams: capture, process, and store data streams
* Data Firehose: load data streams into AWS data stores
* Data Analytics: analyze data streams with SQL or Apache Flink
* Video Streams: capture, process, and store video streams

<div align="left"><figure><img src="../../.gitbook/assets/image (99).png" alt="" width="563"><figcaption><p>Kinesis Data Streams, Kinesis Producer Library (KPL); Kinesis Client Library (KCL)</p></figcaption></figure></div>

### Kinesis Data Streams

Modes

* Provisioned:  manually scale number of shards or API, 1MB/s (1000 records) in and 2MB/s out, pay per shard provisioned per hour
* On-demand: Automatically scale based on throughput over 30 days, default is 4MB/s, pay per stream per hour and data in/out per GB

<div align="left"><figure><img src="../../.gitbook/assets/image (100).png" alt="" width="285"><figcaption><p>Access Control via IAM, monitor API calls via Cloudtrail</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (101).png" alt="" width="563"><figcaption><p>Can process not just collect here also watch extra producers and AWS DESTINATIONS ARE MUST</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (107).png" alt="" width="563"><figcaption><p>All key points watch</p></figcaption></figure></div>

### Ordering Data&#x20;

Use "Partition Key" having a distinct id so corresponding data will always go to same shard

In SQS no ordering, SQS FIFO without group ID messages processed in that order only with one consumer use ID to scale similarly to Kinesis

<div align="left"><figure><img src="../../.gitbook/assets/image (102).png" alt="" width="563"><figcaption><p>Watch Standard vs Enhanced fan-out in Kinesis</p></figcaption></figure></div>

### Amazon MQ

Used to support open protocols apart from the proprietary SQS/SNS, has features of both

Doesn't scale like SQS/SNS but runs on servers and Multi-AZ with failover

<div align="left"><figure><img src="../../.gitbook/assets/image (103).png" alt="" width="502"><figcaption><p>High Availability achieved by Amazon MQ</p></figcaption></figure></div>
