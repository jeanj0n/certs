# Hardening

Config an OS securely, create policies and remove unnecessary stuff, minimize risk as a result

**Least functionality -** only essential apps and services set by admin

How monitor for large number of machine spread across? - Use **baseline images (template)**

**SCCM - Micorsoft System Centre Configuration Management**

Application Allowlisting - Like ACL(Access Control List) only those apps in list allowed to operate\
Application Blocklisting - These apps not allowed to operate

Implement using Active Directory for large scale

Trusted OS - Meets requirements set by government and has multi layer security (Windows 7+)

#### Types of Patches

* Security
* Critical
* Service pack - Cumulative of above two
* Windows Update - Non critical problems, additional features
* Driver update

#### Patch Managment

PTIA - Planning, Testing, Implementing and Auditing

**MIcrosoft Baseline Security Analyzer** - identify security mistakes

Large orgs use update server for everyone to update instead of windows updates

**SCCM** - manage patches, rollouts etc.

#### Group Policies

gpedit for Windows, AD more advanced group policy editor

Group policy outline(GPO) / baseline helps load in one procedure

**Baselining -** Measuring changes in network, hardware and software env

#### File Systems

Different file systems have varying levels of security

NTFS (New Technology File System) - default for Windows, better logging, encryption, partition and file sizes than FAT32

You can convert FAT32 to NTFS w zero data loss

Linux - ext4, Apple APSX

## Threats to Virtual Machines

* VM Escape - attacker break out of VM by directly interacting with the hypervisor
* Data Remnant - when VM decommissioned the data remain in physical server and remains there
* Live Migration - Man in the middle attack if not encrypted
* Virtualization Sprawl - when Vm created, used without proper management or admin&#x20;

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>



