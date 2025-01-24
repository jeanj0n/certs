# Networking - VPC

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt="" width="563"><figcaption><p>VPC Components Diagram</p></figcaption></figure></div>

All new AWS accounts have a default VPC - new EC2 instances launched into this by default

Max 5 VPCs per region per account\
CIDR min size is /28 and max is /16

### Subnet

5 IP reserved, first 4 and last 1 in each subnet\
• 10.0.0.0 – Network Address \
• 10.0.0.1 – Reserved by AWS for the VPC router \
• 10.0.0.2 – Reserved by AWS for mapping to Amazon-provided DNS \
• 10.0.0.3 – Reserved by AWS for future use \
• 10.0.0.255 – Network Broadcast Address. AWS does not support broadcast in a VPC, therefore the address is reserved

**Internet Gateway** - Resources in VPC connect to internet via this, created separately from VPC, only one VPC attached to one IGW\
IGW don't offer internet access on their own, have to edit the routing tables

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt="" width="520"><figcaption><p>Editing route tables</p></figcaption></figure></div>

**Bastion Hosts** - instance on public subnet used to SSH into private EC2 instances, should allow inbound on port 22

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt="" width="211"><figcaption><p>EC2 sec groups must allow sec group or IP of bastion</p></figcaption></figure></div>

**NAT instance** - outdated, replaced by NAT gateway, an instance/entity used to provide internet comms

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt="" width="209"><figcaption><p>Disable EC2 setting: Source / destination Check</p></figcaption></figure></div>

NAT Gateway - managed by AWS, created in specific AZ using elastic IP upto 100Gbps, can't be used by EC2 in same subnet (common sense) no security groups to manage

<div align="left"><figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt="" width="523"><figcaption><p>NAT Gateway, create multiple NATGW in multiple AZs for fault-tolerance</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt="" width="563"><figcaption><p>Security Groups (stateful) and NACLs (stateless)</p></figcaption></figure></div>

### NACL

Firewall which control traffic from and to subnets, one NACL per subnet

#### NACL Rules

* Rules have a number (1-32766), higher precedence with a lower number
* First rule match will drive the decision
* The last rule is an asterisk (\*) and denies a request in case of no rule match
* Default NACL accepts everything inbound/outbound with subnets its associated with

<div align="left"><figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption><p>Default NACL ruleset</p></figcaption></figure></div>

#### Ephemeral Ports

Port client opens to expect a response after sending request to defined port on server

• IANA & MS Windows 10: 49152 – 65535\
• Many Linux Kernels: 32768 – 60999

<div align="left"><figure><img src="../../.gitbook/assets/image (8) (1).png" alt="" width="541"><figcaption><p>NACL rules for each target subnes CIDR</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt="" width="548"><figcaption><p>Differenciate</p></figcaption></figure></div>

### VPC Peering

Privately connect two VPCs using AWS network, no overlapping CIDR, not transitive in nature

VPCs can be in diff accounts/regions, refer to both via same security group

<div align="left"><figure><img src="../../.gitbook/assets/image (8).png" alt="" width="169"><figcaption><p>Update route tables in each VPC’s subnets for proper comms</p></figcaption></figure></div>

### VPC Endpoints (PrivateLink)

Connect to AWS services using a private network instead of using the public Internet, redundant and horizontal scalability

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt="" width="182"><figcaption><p>Every AWS service is publicly exposed (public URL)</p></figcaption></figure></div>

In case of issues: \
• Check DNS Setting Resolution in your VPC \
• Check Route Tables

Types:

* Interface Endpoints (PrivateLink) - provisions ENI (private IP) as entry point (attach SG to this), supports most AWS services: Paid
* Gateway - provisions gateway and used as target in a route table (no SG), supports both S3 and DynamoDB: Free

For S3, gateway preferred all the time, it's free

Interface Endpoint is preferred access is required from on-premises (Site to Site VPN or Direct Connect), a different VPC or a different region

### VPC Flow Logs

Capture information about IP traffic going into your interfaces: \
• VPC Flow Logs \
• Subnet Flow Logs \
• Elastic Network Interface (ENI) Flow Logs

Monitor and troubleshoot connectivity issues, these logs go to S3, CW logs, Kinesis Data Firehose

Network information from AWS managed interfaces like ELB, RDS, ElastiCache, NATGW, Redshift etc.

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>Flow logs syntax, action due to SG/NACL</p></figcaption></figure></div>

### Site-to-Site VPN

<div align="left"><figure><img src="../../.gitbook/assets/image (6).png" alt="" width="191"><figcaption><p>self explanatory title really, connects two sites</p></figcaption></figure></div>

You need

* VGW (VPN Gateway) - VPN concentrator on AWS side, attached to VPC we want the connection for, can customize the ASN (Autonomous System Number)
* CGW (Customer Gateway) - Software/Physical device on customer side of VPC

**Enable Route Propagation** for the Virtual Private Gateway in the route table that is associated with your subnets

**VPN CloudHub** - setup multiple VPN on same VGW, enable dynamic routing and configure route tables

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1).png" alt="" width="281"><figcaption><p>hub-and-spoke, comms bw multiple sites</p></figcaption></figure></div>

### Direct Connect (DX)

Dedicated private connection from a remote network to your VPC, access both public and private resources - both IPv4 and IPv6

You need the infra already (the connection setup and VGW)

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1).png" alt="" width="512"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1).png" alt="" width="426"><figcaption><p>DC Gateway for multiple VPC in multiple regions</p></figcaption></figure></div>

Types

* Dedicated - 1Gbps,10 Gbps and 100 Gbps capacity: physical ethernet port for customer and request made to AWS and then to AWS Direct Connect Partners
* Hosted - 50Mbps, 500 Mbps, to 10 Gbps: connection made via AWS Direct Connect Partners, can modify the capacity on demand

Lead times (what this mean?) more than 1 month to establish new connection (Imp, questions bout which connection to used based on time interval)

<div align="left"><figure><img src="../../.gitbook/assets/image (4) (1).png" alt="" width="518"><figcaption><p>Resiliency for Direct Connect</p></figcaption></figure></div>

If DX fail, keep Site-to-Site as backup cheapest alternative

## Transit Gateway

For having transitive peering between thousands of VPC and on-premises, hub-and-spoke (star) connection

Share cross-account using Resource Access Manager (RAM), supports IP multicast

<div align="left"><figure><img src="../../.gitbook/assets/image (5) (1).png" alt="" width="205"><figcaption><p>regional, can work cross region</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (6) (1).png" alt="" width="493"><figcaption><p>Share DX bw multiple accounts, above one DX gateway for diff regions watch</p></figcaption></figure></div>

VPC Traffic Mirroring - capture and inspect network traffic in your VPC, route traffic to security appliances we manage

Capture the traffic \
• From (Source) – ENIs \
• To (Targets) – an ENI or a Network Load Balancer

### IPv6

IPv4 cannot be disabled for VPC and subnets, IPv6 is optional (Dual-stack mode)

### Egress GW

For IPv6 only (like NATGW but for IPv6), outwards traffic allowed but inbound blocked\
Have to update route tables

### Network Costs

Keep the traffic within internal AWS network, avoid getting it out in public to minimize cost

Keep the egress traffic low (paid) and the ingress traffic high (usually free)

## AWS Network Firewall

<div align="left"><figure><img src="../../.gitbook/assets/image (7).png" alt="" width="246"><figcaption></figcaption></figure></div>

Protect entire AWS VPC

Layer 3 to Layer 7 protection, you can inspect everything

### Fine Grained Controls

• IP & port - example: 10,000s of IPs filtering \
• Protocol – example: block the SMB protocol for outbound communications\
• Stateful domain list rule groups: only allow outbound traffic to \*.mycorp.com or third-party software       repo \
• General pattern matching using regex

Traffic filtering: Allow, drop, or alert for the traffic that matches the rules

Active flow inspection to protect against network threats with intrusionprevention capabilities (like Gateway Load Balancer, but all managed by AWS)&#x20;

Send logs of rule matches to Amazon S3, CloudWatch Logs, Kinesis Data Firehose

