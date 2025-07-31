# Monitoring and Auditing

Criteria for monitoring

* Signature - traffic analyzed for predetermined attack patterns
* Anomaly - any activity which doesn't follow the norm&#x20;
* Behaviour - act different compared to how it was behaving before

Baselining - Process of measuring changes in networking, hardware, software and apps\
Baseline Reporting - documenting the changes made to the baseline

Security Posture - Risk level to which a system is exposed

Perfmon.exe - Performance monitor for Windows

**Protocol Analyzer** - used to capture and monitor network traffic

* Promiscuous mode - capture all packets regardless of destination MAC address of frames carrying them, even if not addressed to me I still collect and listen
* Non Promiscuous mode - Adapter can only capture packets addressed to itself directly

Can't view traffic without doing **Port Mirroring using SPAN port**

**If no SPAN port possible, use Network tap,** SPAN can increase processing needs

**SNMP (Simple Network Management Protocol)** - TCP/IP protocol aids in monitoring systems

Three components

* Managed Devices - Computers and other devices monitored by use of agents in system
* Agent - Software loaded on device to redirect info to Network management system
* Network management systems - Software run on one or more server to control moitoring of managed devices

<div align="left"><figure><img src="../../.gitbook/assets/image (114).png" alt=""><figcaption></figcaption></figure></div>

Three versions, SNMP v1/2 **insecure** due to use of community strings to access device

SNMP v3 - Provides integrity, authentication and encryption of messages sent over network

Management should be conducted on an out-of-band network to increase security

## Auditing

Detective control, goes hand in hand w logs for exam&#x20;

**Logs** - Windows use three types, use **Event Viewer to access them**

* Security - events such as successful and unsuccessful logins are logged
* System - events of a system such as shutdown or failure
* Application - logs OS and third party apps

### SYSLOG

Client-server model, standardize all logs into one repo and corelate bw diff entities, the standard logging mechanism for distributed systems

Works over **PORT 514 (UDP), New Versions 1468 (TCP)**

**Old** Syslog cons - Run on UDP, no built-in provisions for CIA stuff

New versions - **syslogng or rsyslog**

Overwrite Events - When maximum log size is reached, system can begin overwriting oldest events in log files to make room

WORM (Write Once Read Many) - Ensure logs not tampered with

Different options of SIEM

<figure><img src="../../.gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure>

### SOAR (Security Orchestration, Automation and Response)

Class of security tools help in IR, threat hunting and security configuration - data enrichment

Primarily used for Incident Response

#### Next-gen SIEM - Integrated with SOAR

Playbook - list of actions to perform and detect and respond to specific type of incident

Runbook - automate the playbook, has clear interaction points for human analysis

Eg. the SOAR will use runbook to detect and triage, and at some point alert human who decide next plan of action



