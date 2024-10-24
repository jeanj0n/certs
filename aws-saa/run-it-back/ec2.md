# EC2

## Elastic Compute Cloud (IaaS)

Consists of

* Virtual Machines (EC2)
* Storing data on virtual drives (EBS)
* Load balancing (ELB)
* Scaling services automatically (ASG)

The public IP may be changed by AWS on every launch of the instance however the private IP will always remain the same

Bootstrap using an EC2 User Data script (only once when you first boot the machine obv)

### Naming Convention of EC2 Instances

<div align="left">

<figure><img src="../../.gitbook/assets/image (27).png" alt="" width="563"><figcaption></figcaption></figure>

</div>

The different optimized types and their workloads - **DO (Pg. 50)**

### Security Groups

Control how traffic is allowed into or out of our EC2 Instances (can be attached to multiple instances)

By default all outbound traffic is ALLOW and all inbound traffic is DENY

Only ALLOW rules can be set, can reference by IP or security group (focus on the instance not the IP)

One security group can reference another

It exists independent of the EC2 so it wouldn't even know a failed connection occured&#x20;

<div align="left">

<figure><img src="../../.gitbook/assets/image (29).png" alt="" width="563"><figcaption></figcaption></figure>

</div>

### Purchasing Options

<div align="left">

<figure><img src="../../.gitbook/assets/image (11).png" alt="" width="563"><figcaption></figcaption></figure>

</div>

**Spot Instances** - Define max spot price and get the instance while current spot price < max

**Spot Fleets** - set of Spot Instances + (optional) On-Demand Instances
