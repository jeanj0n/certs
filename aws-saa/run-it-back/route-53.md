# Route 53

<div align="left"><figure><img src="../../.gitbook/assets/image (169).png" alt="" width="340"><figcaption></figcaption></figure></div>

Highly available, scalable, fully managed and Authoritative DNS (We can modify the records)\
Can check health of resources and provide 100% availability SLA\


* A – maps a hostname to IPv4
* AAAA – maps a hostname to IPv6
* CNAME – maps a hostname to another hostname (target must have A or AAAA, not for non-root domain eg. omething.mydomain.com you need the dot)
* NS – Name Servers for the Hosted Zone

### Hosted Zones

0.50$ per month per hosted zone

* Public Hosted Zones – contains records that specify how to route traffic on the Internet (public domain names)
* Private Hosted Zones – contain records that specify how you route traffic within one or more VPCs (private domain names)

**Except for Alias records, TTL is mandatory for each DNS record**

### Alias

Maps a hostname to an AWS resource\
Similar to CNAME, works for root domains too (Free if charge)

Create Alias record for anything except EC2 DNS instances (works on root domains too)

<div align="left"><figure><img src="../../.gitbook/assets/image (171).png" alt="" width="563"><figcaption></figcaption></figure></div>

### Routing Policies

* Simple - route traffic to a single resource, if multiple then randomly picked (NO health checks)
* Weighted - assign traffic to instance based on weight allotted, 0 for everyone equal, health checks&#x20;
* Latency - based on traffic between users and AWS Regions
* Failover - If the primary health check mess up, switch to the secondary one
* Geolocation - routing is based on user location, create default too (website localization)
* Multi value - like simple where assign multiple but health checks possible so if down taken off
* Geoproximity - to shift more traffic to resources based on the defined bias to a specific location, To expand (1 to 99) & To shrink (-1 to -99)&#x20;
* IP based - provide a list of CIDRs for your clients and the corresponding endpoints/locations

### Health Checks

For public AWS services

* Monitor an endpoint (application, server, other AWS resource)
* Monitor other health checks (Parent child relation, monitor upto 256 children)
* Monitor CloudWatch alarms

**For endpoints** (HTTP, HTTPS and TCP)

Upto 15 global health checkers, Healthy/Unhealthy Threshold – 3 (default)

If > 18% of health checkers report the endpoint is healthy, then Healthy. Otherwise Unhealthy\
Or pass/fail based on the text in the first 5120 bytes of the response\


### Domain Registrar vs DNS Service

Domain Registrar usually provides you with a DNS service to manage your DNS records\
But you can use another DNS service to manage your DNS records

<div align="left"><figure><img src="../../.gitbook/assets/image (170).png" alt="" width="563"><figcaption></figcaption></figure></div>

