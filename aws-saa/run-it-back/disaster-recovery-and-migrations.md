# Disaster Recovery & Migrations

* RPO - Last point from which you can restore normal data
* RTO - Latest point by which you gotta get back up

<div align="left"><figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure></div>

### DR Strategies

* Backup and Restore (High RPO)
* Pilot Light - small version of app (critical parts) always running in cloud
* Warm Standby - full system up and running but at min size, scale to production load on disaster
* Hot Site - Low RTO & expensive, full production scale running on AWS and on premise

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Disaster Recovery Tips</p></figcaption></figure></div>

## DMS

Database Migration Service, need to create EC2 to perform replications tasks\
Supports:

* Homogeneous migrations: Oracle to Oracle
* Heterogeneous migrations: MS SQL to Aurora

Continuous Data Replication using CDC

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1).png" alt="" width="548"><figcaption><p>DMS Sources and Targets</p></figcaption></figure></div>

### Schema Conversion Tool (SCT)

The engine conversion of DB Schema happens via this, data is transferred via DMS - use a combo of both, supports Multi-AZ deployment

No need for SCT if migrating same DB engine (eg. On-premise PostgreSQL to RDS PostgreSQL)

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1).png" alt="" width="527"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (4) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

## AWS Backup

Fully managed and centralized backup service, supports cross-region and cross-account

PITR for supported services, on-demand and scheduled backups

Create backup policies called backup plans - backup frequency, backup window, transition to cold storage, retention period

Automatically backed up to S3

### Backup Vault Lock

WORM state for all backups stored, even root user cannot delete backups if enabled

### AWS Application Discovery Service

Plan migration projects by gathering information about on-premises data centers

**Agentless Discovery** (AWS Agentless Discovery Connector) \
• VM inventory, configuration, and performance history such as CPU, memory, and disk usage \
**Agent-based Discovery** (AWS Application Discovery Agent) \
• System configuration, system performance, running processes, and details of the network connections between systems

AWS Migration Hub - dashboard having the info about the above mentioned

### Application Migration Service (MGN)

**SMS (Server Migration Service) -** Incremental replication of on-premises live servers to AWS

This replaces SMS

Lift-and-shift (rehost) solution which simplify migrating applications to AWS \
• Converts your physical, virtual, and cloud-based servers to run natively on AWS \
• Supports wide range of platforms, Operating Systems, and databases \
• Minimal downtime, reduced costs

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### VMware Cloud on AWS

Migrate VMware based workloads to AWS, run production workloads across a VMware hybrid cloud environment.
