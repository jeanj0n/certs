# EC2 - SAA Level

### Elastic IP (5 per user)

Fixed public IPv4 IP as long as we don't delete it, otherwise it keeps on changing

You can mask internal failures by remapping the IP to another running instance (Not Advised)

Using a load balancer or DNS name mapping is more convenient

### Placement Groups

Define how the EC2 instances are placed and spread throughout the AWS infrastructure&#x20;

* Cluster - All instances are on the same rack - low latency but SPOF
* Spread - Spread across Availability Zones(AZ) but only limited to 7 per placement group
* Partition - Same as spread, but diff instances on same partition depend on diff racks so safe from rack failure

### Elastic Network Interface (ENI)

* Logical component in VPC that represents a virtual network card
* Has a primary private IPv4 and/or secondary IPv4 and an elastic IP per private
* One public IP, one or more security groups and a MAC address
* Create ENI and move them across EC2 for fail over but bound to specific AZ

### EC2 Hibernate

RAM is preserved and dumped into an EBS volume, the root EBS volume must be encrypted

* Not for bare metal instances
* Not for more than 60 days





