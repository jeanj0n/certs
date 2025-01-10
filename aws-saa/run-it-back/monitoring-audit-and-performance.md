---
description: Logs and metrics are two entities surprisingly
---

# Monitoring, Audit and Performance

## CloudWatch

### Metrics

Metrics provided for every service in AWS, belong to namespaces\
Upto 30 dimensions per metric, timestamps included

### Metric Streams

Stream CloudWatch metrics to a destination of your choice, with near-real-time delivery \
\- Kinesis Data Firehose (and then its destinations)\
Filter metrics to only stream a subset.

### Logs

Log groups: arbitrary name, usually representing an application\
Log stream: instances within application / log files / containers

Send logs to S3, Kinesis (Firehose too), Lambda, OpenSearch - encrypted by default

### Logs Insights

Search and analyze data in CloudWatch logs, query multiple groups diff AWS accounts - query engine, not real time

Export logs to S3, upto 12 hours 'CreateExportTask' - use Log Subscriptions cus not real time&#x20;

Subscription Filter – filter which logs are events delivered to your destination

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>Cross-Account subscription can send log events to resources in diff AWS account</p></figcaption></figure></div>

### Logs Agent & Unified Agent

By default, no logs sent from EC2 to CloudWatch\
For virtual servers (EC2 instances)

* Logs Agent - Old version, can only send to CloudWatch logs
* Unified Agent - Additional metrics such as RAM, send to CW logs and uses SSM Parameter store

### Alarms

#### States

* OK
* ALARAM
* INSUFFICIENT\_DATA

Period - time to evaluate the metric, 10-30-60 seconds high resolution metrics

Target EC2, ASG, SNS etc.

Composite Alarms - single metric monitoring states of multiple other alarms, aggregated by AND and OR conditions

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt="" width="359"><figcaption></figcaption></figure></div>

Alarms can be created based on CloudWatch Logs Metrics Filters

### EventBridge

* Schedule cron jobs
* Event pattern - rules react to a service doing something
* Trigger Lambda functions, send SQS/SNS

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption><p>Event buses can be accessed by other AWS accounts using Resource-based Policies</p></figcaption></figure></div>

You can archive events sent to an event bus and replay these archived events

This can analyze events in bus and infer schema - Schema Registry can let the bus know in advance how the data is structured

#### Resource-based Policy

Manage perms for specific Event bus - aggregate all events to one AWS account/region

### Container Insights

Summarize metrics and logs for ECS, EKS, Kubernetes, Fargate (In Amazon EKS and Kubernetes, CloudWatch Insights is using a containerized version of the CloudWatch Agent to discover containers)

* Lambda Insights - Monitoring and troubleshoot for apps on Lambda
* Contributor Insights - Analyze log data and create time series that display contributor data (Top N contributors, heaviest network user etc.)
* Application Insights - Automated dashboards that show potential problems with monitored applications, powered by SageMaker

## CloudTrail

History of events / API calls made within your AWS Account - governance, compliance and audit\
A trail can be applied to All Regions (default) or a single Region

### Events

* Management Events (Default) - Performed on resources in AWS account (eg. config security), separate read from write (modify resources) events
* Data Events - Doesn't log by default (high volume), S3 object-level activity, Lambda&#x20;
* Insight Events (Behavior based) - detect unusual activity, analyze write events

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption><p>Event sent to S3, EventBridge for automation</p></figcaption></figure></div>

Events are stored for 90 days, for further times send to S3 and use Athena

## Config

Record configurations and changes over time - auditing and recording compliance

Per-region service, receive alerts, can be aggregatd across regions and accounts

**Config rules used to evaluate/trigger for each config change/interval, does not prevent anything from happening**

### Remediations

Automate remediation of non-compliant resources using SSM Automation Documents, set Remediation Retries if resource is still non-compliant

### Notifications

Use EventBridge to trigger notifications **when AWS resources are noncompliant**, send **configuration changes and compliance state notifications** to SNS (pretty much the same)
