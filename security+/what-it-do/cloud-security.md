# Cloud Security

Depends on virtualization to cut costs and be efficient

Hyperconvergence - Full integration of servers, networks and storage

* Secure Enclave - two distinct areas to store and access data from, used in Azure
* Secure Volume - load encrypt and decrypt area of memory trying to read as needed eg. Bitlocker

### As a Service(aaS)

* Software - provide hardware, OS, software, and apps needed for software to be delivered     eg. Office 365
* Infrastrucutre - Provide hardware, OS needed to develop your own service/software
* Platform - Provide hardware and software for service to operate
* Security - outsource sec services as cloud interface

<div align="left"><figure><img src="../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure></div>

#### Virtual Private Cloud (VPC - IaaS)

Private network segment available to single cloud customer within public cloud\
Consumer responsible for IP address allocation and routing in VPC\
Used to provision internet-accessible apps that need to be accessed from geographic sites

#### Cloud Access Security Broker (CASB) \[Middleman for auth]

Enterprise Management software designed to mediate access to cloud services by users across all types of devices

* Forward proxy - leave network for cloud only if comply
* Reverse proxy - proxy at edge of cloud, can leave network always but only enter cloud if comply

API - allow automated admin, management and monitoring of cloud service

FAAS - Function as a Service

Allows to run things without having your own server (**Serverless)**

**Serverless -** runs functions in virtualized containers in cloud instead of dedicated server

Eg. Netflix uses AWS Lambda to run its streaming services

No patching, no admin, no file monitoring

### Cloud Threats

* Insecure API - only over secure channel, sanitize error messages&#x20;
* Improper Key Management - don't hardcode, replace old keys, uses SAML for auth in APIs
* Logging and Monitoring - SaaS may not provide logs
* Unprotected Storage - Access control to cloud storage through IAM policies, container policy and ACL policy
* CORS (Cross Origin Resource Sharing) - Used by CDNs to trust request from nominated domains as safe since they use so many edge points and they need to trust each other, weak policy lead to XSS
