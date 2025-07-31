---
description: Read from notes and do social engineering here as well
---

# Planning for the Worst

<div align="left"><figure><img src="../../.gitbook/assets/image (150).png" alt="" width="375"><figcaption></figcaption></figure></div>

### Data Redundancy

* RAID 0 - Provides data striping across multiple disks to increase performance
* RAID 1 - Provides redundancy by mirroring the data identically on two hard disks
* RAID 5 - **3 disk drives,** provides redundancy by striping data and parity data across the disk drives
* RAID 6 - **4 disk drives,** provides redundancy by striping and **double** parity data across the disk drives
* RAID 10 - Creates a striped RAID of two mirrored RAIDs (combines RAID 1 & RAID 0)

<img src="../../.gitbook/assets/image (151).png" alt="" data-size="original">

<div align="left"><figure><img src="../../.gitbook/assets/image (152).png" alt="" width="346"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (153).png" alt="" width="369"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (154).png" alt="" width="361"><figcaption></figcaption></figure></div>

**Business Continuity Plan (BCP)** - continue the delivery of products or services at pre-defined acceptable levels following a disruptive incident

### Business Impact Analysis

* Maximum Tolerable Downtime (MTD) - The longest period of time a business can be inoperable without causing irrevocable business failure
* Recovery Time Objective (RTO) - **\[MAY]** The length of time it takes after an event to resume normal business operations and activities
* Work Recovery Time (WRT) - The length of time in addition to the RTO of individual systems to perform reintegration and testing of a restored or upgraded system following an event
* Recovery Point Objective (RPO) - The longest period of time that an organization can tolerate lost data being unrecoverable
* Mean Time To Repair (MTTR) - **\[FACT]** Measures the average time it takes to repair a network device when it breaks
* Mean Time Between Failures (MTBF) - Measures the average time between failures of a device
