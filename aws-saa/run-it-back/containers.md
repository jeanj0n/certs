# Containers

* ECS (Elastic Container Service) - Own container platform, proprietary
* EKS (Elastic Kubernetes Service) - Open source
* Fargate - Serverless container platform, works with ECS and EKS
* ECR (Elastic Container Registry) - stores images, can be public/private repo

## ECS

### EC2 Launch Type

**Launch ECS tasks on ECS Clusters** (Task needs a task definition in order to create an instance of it)

Here, we provision the EC2 instances (having **ECS Agent**)&#x20;

### Fargate Launch Type

Serverless, just create task definition and run, AWS looks after compute requirements

### IAM Roles

* EC2 Instance Profile (EC2 Launch type only) - Used by **ECS Agent**, make API call to ECS, send logs to CloudWatch, hit up ECR basically root
* ECS Task Role - Perms to perform given task, diff roles diff tasks defined in task definition

Use Application Load Balancer (ALB), NLB only for high throughput/performance

For persistent data, **mount EFS onto ECS tasks**, works with both launch types. Multi-AZ as well so shared storage for containers.

#### Auto Scaling

Task level - ECS auto scaling based on: CPU, RAM, ALB request per count

* Target tracking (Cloudwatch metric), step scaling (Cloudwatch alarm), scheduled scaling

Instance level - EC2 instances scaled by ECS Cluster Capacity Provider - paired with ASG, auto does it for you

<div align="left"><figure><img src="../../.gitbook/assets/image (203).png" alt="" width="401"><figcaption><p>ECS tasks invoked by Event Bridge</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (204).png" alt="" width="405"><figcaption><p>Identify status change in the task and notify </p></figcaption></figure></div>

## EKS

Cloud-agnostic (Works with any CSP), collect logs using Cloudwatch Container Insights

<div align="left"><figure><img src="../../.gitbook/assets/image (205).png" alt="" width="437"><figcaption><p>EKS diagram</p></figcaption></figure></div>

#### Node Types

* Managed Node groups - Creates nodes for you (EC2 instance) for you, part of ASG, on-demand or spot supported
* Self-managed nodes - You create the odes and register to EKS cluster and managed by ASG, can use pre-built images, on-demand or spot supported
* Fargate - No maintanence required, no nodes managed

#### Data Volumes

Specify **StorageClass** on EKS cluster, uses a **CSI (Container Storage Interface)** compliant driver

Used by EBS, EFS (w Fargate), FSx for Lustre, FSx for NetApp ONTAP

### App Runner

Fully managed service that makes it easy to deploy web applications and APIs at scale.

Just mention source/image and it build everything (ALB, encryption, target, VPC)

<div align="left"><figure><img src="../../.gitbook/assets/image (206).png" alt="" width="133"><figcaption></figcaption></figure></div>
