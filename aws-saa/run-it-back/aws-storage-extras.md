# AWS Storage Extras

Physical devices to collect, transfer and process data at edges, in and out of AWS.

Snowcone - TB (terabytes)\
Snowball - PB (petabytes)

Transferring hugee amounts of data might be slow, limited bandwidth, these devices can be ordered to site, transfer data to it and send to AWS server where they transfer to S3

Edge Computing - process data at edge location (ship on the sea) where limited internet, bring these snowball devices to perform computations (preprocess, MLF, transcoding)

<div align="left"><figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt="" width="405"><figcaption><p>To transfer to Glacier, use the S3 lifecycle policy can't transfer directly</p></figcaption></figure></div>

### Amazon FSx (3rd party File Systems)

#### FSx for Windows

Fully managed WIndows file system share drive, SMB and NTFS supported along with AD, ACLs and user quotas\
Can be mounted on Linux EC2, supports Microsoft DFS (Distributed File System)

Scale up to 10s of GB/s, millions of IOPS, 100s PB of data (both SSD and HDD)\
Can be accessed on-premise (VPN or Direct Connect), can be Multi-AZ

Backed up to S3 daily

#### FSx for Lustre (Linux + Cluster - ML, HPC)

Scales up to 100s GB/s, millions of IOPS, sub-ms latencies (SSD and HDD support)\
Can 'read S3' as file system through FSx and write output of computations to S3

Can be used from on-premises servers (VPN or Direct Connect)

Two deployment options

* **Scratch FS** - Temporary, doesn't replicate data, high burst (Short-term processing)
* **Persistent FS** - Long-term, replicates data in same AZ, replace failed files in minutes (Sensitive data)

#### FSx for NetAPP ONTAP (Unified Storage Operating System)

Compatible with NFS, SMB, iSCI - move workloads running on ONTAP or NAS to AWS

Works with Linux, Windows, MacOS, VMWare, EC2, ECS and EKS

Does - Snapshots, replication, low-cost, compression and data de-duplication\
Balances storage automatically (shrink/grow) and offers point-in-time instantaneous cloning (for testing new workloads)

#### FSx for OpenZFS

Compatible with NFS (v3, 4, 4.1, 4.2) and move workloads running on ZFS to AWS. Works with everything same as ONTAP along with the cloning feature

No replication and de-duplication tho\
Up to 1,000,000 IOPS with < 0.5ms latency

### Hybrid Cloud

S3 is proprietary, expose S3 data on-premise via **AWS Storage Gateway**

<div align="left"><figure><img src="../../.gitbook/assets/image (10) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

Gateway bridge on-premise and cloud data

For backup, tiered storage, on-premise cache & low-latency file access

Types (Put diagrams after this)

* S3 File Gateway - S3 access via NFS and SMB, recent data cached in gateway, bucket acess using IAM, integrate SMB with AD
* FSx File Gateway - Compatible w SMB, NTF, AD and offers native access to FSx for Windows File Server
* Volume Gateway - Block storage using iSCI backed by S3, backed by EBS snapshots which can restore on-premise.\
  Cached Volumes offer low latency to recent data\
  Stored Volumes offer entire dataset on premise and schedules backups to S3
* Tape Gateway - Backup using virtual tape libraries (VTL) backed by S3 and Glacier

To use Storage Gateway, one needs on-premise virtualization or a hardware appliance from Amazon - useful for daily NFS backups in small data centers

<div align="left"><figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt="" width="491"><figcaption><p>S3</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt="" width="479"><figcaption><p>FSx</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt="" width="454"><figcaption><p>Volume</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt="" width="452"><figcaption><p>Tape</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt="" width="563"><figcaption><p>High Level View</p></figcaption></figure></div>

### Transfer Family

Help transfer in and out of S3 or EFS using FTP/FTPS/SFTP, integrate using existing auth schemes

Scalable and highly available (multi-AZ)\
Pay per hour + data transferred

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt="" width="499"><figcaption></figcaption></figure></div>

### DataSync

Facilitates data transfer from AWS to AWS/Non-AWS storage services (Non requires an agent)

Can perform replication tasks as necessary and preserve file perms and metadata\
One agent can use upto 10 Gbps

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt="" width="542"><figcaption><p>DataSync bw AWS and NFS/SMB server</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt="" width="461"><figcaption><p>DataSync bw AWS services</p></figcaption></figure></div>





