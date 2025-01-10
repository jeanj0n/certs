# Networking - VPC

<div align="left"><figure><img src="../../.gitbook/assets/image (3).png" alt="" width="563"><figcaption><p>VPC Components Diagram</p></figcaption></figure></div>

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

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1).png" alt="" width="520"><figcaption><p>Editing route tables</p></figcaption></figure></div>

**Bastion Hosts** - instance on public subnet used to SSH into private EC2 instances, should allow inbound on port 22

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1).png" alt="" width="211"><figcaption><p>EC2 sec groups must allow sec group or IP of bastion</p></figcaption></figure></div>

**NAT instance** - outdated, replaced by NAT gateway, an instance/entity used to provide internet comms

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1).png" alt="" width="209"><figcaption><p>Disable EC2 setting: Source / destination Check</p></figcaption></figure></div>

NAT Gateway - managed by AWS, created in specific AZ using elastic IP upto 100Gbps, can't be used by EC2 in same subnet (common sense) no security groups to manage

<div align="left"><figure><img src="../../.gitbook/assets/image (4).png" alt="" width="523"><figcaption><p>NAT Gateway, create multiple NATGW in multiple AZs for fault-tolerance</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (5).png" alt="" width="563"><figcaption><p>Security Groups (stateful) and NACLs (stateless)</p></figcaption></figure></div>

### NACL

Firewall which control traffic from and to subnets, one NACL per subnet

#### NACL Rules

* Rules have a number (1-32766), higher precedence with a lower number
* First rule match will drive the decision
* The last rule is an asterisk (\*) and denies a request in case of no rule match
* Default NACL accepts everything inbound/outbound with subnets its associated with

<div align="left"><figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption><p>Default NACL ruleset</p></figcaption></figure></div>

#### Ephemeral Ports

Port client opens to expect a response after sending request to defined port on server

• IANA & MS Windows 10: 49152 – 65535\
• Many Linux Kernels: 32768 – 60999

<div align="left"><figure><img src="../../.gitbook/assets/image (8).png" alt="" width="541"><figcaption><p>NACL rules for each target subnes CIDR</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (6).png" alt="" width="548"><figcaption><p>Differenciate</p></figcaption></figure></div>

### VPC Peering

Privately connect two VPCs using AWS network, no overlapping CIDR, not transitive in nature

VPCs can be in diff accounts/regions, refer to both via same security group

<div align="left"><figure><img src="../../.gitbook/assets/image.png" alt="" width="169"><figcaption><p>Update route tables in each VPC’s subnets for proper comms</p></figcaption></figure></div>

### VPC Endpoints (PrivateLink)

Connect to AWS services using a private network instead of using the public Internet, redundant and horizontal scalability

<div align="left"><figure><img src="../../.gitbook/assets/image (1).png" alt="" width="182"><figcaption><p>Every AWS service is publicly exposed (public URL)</p></figcaption></figure></div>

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

<div align="left"><figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Flow logs syntax, action due to SG/NACL</p></figcaption></figure></div>

### Site-to-Site VPN

