# High Availability & Scalability

* Vertical Scaling: Increase instance size (= scale up / down)
* Horizontal Scaling: Increase number of instances (= scale out / in)
* High Availability: Run instances for the same application across multi AZ

### Load Balancers

Load Balances are servers that forward traffic to multiple servers (e.g., EC2 instances) downstream

An Elastic Load Balancer is a managed load balancer by AWS, they look after deployment and maintenance

**Health Checks** are used know if instances it forwards traffic to are available to reply to requests, done on a port and route

* Application Load Balancer - HTTP, HTTPS, WebSocket
* Network Load Balancer - TCP, TLS, UDP
* Gateway Load Balance - At network level

### Application Load Balancer (ALB)

Load balancing to multiple HTTP applications across machines (target groups) or on the same machine (containers)\
Support for HTTP/2 and redirect HTTP to HTTPS

Possible target groups include

* EC2 instances
* ECS tasks
* Lambda functions
* Private IP addresses
* NLB is NOT POSSIBLE

<div align="left"><figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

Get fixed DNS name so all requests are consistent.\
For app servers to get client IP, use X-Forwarded-For in the header, Port and Proto similarly

### Network Load Balancer (NLB)

To forward TCP and UDP traffic to instances, handle upto millions of requests per second.\
One static IP per AZ, and supports assigning Elastic IP

<div align="left"><figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

### Gateway Load Balancer

Manage a fleet of 3 rd party network virtual appliances in AWS

* Transparent Network Gateway – single entry/exit for all traffic
* Load Balancer – distributes traffic to your virtual appliances
* Uses the GENEVE protocol on port 6081

<div align="left"><figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt="" width="204"><figcaption><p>Target Group can be either EC2 Instances or Private IP addresses</p></figcaption></figure></div>

### Sticky Sessions (ALB & NLB)

Same client is always redirected to the same instance behind a load balancer

Cookie name rules (Application-based)

* Don’t use AWSALB, AWSALBAPP, or AWSALBTG (reserved for use by the ELB)
* Cookie name must be specified individually for each target group
* If not custom, cookie name is AWSALBAPP

Rules for Duration-based

* Cookie generated by the load balancer, name is AWSALB for ALB, AWSELB for CLB

### Cross-Zone Load Balancing

<div align="left"><figure><img src="../../.gitbook/assets/image (12) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

Enabled and disabled by default for ALB and NLB respectively

### SSL Certificates

Load balancer uses an X.509 certificate (SSL/TLS server certificate)\
Manage certificates using ACM (AWS Certificate Manager), make custom certs on your own

**Server Name Indication(SNI)**

Load multiple SSL certificates onto one web server, requires the client to indicate the hostname of the target server in the initial SSL handshake

<div align="left"><figure><img src="../../.gitbook/assets/image (13) (1).png" alt="" width="295"><figcaption></figcaption></figure></div>

### Connection Draining

Gives time to complete “in-flight requests” while the instance is de-registering or unhealthy and stops new new requests from coming\
Between 1 to 3600 seconds (default: 300 seconds), 0 to disable feature

### Auto Scaling Groups (ASG)

Perform elastic scaling of instance deployment depending on the workload&#x20;

<div align="left"><figure><img src="../../.gitbook/assets/image (14) (1).png" alt="" width="207"><figcaption><p>Add Min/Max and desired size capacity</p></figcaption></figure></div>

Scale using CloudWatch alarms, monitor metrics such as CPU usage, requests per minute

Dynamic Scaling Policies

* Target Tracking Scaling - I want the average ASG CPU to stay at around 40%
* Simple / Step Scaling - When alarm is triggered (example CPU > 70%), then add 2 units
* Scheduled Actions - based on usage patterns, increase the min capacity to 10 at 5 pm on Friday

After a scaling activity happens, you are in the cooldown period (default 300 seconds), in this period ASG will not launch or terminate any instances
