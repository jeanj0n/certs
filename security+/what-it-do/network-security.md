# Network Security

### Switches

Mac Flooding - Attempt to override the memory for storing mac addresses, if success then act like hub and forward it to everyone connected (Fail-Open)

Mac Spoofing - Pretend to have another user's MAC address , combine with ARP spoofing

**Network Zones**

![](<../../.gitbook/assets/image (27) (1).png>)

Extranet - Special DMZ for partner organization to access over wide area network

Bastion Host - host/server in DMZ which are not configured with any services that run on a local network

Jumpbox - hardened server providing access to other hosts within DMZ

admin connect to jumpbox -> jumpbox connect to dmz

the only box having perms from internal network to dmz so be very careful

Network Access Control (NAC) - Scan devices to determine current state prior to allowing access to network, **hardware or software solution, 802.1x port-based NAC**

if it fails inspection, placed into digital quarantine

* Persistent Agent - software installed on device, requesting access to network
* Non persistent - scans in remote, or is installed and removed after scan

### VLAN

* Switch Spoofing - attacker configure device to act as a switch and negotiate with trunk link to break out of VLAN
* Double Tagging - When read traffic between VLAN, it reads the outermost tag first so attacker adds an outer tag, to forward to destination and then it does its thing

Telephony - devices providing voice comms to users

War Dialing - hitting up modems to see who respond, like servers back in the day but prevent by callback feature, the modem initiate connection&#x20;

Private Branch Exchange (PBX) - Internal Phone system used in organizations

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

