# Advanced Identity

## Organizations

Manage multiple AWS accounts - Consolidated Billing and shared reserved instances across all accounts

<div align="left"><figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure></div>

### Service Control Policies (SCP)

IAM policies applied to OU or Accounts to restrict Users and Roles, not applicable to management

<div align="left"><figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>SCP Hierarchy</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption><p>ListBucket is bucket level and the rest are object level</p></figcaption></figure></div>

### IAM vs Resource-Based

* When you assume a role (user, application or service), you give up your original permissions and take the permissions assigned to the role whereas resource-based policy no need to give up perms
* Eg. Lambda use a Resource based policy whereas EventBridge use IAM role for Kinesis

### Permission Boundaries

For users and roles (not groups), policy to set the max perms an IAM entity can get

<div align="left"><figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure></div>

## IAM Identity Center

Previously AWS SSO

One login for all AWS accounts in AWS organisations, SAML 2.0 apps, EC2 WIndows instances

Identity providers can be built-in IAM Identity center or 3rd party like AD

<div align="left"><figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption><p>Identity Center fine-grained permissions and assignments</p></figcaption></figure></div>

## AWS Directory Services

* AWS Managed Microsoft AD - r own AD in AWS, manage users locally, supports MF
* AD Connector - proxy to redirect to on-premises AD, support MFA and users managed on the on-premises AD
* Simple AD - Weakest one, AD-compatible managed directory on AWS

Connect to AWS MM AD out of the box - just connect\
For Self-managed directory - use AD connector or two-way trust relationship

<div align="left"><figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure></div>

## Control Tower

Set up and govern a secure and compliant multi-account AWS environment, uses AWS organisations to create accounts

### Guardrails

Ongoing governance for Control Tower

* Preventive Guardrail – using SCPs
* Detective Guardrail – using AWS Config

<div align="left"><figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption><p>Sample Workflow</p></figcaption></figure></div>
