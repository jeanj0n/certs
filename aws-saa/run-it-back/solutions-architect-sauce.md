# Solutions Architect Sauce

### 3-Tier architecture for Web Applications

* ELB sticky sessions
* Web clients for storing cookies and making our web app stateless
* ElastiCache
* RDS

### Instantiate Applications Quickly

* Golden AMI: Install your applications, OS dependencies etc. beforehand custom AMI&#x20;
* Bootstrap using User Data

### Elastic Beanstalk

Automatically handles application deployment EC2, ASG, ELB, RDS\
Dev has to only look after application code

<div align="left"><figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption><p>Lifecycle of Beanstalk deployment</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption><p>Client communicate indirectly through SQS Queue, 'Workers' pull messages from queue and scale accordingly</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>
