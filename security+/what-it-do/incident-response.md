# Incident Response

### Phases

* Preparation
* Identification - recognizing the event
* Containment - isolation
* Eradication
* Recovery - data restoration, system repair
* Lessons Learned

### CSIRT (Compter Security Incident Response Team)

* Incident Response Manager&#x20;
* Security Analyst &#x20;
* Triage Analyst&#x20;
* Forensic Analyst&#x20;
* Threat Researcher&#x20;
* Cross-functional Support

CSIRT will be asked for information regarding the estimated downtime, the scope of system and data affected, and other relevant details

### Investigative Data

* SIEM
* Log files
* syslog, syslog-ng, rsyslog
* journalctl - query logs from journald, the systemd logging service on Linux
* nxlog - cross platform and open source, similar to rsyslog
* netflow - network protocol created by cisco , summarizes the data intercepted
* sflow - open source of netflow
* IPfix - backend of service management, helps w billing and accounting

### Forensic Procedures

* Identification
* Collection
* Analysis
* Reporting
* Legal Hold - designed to preserve all relevant information when litigation is reasonably expected to occur\
  **Analysis must be performed wo bias, attorneys try any means to dismiss your findings**

Which of the following is required for evidence to be admissible in a court of law? - **Chain of Custody**

* Timeline - tool that shows the sequence of file system events within a source image in a graphical format

### Data Acquisition

Collect on order of volatility

1. CPU, cache, and register contents (collect first)
2. Routing tables, ARP cache, process tables, kernel statistics
3. Live network connections and data flows
4. Memory (RAM)
5. Temporary file system/swap space
6. Data on hard disk
7. Remotely logged data
8. Data stored on archival media/backups (collect last)



