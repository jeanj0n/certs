# Security & Encryption

## KMS (Key Management Service)

KMS Keys - new name for KMS Customer Master Key (CMK)

By default, AWS services use symmetric CMK - asymmetric also available, KMS API to use private key

### Types

* AWS Owned key - free, default and managed by AWS
* AWS Managed Key - free (example: aws/rds)
* Customer managed key (created in KMS or imported)

Automatic Key rotation

* AWS-managed KMS Key: automatic every 1 year
* Customer-managed KMS Key: (must be enabled) automatic & on-demand
* Imported KMS Key: only manual rotation possible using alias

<div align="left"><figure><img src="../../.gitbook/assets/image (4) (1).png" alt="" width="417"><figcaption><p>watch perms, region 2 should be able to use Key A if send encrypted</p></figcaption></figure></div>

### Key Policies

* Default policy - If no specific policy mentioned, access given to root aka whole account
* Custom - define users and roles who can use key, who administers key and cross-acc access

### Multi-Region Keys

Identical KMS keys in different AWS Regions, same ID, key material, automatic rotation&#x20;

No need for cross-region API calls

<div align="left"><figure><img src="../../.gitbook/assets/image (5) (1).png" alt="" width="331"><figcaption><p>Keys are managed independantly, NOT GLOBAL</p></figcaption></figure></div>

**(DynamoDB Global Tables/ Global Aurora) and KMS MultiRegion Keys** perform Client-Side Encryption - can protect specific fields even from database admins, only if API access there then sort

### S3 Replication Encryption

Default - Unencrypted objects and objects encrypted with SSE-S3 are replicated

* SSE-C (Customer key) can be replicated
* SSE-KMS need to enable option, specify KMS key, adapt key policy for target key and IAM roles

Watch - AMI Sharing Process Encrypted via KMS (page 665)

## SSM Parameter Store

Secure storage for configuration and secrets - serverless, encryption using KMS and security through IAM

**GetParameters** or **GetParametersByPath** API

* Standard - 4kb max size, no parameter policies
* Advanced - 8kb max size, has parameter policies like TTL or other stuff

## Secrets Manager

Store secrets, rotation every X days and encrypted using KMS

Multi-Region replica supported - read replica kept in sync, can become primary

## ACM (AWS Certificate Manager)

Deploy TLS certificates, both public (free) and private

Used for Load balancers, CloudFront distributions and API gateways - NO EC2

ACM-generated certificates auto-renew 60 days before expiry

Imported certs no automatice renewal - EventBridge send daily expiry events 45 days (can be changed) before expiry

For Edge-optimized API, TLS certificate must be in us-east-1

## WAF

Protect against common web exploits, Web ACL are regional except for CloudFront

<div align="left"><figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption><p>WAF on ALB with fixed IP</p></figcaption></figure></div>

## Shield

Protect from DDoS

### Standard

Free service for every AWS customer - flood, reflection and other layer 3 or 4 attacks covered

### Advanced

Optional but $3000, more sophisticated attacks cover our services with 24/7 support and refunds from higher spikes due to DDoS

## Firewall Manager

Manage rules in all accounts of AWS organisation

WAF rules, Shield Advanced, Security groups for EC2, ALB and ENI, Route 53 all covered at the regional level

Firewall Manager with AWS WAF to use AWS WAF across accounts, accelerate WAF configuration and automate the protection of new resources,

## GuardDuty

Intelligent Threat discovery to protect your AWS Account using ML algos

Uses CloudTrail Events logs, VPC flow logs and DNS logs - can import other logs too except CloudWatch\
Can protect against CryptoCurrency attacks (has a dedicated pattern for it)

## Inspector

Automated security assessments with risk scores and stuff, only for the following

* EC2 - leverage SSM agent, network accessibility and analyze running OS
* Containers - assess ECR
* Lambda - assess functions and software vulnerabilities

## Macie

Data privacy service which identifies PII and uses machine learning to protect sensitive data
