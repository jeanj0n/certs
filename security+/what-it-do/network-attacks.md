# Network Attacks

### DoS types

* Flood Attack - attempt to send more packets than host can handle\
  Ping Flood - ICMP echo requests used\
  Smurf Attack - send ping to subnet broadcast address and they reply to spoofed IP(victim server) \[so every pc in that subnet will reply to the broadcast ping thinking its from server]\
  Fraggle - UDP echo request used, ports 7 and 19\
  SYN flood - multiple 3-way handshake but never complete\
  Xmas Attack - All FIN, ACK URG flag set to 1, packet look like tree in Wireshark
* Ping of Death - Send oversized and malformed packet
* Teardrop Attack - break IP packets into fragments, overrides them and sent to victim
* Permanent DoS - reflash its firmware so a simple reboot can't fix&#x20;
* Fork Bomb - create large number of processes to use available resources

DNS Amplification (DDoS) - Use DNS requests which reply back to server with more resources than our request so hence our attack amplified

### Stopping DDoS

* Blackholing or Sinkholing - identify attacking IP and reroute to non existent server
* IPS can stop small scale DDoS
* Cloud services like Cloudfare can prevent by Web Filtering and Content Delivery Networks

### Hijacking Types (Modify data)

* Session theft - guess SID enabling them to takeover that session
* TCP/IP hijacking - Take over TCP session without need of cookies (if they can guess next number of sequence in packet transfer)
* Blindjacking - Attacker put some info without seeing the results
* Man in the Browser (MITB) - instead of entire communication, only the browser comms&#x20;
* Watering hole - Attacker place malware in website he know victim will access soon

**Replay Attack (Intercept data an decide if pass through or not)**

**Null Connection -** connection to Windows interprocess comms share (IPC$), helps in info gathering

### DNS Attacks

* DNS posioning - Name resolution modified in DNS cache, mainly seen in internal servers \
  DNSSEC introduced which uses signatures to improve security
* Unauthorized zone transfer - attacker request DNS replica of DNS info of systems so now they have the info, Zone transfer is how DNS servers share info so if not trusted, then bad ppl get
* Altered Host file - Modify host file and make client bypass DNS and redirect to malicious site
* Domain Name Kiting - 5 day grace period for leavng DNS so attacker restart the process and exploit this process so legit ppl can't buy this&#x20;

Pharming - When attacker redirect website traffice to bogus website

Prevent ARP spoofing by proper VLAN segmentization and DHCP snooping



