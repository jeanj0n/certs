---
description: Elastic Block Store (Volume)
---

# EC2 Storage

### EBS

Network drive that can be attached to instances while they run, can allow instances to have data even after termination (Only one instance a time - CCP Level, only one specific availability zone too)

**Snapshots -** can copy screenshots across AZ or region, moving it to an **Archive saves upto 75%** of the cost, plus you get a recycle bin incase of accidental delete upto 1 year max but define

### AMI (Amazon Machine Image)

Template/Image file whcih can custom to our needs to provide faster workflows and setups

Built for specific region but can be copied across, launch other instances from this AMI copy

### Instance Store

Best I/O performance - High-performance hardware disk, good for buffer and temp content

They lose their storage if stopped so backups and replication are our responsibility.

### Volume Types

* gp2 / gp3 (SSD): General purpose SSD volume, balances price and performance for a wide variety of workloads
* io1 / io2 (SSD): Highest-performance SSD volume for mission-critical low-latency or high-throughput workloads
* st1 (HDD): Low cost HDD volume designed for frequently accessed, throughput- intensive workloads
* sc1 (HDD): Lowest cost HDD volume designed for less frequently accessed workloads

**Only gp2/gp3 and io1/io2 can be used as boot volumes**

<figure><img src="../../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

**EBS Multi-Attach only for io1/io2 family upto 16 instances at a time, only cluster-aware file systems)**

To encrypt an unencrypted EBS volume, take a snapshot and when creating a volume again, tick the encryption box and use that from now on

### EFS (Elastic File System)

Managed NFS (network file system) that can be mounted on many EC2 (**Across multiple AZ)**

Uses NFSv4.1 protocol and security groups to control access to EFS

Uses POSIX (Linux) file systems only, no Windows

<div align="left"><figure><img src="../../.gitbook/assets/image (172).png" alt="" width="563"><figcaption></figcaption></figure></div>

#### Storage Tiers

* Standard - for frequently accessed files
* Infrequent access (EFS-IA) - cheap price, pay to retrieve (Enabled with a lifecycle policy)

#### Availability

* Standard - Multi-AZ, great for prod
* One Zone - One AZ, great for dev, backup enabled by default, compatible with IA

### EBS vs EFS

EBS - one instance (except multi-attach io1/io2), locked to a specific AZ\
&#x20;          gp2: IO increases if the disk size increases\
&#x20;          gp3 & io1: can increase IO independently

EFS - mounting 100s of instances across AZ\
&#x20;          share website files (WordPress, example workflow)

<div align="left"><figure><img src="../../.gitbook/assets/image (173).png" alt="" width="241"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (175).png" alt="" width="241"><figcaption></figcaption></figure></div>
