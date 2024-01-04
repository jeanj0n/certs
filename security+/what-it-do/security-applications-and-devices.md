# Security Applications and Devices

### Software Firewalls

Personal Firewall: software app protect single computer from unwanted internet traffic

Host Firewall: for a host, consider OS also\
&#x20;       Windows - Windows firewall\
&#x20;       MacOS - PF and IPFW     \[pf - packet filter]\
&#x20;       Linux - iptables

Advanced Windows Firewall - wf.msc (for businesses)

Some business prefer hardware/network firewall instead cus this use up resources

IDS (Intrusion Detection System)

Host Based (HIDS) \[Software]

Logs everything it finds sus

Network

All traffic go through this, if it sus it log and report

#### Types of Detection

* Signature - looks for specific string of bytes , if it matches IDS know it sus and flag it&#x20;
* Policy - Depends on specific policy declared (the rules we make)
* Anamoly - Analyze based on past data, anytime out of the norm it do the thing

#### Types of Alerts

* True positive/ negative \[identify real traffic legit and bad traffic fake]
* False positive/ negative \[can't do its job properly]

IDS only log and alert, for prevention need **IPS**&#x20;

Syslog server instead of HIDS cus logs stored in host what if it get compromised, we can recreate the scene using syslog remote so we know how to prevent next time

### Data Loss Prevention (DLP)

Monitor data of a system while in transit, use or rest to detect attempts to steal it, two types - software and hardware

Endpoint DLP System - Software based client that monitors the data in use on a computer and stop file transfer or alert admin

Network DLP System - Perimeter of network check data in transit

Storage DLP - Installed on server to inspect data at rest, ensure encrypted properly and all

Cloud DLP - Cloud Saas to protect data in cloud services

### BIOS

Basic Software Input and Output - Firmware which provides computer instructions on how to accept input and output

Modern machines have UEFI instead (Unified Extensible Firmware Interface)

Securing the BIOS

* Flashing - Update firmware making sure its uptodate
* Set BIOS password - so noone can change boot order and other things
* Configure BIOS boot order - disallow external boot at start
* Disable external ports and services
* Enable secure boot - loads public key from TPM (trusted platform module) chip

### Securing Storage Devices

* Removable media always encrypted, place access controls
* NAS (Network Attached Storage) - Connect direct to company network, use **RAIDS** to ensure availability
* SAN (Storage Area Network) - Network consist of various NAS
* Use proper encryption/authentication and log everything

### Encryption

Hardware and Software based

SED (Self Encrypting Drive) Storage device performs encryption by embedded hardware **EXPENSIVE**

Use Sotware based instead

BitLocker (WIn) and FIleVault (Mac) can encrypt disks in one click

You use a hardware key to perform this encryption and that key is in the TPM

If no TPM, use external drive for key

BitLocker use AES (symmetric 128 or 256 bit)

Bad Performance so instead of disk level use file level encrytion

To speed up encryption use hardware based since computing power right there

HSM (Hardware Security Module) - Physical device to perform crypto process during encryption , connect to usb or network adapter

### EndPoint Protection

An endpoint is any device used to perform actions (eg. our computer, phone etc.)

* Antivirus
* HIDS/HIPS
* EPP (Endpoint Protection Platform) - \[**Signature based**]Software peform multiple task like antivirus, HIDS, firewall etc.
* Endpoint Detection and Response (EDR) - \[**Anomaly based**]collects system data and logs for analysis by a monitoring system
* UEBA (User Endpoint Behaviour Analytics) -  provide automated identification of suspicious activity by user accounts and computer hosts, uses AI **eg. SPLUNKKKK**



#### &#x20;

